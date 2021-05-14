# 面试官：观察过 chrome 调试工具的请求体么？Form Data 和 Request Payload 有什么区别？

<div align="center">
  <image src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b43e351df3e34fbc9be964b98ef64b33~tplv-k3u1fbpfcp-watermark.image" />
</div>

## 前言

这篇文章旨在记录自己解惑过程，比如

1. 在 chrome 调试工具中，`Form Data` 和 `Request Payload` 有什么区别？
2. `application/x-www-form-urlencoded` 和 `application/json` 有什么区别？开发中我们应该怎么选择？
3. 为什么后端有时会无法解析自己发送的数据？
4. 在 `POST` 的跨域请求中，有办法不发送 `OPTIONS` 预检请求也能发送数据的方法么？

话不多说，直接进入主题。

## 发现问题，从两个截图开始

![微信请求](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/81447cfad56545ce8613f6661213001f~tplv-k3u1fbpfcp-watermark.image)

![掘金请求](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e90d54e4287544aeb8118b33b6e52c12~tplv-k3u1fbpfcp-watermark.image)

这两个截图就是写这篇文章的初衷，微信文章在打开的时候是显示的 `Form Data`，第二张图是掘金在打开文章发起的请求，当时看到就特奇怪，`Form Data` 和 `Request Payload` 这俩货有啥区别？为啥都是 `POST` 请求，但却有两种发送数据的方式？

我这个人就是属于碰到这种奇怪的问题不把他搞清楚就睡不了觉的人，我们直接在本地场景重现，好好看看这俩货。

如果不想看中间的分析过程，可以直接点击 [总结](#总结) 看杰伦。

## 场景重现

本地起两个服务，前端和后端，通过创建 `XMLHttpRequest` 对象来进行数据传输，并通过 `setRequestHeader()` 来改变 `Content-Type`，最终我们在调试工具中完美重现了两种模式。

文章里的示例代码都可以从这个仓库里找到，希望自己亲自尝试的小伙伴可以点击查看详情 [示例地址](https://github.com/jsjzh/tiny-codes/tree/demo/study-post-request)。

### Request Payload

如果希望看到 `Request Payload`，需要设置请求头部 `Content-Type: application/json`，再将数据经过 `JSON.stringify` 序列化后发送。

![chrome application/json](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0c5eab5da1704e1fbfd7fc3e115107b9~tplv-k3u1fbpfcp-watermark.image)

> 大家可以看到我这里的 `Origin: http://localhost.charlesproxy.com:3000`，这是因为要用 charles 抓本地包，得用这做一层代理

直接上抓包的截图

![application/json 抓包](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b16ddd6bd31e43bfa32369512eac1600~tplv-k3u1fbpfcp-watermark.image)

上半部分就是一个完整的 http 请求，空行上面为请求头，空行下面是请求体，可以看到我们的请求体就是一个 json 序列化后的字符串。

下半部分，注意 `JSON` 和 `JSON Text` 两个 tab，这个是我们设置了 `Content-Type: application/json` 了之后，charles 自动会给带上的。

后端接到 http 请求后，就是截取空行后的这个请求体解析，因为我们传了 `Content-Type: application/json`，所以后端知道请求体是一个 json 字符串，就可以用 `JSON.parse` 来解析。

发送的数据为

```json
{
  "name": "king",
  "age": 18,
  "isAdmain": true,
  "groups": [1, 2, 3],
  "address": "",
  "foo": null,
  "bar": undefined,
  "extra": { "wechat": "kimimi_king", "qq": 454075623 }
}
```

解析的数据为

```json
{
  "name": "king",
  "age": 18,
  "isAdmain": true,
  "groups": [1, 2, 3],
  "address": "",
  "foo": null,
  "extra": { "wechat": "kimimi_king", "qq": 454075623 }
}
```

可以看到除了 `bar: undefined` 之外，`number`、`boolean` 和 `null`，数据类型都被正确的传输了。

### Form Data

再来说说 `Form Data`，我们需要设置 `Content-Type: application/x-www-form-urlencoded`，再将数据通过 `qs.stringify` 序列化后再发送。

> qs 即为 [qs npm source](https://www.npmjs.com/package/qs)，是一个将数据 querystring 化的库
>
> 可以简单理解成他可以把一个对象转换成类似 get 请求中 ? 后面的查询字段 `key=data&key2=data2`
>
> 如果不经过 qs 处理直接发送，方法会使用 `toString()` 来将数据转为字符串，如果传输的是对象，你会得到 `[object Object]`

![chrome application/x-www-form-urlencoded](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/de34afab1101436db5467c41c656d328~tplv-k3u1fbpfcp-watermark.image)

这里也直接贴出抓包的截图

![application/x-www-form-urlencoded 抓包](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/95322c68a6e74bb19cf8c6fa6a587a3b~tplv-k3u1fbpfcp-watermark.image)

上半部分就是 http 请求，可以看到当我们设置 `Content-Type: application/x-www-form-urlencoded` 请求体也是放在了空行之后。

下半部分，对比刚才的 `application/json` 就能发现不一样的地方了，`JSON` 和 `JSON Text` 的 tab 不见了，取而代之的是 `Form` tab。

后端接到 http 请求之后，也是截取的空行后面的请求体，并使用 `qs.parse` 进行解析。

发送的数据为

```json
{
  "name": "king",
  "age": 18,
  "isAdmain": true,
  "groups": [1, 2, 3],
  "address": "",
  "foo": null,
  "bar": undefined,
  "extra": { "wechat": "kimimi_king", "qq": 454075623 }
}
```

解析的数据为

```json
{
  "name": "king",
  "age": "18",
  "isAdmain": "true",
  "groups": ["1", "2", "3"],
  "address": "",
  "foo": "",
  "extra": { "wechat": "kimimi_king", "qq": "454075623" }
}
```

经过和 `Content-Type: application/json` 对比，我们可以看到，不仅 `number` 和 `boolean` 的数据类型丢失，并且 `foo: null` 还被转换成了 `foo: ""`。

### 交换序列化方式

刚才我们尝试了正确的 `Content-Type` 对应正确的序列化方式

`application/json` + `JSON.stringify`

`application/x-www-form-urlencoded` + `qs.stringify`

但其实我们观察到实际的 http 请求，这两个 `Content-Type` 都是将数据放在空行后传输，所以我们当然也可以交换他们的序列化方式。

#### application/json + qs.stringify

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/81dbc404062e42668dffe095fe172f6d~tplv-k3u1fbpfcp-watermark.image)

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9e863aaa7c0d4d23b94ce9845ef5e70f~tplv-k3u1fbpfcp-watermark.image)

这里直接就说结论，我们设置了 `application/json`，但使用 `qs.stringify` 序列化，结果就是

1. chrome 调试工具的 `Request Payload` 无法解析，遂无法格式化数据
2. charles 工具的 `JSON` 和 `JSON Text` 无法解析
3. 最重要的，后端若是读取了 `Content-Type` 为 `application/json`，就会使用 `JSON.parse` 来解析数据

> 在后端我们当然可以手动用 `qs.parse` 来进行解析，但是我们为什么要给自己埋坑？

#### application/x-www-form-urlencoded + JSON.stringify

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/29304b2cf4f14790a5b8a16d5b7c1f2c~tplv-k3u1fbpfcp-watermark.image)

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b14a8a0003304904be5311369d4414d7~tplv-k3u1fbpfcp-watermark.image)

同理，使用了 `Content-Type` 和不正确的序列化方式，不仅 chrome 和 charles 无法解析，后端也会有疑惑，更重要的是会给自己埋坑。

## 总结

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a6a3d785fca74b81b0983e6c6d986907~tplv-k3u1fbpfcp-watermark.image)

> 诶，没错，我就想皮一下

前面说了这么多，现在来总结一下

1. `Form Data` 和 `Request Payload` 就是因为请求的 `Content-Type` 不同，而不同的解析请求体后的呈现方式
2. `Content-Type` 设置成 `application/json` 还是 `application/x-www-urlencoded` 在 http 请求中，除了 `Header` 以外并无区别，都是将请求体放在空行后

那我们在开发中应该如何选择 `Content-Type`？建议如果不是项目有特别要求，都使用 `application/json`，原因有以下几点

1. 原生自带的 `JSON.stringify` 和 `JSON.parse` 不香么？qs 在前端就有很多实现，比如 `qs` 和 `query-string`，还有 node 自带的 `querystring`
2. `x-www-form-urlencoded` 需要使用配套 `qs.stringify`，**后端解析数据后会丢失数据类型**，比如 `number`、`boolean`、`null`
3. 不同的框架对于 `qs.parse` 的实现方式不同，在项目刚开始对接时可能会有前后端对齐解析方式的操作
4. 前端的 `qs` 仓库默认只能处理 5 层对象，默认只能解析 1000 个参数（当然，这两个配置都可以修改）举一个例子

```json
{
  "a": {
    "b": {
      "c": {
        "d": {
          "e": {
            "f": {
              "g": { "name": "king" }
            }
          }
        }
      }
    }
  }
}
```

因为对象嵌套的层数太深，解析后就成了如下

```json
{
  "a": {
    "b": {
      "c": {
        "d": {
          "e": {
            "[f][g][name]": "king"
          }
        }
      }
    }
  }
}
```

当然，使用了 `application/json` 之后会有些不一样

1. 配置头部 `Content-Type: application/json` 之后就不是简单请求，会发起一个 `Options` 预检请求
2. 后端需要同步配置 `Access-Control-Request-Headers: Content-Type`，允许前端配置 `Content-Type` 头部

当然，再说下去就是 `CORS` 的知识点了，这方面也有很多内容可以掰开细说，我也正在整理这方面的内容，可以小小的期待一下。

## 后语

不知道这篇文章是否给你带来了一些帮助，如果有的话是我的荣幸，在平时碰到问题的时候不妨可以挖的深一点，就像这次的 `Form Data` 和 `Request Payload`，当我们挖掘到 http 请求层面就能发现两者其实并无区别，就是浏览器对于 http 协议的一种封装，而正确的使用 `Content-Type` 就是我们和后端联调的一个约定，也是一个规范。

我们当然可以随意设置 `Content-Type`，但是这就需要和后端进行非必要联调，并且也不方便后续理解维护，所以我们能简单就简单一些，有些框架会自动根据 `Content-Type` 的值来解析请求体，头发已经这么少了，我们就不要强行增加游戏难度了。

## 页脚

代码即人生，我甘之如饴。

> 技术不断在变
> 头脑一直在线
> 前端路漫漫
> 我们下期见
>
> by --- 裤裆三重奏

我在这里 [gayhub@jsjzh](https://github.com/jsjzh) 欢迎大家来找我玩儿。

欢迎小伙伴们直接加我，拉你进群一起搞事情，记得备注一下你是从哪里看到文章的。

<div align="center">
  <image src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/53fb3e16b1f64ebbb8aee73734371257~tplv-k3u1fbpfcp-watermark.image" />
</div>

> ps: 如果图片失效，可以加我 wechat: kimimi_king

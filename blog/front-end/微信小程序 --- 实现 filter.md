![wechat](https://user-gold-cdn.xitu.io/2018/5/28/163a68a18c005d1c?w=200&h=200&f=png&s=5647)
## 前言 ##
最近有在做小程序开发，在开发的过程中碰到一点小问题，描述一下先。

本人在职的公司对于后台获取的 `json` 数据需要做过滤转义的很多，不同的状态码会对应不同的文字，但是在微信小程序中又没有类似 `vue` 中的 `|` 方法进行快速的过滤，看了前人的代码大都是用数据遍历洗数据来实现的，说实话，很麻烦，即使提取了公共方法那也麻烦，总之要洗数据就麻烦（对，我就是这么懒，懒人推动世界发展 =3=）

在翻看小程序的文档的时候，正好看到了 `WXS` 的介绍 [官方文档[点我查看]](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxs/) 一拍脑门，这不就成了么？

## 正文 ##
### WXS 为何物 ###
在上代码之前先简单的介绍一下 `WXS` 是什么，以及和 `javascript` 有什么区别，虽然官方文档中都有，但我认为博客的存在意义就是尽量减少看官们的页面跳转，能够在一个页面说明的问题就不要再跳转，外链应该作为课后拓展的手段。
- `WXS` 介绍
    - 是小程序出的一套脚本语言，用于 `wxml` 模板文件中，在模板文件中可以完成页面的结构。
    - 不依赖于运行时的基础库脚本，可以在所有版本的小程序中运行。
    - `WXS` 中不能调用 `javascript` 中定义的函数或者变量，也不能调用小程序提供的 `API`，他的运行环境和 `javascript` 是隔离的。
    - 小程序的条件渲染和循环渲染对 `WXS` 是无效的，就是说如果 `WXS` 代码包裹在未渲染的代码中，只要渲染的 `wxml` 部分调用了此模块，此段 `WXS` 代码依然会被加载。
    - 由于运行环境的差异，在 `ios` 设备上小程序的 `WXS` 会比 `javascript` **快 2~20 倍**，在 `android` 设备上运行效率无异。
    - 模块想要暴露自己的私有变量和方法，只能通过 `module.exports` 实现。
    - 若在模块中想要引用其他模块，只能通过 `require` 实现。
    - 只能使用 `var` 来定义变量，表现形式和 `javascript` 一样，会有变量提升。
    - `WXS` 模块只能在定义模块的 `wxml` 文件中被访问到，使用 `<include>` 或 `<import>` 时，`WXS` 模块不会被引入到对应的 `wxml` 文件中。
    - **不能使用 `new Date()` 应该使用 `getDate()`**。

### 正确的使用 WXS ###
上代码

首先，新建一个 `config.wxs` 文件，用于存储状态码对应转义后的文字。
```javascript
var orderType = {
  "-1": "type one",
  "0": "type two",
  "1": "type three"
};
module.exports.orderType = orderType;
```
可以看到我们对外暴露变量的时候用的是 `module.exports`，在 `wxs` 文件中只能使用该方法 [官方文档[点我查看]](https://developers.weixin.qq.com/miniprogram/dev/framework/view/wxs/01wxs-module.html) 同样，在引入其他模块的时候，只能使用 `require`。

接着，创建一个 `index.wxs` 文件，用于对外暴露一些过滤的方法。
```javascript
var config = require("./config.wxs");

function _filterOrderType(value) {
  return config.orderType[value.toString()] || "order type undefined"
}
// 时间戳转换成 yyyy-MM-dd HH:mm:ss
function _filterTimestamp(value) {
  // 有些特殊 不能使用 new Date()
  var time = getDate(value);
  var year = time.getFullYear();
  var month = time.getMonth() + 1;
  var date = time.getDate() < 10;
  var hour = time.getHours() < 10;
  var minute = time.getMinutes() < 10;
  var second = time.getSeconds() < 10;
  month = month < 10 ? "0" + month : month;
  date = date < 10 ? "0" + date : date;
  hour = hour < 10 ? "0" + hour : hour;
  minute = minute < 10 ? "0" + minute : minute;
  second = second < 10 ? "0" + second : second;
  return year + "-" + month + "-" + date + " " + hour + ":" + minute + ":" + second;
}

module.exports._filterOrderType = _filterOrderType;
module.exports._filterTimestamp = _filterTimestamp;
```
最后在我们需要进行过滤处理的 `wxml` 页面上引入这个模块，使用即可。
```html
<wxs src="../filter/index.wxs" module="filter" />
<view>{{filter._filterOrderType(item.type)}}</view>
<view>{{filter._filterTimestamp(item.time)}}</view>
```
这里需要注意一下，在 `wxml` 页面上使用模块的时候，需要用一个 `module="filter"` 或者其他的命名来包裹。

## 结语 ##
`WXS` 和 `javascript` 虽然类似，但是还是有一些不同的地方，如果在 `debug` 的时候发现报错了，可以在底下回复或者直接私信我，虽然不能秒回，但是多一个人多一条思路，也许我能给您提供一些别的思路，或者您的问题会给我带来一些新的思考，我正是这么期待着。

**本文可以随意转载，只要附上原文地址即可。**

**如果您认为我的博文对您有所帮助，请不吝赞赏，点赞也是让我动力满满的手段 =3=。**
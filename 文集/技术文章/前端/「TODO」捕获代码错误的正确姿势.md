# 捕获代码错误的正确姿势（一）

<div align="center">
  <image src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d45a010de01a413ab7101a4479b1ad5e~tplv-k3u1fbpfcp-zoom-1.image" />
</div>

## 前言

不知道小伙伴们是否有这样的体验，本地开发项目调试时一切正常，一旦发布到线上就会出现各种奇怪的问题，原因也是多种多样，环境变量不同，宿主环境不同，接口返回的数据格式不同，代码逻辑问题等等。

有些错误在开发和测试环节能被发现，有些错误要到了线上才会被发现，此时傻乎乎的等着用户反馈未免太过被动，所以，一个收集能够捕获代码错误的监控系统就显得尤为重要。

该系列文章目的并不是让大家能够立马打造一个前端代码监控系统，是旨在介绍浏览器环境下捕获代码错误的常用方式，并通过我们所熟知的方式，打造一个前端收集代码错误的插件，最后再简单介绍一下收集到了报错信息，如何快速定位错误。

## 常见错误类型

在开始我们的课题之前，我们首先需要了解一下常见的错误类型。

### 代码书写错误

此类错误是最容易排除的，我们往往只需要借助编辑器的自动检测，或者 `eslint` `tslint` 等三方工具，在开发阶段就能解决。

当然，如果有同学是用文本编辑器书写代码的，请在收下我膝盖的同时，下载一个 vscode 体验更好的代码编写环境。

```js
function foo()
cont bar = "string"
```

### 代码执行错误

这个可以说是平时开发中遇到最多的了，比如下面这个例子，相信有不少同学在使用 `vue` 或者 `react` 时，通过接口获取列表数据，如果后端同学对于空数据没有返回 `[]`，而是返回了 `null` 或者 `undefined`，前端展示就会出现问题。

也有的同学会说，ts 大法好，接口定义香香的（但其实这并不能解决接口返回不一致的问题），而且，若遇到同事类型定义皆 `any` 的情况。。。

```js
undefined.map((item) => console.log(item));
```

其他的错误类型，比如使用了一个未定义的变量，但其实这种错误都可以在开发阶段规避掉。

```js
console.log(helloWorld);
```

### 异步代码 reject

不知道小伙伴们在使用 `promise` 的时候有没有 `catch` `reject` 的习惯，我相信除了我大部分人都是有的（违心），那对于未主动 `catch` 的 `reject` 我们应该如何捕获并处理？

```js
const promise = () =>
  new Promise((resolve, reject) => {
    setTimeout(() => {
      reject(new Error("promise reject"));
    }, 1000);
  });
// 未使用 catch 捕获错误
promise().then(console.log);
```

### 资源加载错误

资源加载错误也是一个令人头秃的问题，如果只是 `image` 那还好，若是重要的 `js`、`css` 资源加载错误。。。

```html
<!-- 你说这个资源能加载？嗯？你有问题，小老弟 -->
<script src="http://ajax.googleapis.com/ajax/libs/jquery/2.0.2/jquery.min.js"></script>
```

## 捕获代码错误

接下来就我们说的几种报错类型来做捕获，有的同学可能会说，哎呀我平时用的是 `vue` `react`，这个这么简单的场景我不可能会犯错的啦。

嘘，憋说话，框架们抛出错误的方式也是用那些简单的方法。

另外，下面说内容都有详细的代码和注释，小伙伴们可以直接 `clone` 代码在本地进行调试，具体说明可以看项目分支的 `README.md`。

[示例地址](https://github.com/jsjzh/tiny-codes/tree/demo/catch-code-error)

```bash
git clone -b demo/catch-code-error https://github.com/jsjzh/tiny-codes.git
```

### 代码执行错误

在平时写代码的时候，如果遇到**可能会执行错误**的逻辑，我们一般会用 `try catch`，包裹，然后单独做处理，但是对于一些意料之外的错误，比如上面的接口返回；还有**异步执行的代码中若发生错误，通过 `try catch` 是无法捕获到错误的**，这个时候，`window.onerror` 就派上用场了。

我们可以这么理解，`try catch` 用来捕获可以预料到的错误，`window.onerror` 用来捕获预料之外的错，也就是兜底策略。

```js
/**
 * message {String}: 错误信息
 * fileName {String}: 发生错误的文件
 * col {Number}: 错误代码的行
 * row {Number}: 错误代码的列
 * error {Error}: Error 对象
 */
window.onerror = (message, fileName, col, row, error) => {
  console.log("message: ", message);
  console.log("fileName: ", fileName);
  console.log("col: ", col);
  console.log("row: ", row);
  console.log("error.name: ", error.name);
  console.log("error.message: ", error.message);
  console.log("error: ", error);
};
```

#### 主动 try catch

下面的代码我们主动 `try catch` 了错误，说明我们知道 `try` 的逻辑里面可能会出错，在 `catch` 里面做了处理，其实这种错误情况有可能是不需要上报的，因为我们已经做了对应的错误处理。

> ps: 若认为仍旧需要上报错误，可以在 `catch` 中增加一段 `throw error` 的逻辑，这样就可以将错误抛出，被 `window.onerror` 捕获。

```js
// 同步执行代码，通过 try catch 可以捕获错误
try {
  console.log(helloWorld);
} catch (error) {
  // 在这里做一些自定义处理
  // ...
  // 若做了自定义处理后，仍旧需要上报错误，增加 throw error
  // throw error;
}
```

#### 意料之外的报错

以下这种情况，我们没有预料到会报错，则报错会直接被 `window.onerror` 捕获。

> ps: 这里在浏览器环境访问了 `process` 对象，这是 `node` 环境才有的全局变量。

```js
// 未使用 try catch 捕获错误
// 说明我们未预料到这段代码可能出错
console.log(process);
```

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/430734b1f20d422984c02b73b8940554~tplv-k3u1fbpfcp-watermark.image)

#### 异步代码执行错误

异步的逻辑代码中若有代码执行错误，我们通过 `try catch` 无法直接捕获到，这个时候有两种办法，第一种是给异步执行的函数包裹 `try catch`，这种我们就不举例了，第二种方法是通过 `window.onerror` 来捕获错误。

```js
const promise = () =>
  new Promise(() => {
    setTimeout(() => {
      console.log(helloWorld);
    }, 1000);
  });

try {
  promise().then();
} catch (error) {
  // 无法捕获到错误 error
  console.log("can't catch error");
}
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f3a8b56d035f41328fec7ed560df67b5~tplv-k3u1fbpfcp-watermark.image)

可以看到上图，错误被 `window.onerror` 给捕获了。

### 异步代码 reject

在说异步代码 `reject` 前，我们得有一个共识，`promise` 的 `catch` 无法捕获到代码执行错误，只有通过 `reject()` 的方式抛出的异常，才会被 `promise.catch` 捕获到，什么意思？看下面代码。

```js
const promise = () =>
  new Promise(() => {
    setTimeout(() => {
      console.log(helloWorld);
    }, 1000);
  });

promise()
  .then()
  // 无法捕获到 helloWorld 的错误
  .catch(() => {
    console.log("can't catch error");
  });
```

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f03d2f89ac7d4480aead302a726ca69a~tplv-k3u1fbpfcp-watermark.image)

那 `promise` 的 `catch` 可以捕获到什么错误呢？答案是通过 `reject` 抛出的错误，如下。

```js
const promise2 = () =>
  new Promise((resolve, reject) => {
    setTimeout(() => {
      // 通过 reject 抛出错误
      reject(new Error("promise reject"));
    }, 1000);
  });

promise2()
  .then()
  .catch((error) => {
    // 能够捕获到错误
    console.log("error.name: ", error.name);
    console.log("error.message: ", error.message);
    console.log("error: ", error);
  });
```

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fed1ebbc972f4f0d8c87fc14781ed0f6~tplv-k3u1fbpfcp-watermark.image)

上面的内容只是为了达成我们之间的共识，接下来我们来说说如何捕获未 `catch` 的 `reject`，通过 `window.onunhandledrejection`。

> ps: 有一点需要提醒的，对于抛出的自定义错误，尽量使用 `new Error(...)`，这样我们不仅可以获取到完整的错误信息，还可以获取到文件名，出错行以及出错列，也就是所谓的堆栈信息。

> ps2: 如果是被 `promise.catch` 捕获的 `reject`，则不会被 `window.onunhandledrejection` 捕获，若仍想将此错误上报，可以通过 `throw error` 的方式，此时将被 `window.onerror` 捕获。

```js
window.onunhandledrejection = (promiseRejectEvent) => {
  // Error 对象，由 reject(new Error()) 生成
  const reason = promiseRejectEvent.reason;
  if (reason) {
    console.log("reason.name: ", reason.name);
    console.log("reason.message: ", reason.message);
    console.log("reason.stack: ", reason.stack);
  }
};

const promise2 = () =>
  new Promise((resolve, reject) => {
    setTimeout(() => {
      // 通过 reject 抛出错误
      reject(new Error("promise reject"));
    }, 1000);
  });

// 未使用 catch，错误将被 window.onunhandledrejection 捕获
promise2().then();
```

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5c2aa11c3e8941eb9011a404677dc293~tplv-k3u1fbpfcp-watermark.image)

### 资源加载错误

对于资源加载错误，不管是 `js`、`css`、`image`，我们都可以通过 `window.addEventListener("error")` 来监听捕获错误。

但是对于使用 `background-image: url(./error.png)` 导致的资源加载错误，以及 `new Image().src = "./error.png"` 导致的资源加载错误，通过该方法无法捕获到。

> ps: 若有小伙伴知道有什么办法可以将这些加载错误捕获到的，还劳烦告诉我，我自测成功后会更新到文章中。

```js
window.addEventListener(
  "error",
  (sourceErrorEvent) => {
    const targetElement =
      sourceErrorEvent.target || sourceErrorEvent.srcElement;
    const url = targetElement.src || targetElement.href;

    console.log("sourceError: ", url);
  },
  true
);
```

```html
<script src="http://ajax.googleapis.com/ajax/libs/jquery/2.0.2/jquery.min.js"></script>
<script src="./error.js"></script>
<link rel="stylesheet" href="./error.css" />
<img src="./error.png" />
```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5aaa40fefa1e4fe8b988ab8cfa9d4ad7~tplv-k3u1fbpfcp-watermark.image)

### 意外的小问题

相信有了上面的三个方案，我们已经能够捕获大部分报错了，但是，在调试的时候发现，当加载的 `js` 资源是跨域的，`window.onerror` 就只会收集到错误信息 `Script error.`，如下图。

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bb98a4896e344495bc652d5447099e5a~tplv-k3u1fbpfcp-watermark.image)

为啥会这样？当然就是浏览器的策略。。。不过我们也有对应的解决办法。

> ps: 关于某些 `html` 标签资源跨域内容可以参见 MDN 的 [crossOrigin](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Attributes/crossorigin) 属性说明。

回到问题，我们只需要做两件事，第一：

给跨域加载的 `js` 资源增加一个 `crossOrigin="anonymous"` 属性

```html
<script src="http://127.0.0.1:7002/index.js" crossorigin="anonymous"></script>
```

第二：

保证跨域资源的服务器设置了 `Access-Control-Allow-Origin: hosts` 头，并且你的浏览器发送 `Origin: host` 在其列表内，嫌麻烦的话，服务端可以直接设置 `Access-Control-Allow-Origin: *`。

> ps: 其实不太建议设置成 `*`，如果你使用了 `cookie`，这会导致 `cookie` 无法正常工作，还需要配套设置一些其他的头部，才能使 `cookie` 生效。  
> 当然，如果资源都是统一做了 CDN 配置的，那可以为静态资源的服务器单独配置头部。

> ps2: 你不需要主动添加 `Origin` 头部，这是浏览器的自发行为

```bash
# Response Header
...
Access-Control-Allow-Origin: http://127.0.0.1:7001
...

# Request Header
...
Origin: http://127.0.0.1:7001
...
```

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/249031f538cd4c0ca633b7a2f018036a~tplv-k3u1fbpfcp-watermark.image)

如上，我们就可以获取到错误的详细信息了。

再小小的强调一下，以上的例子你都可以在如下示例中找到。

[示例地址](https://github.com/jsjzh/tiny-codes/tree/demo/catch-code-error)

```bash
git clone -b demo/catch-code-error https://github.com/jsjzh/tiny-codes.git
```

## 后语

至此，捕获代码错误的正确姿势（一）就结束了，在书写文章的时候了解了许许多多平时不会关注的点，对于神奇的前端代码监控系统也有了自己的一些心得体会。

当然，有的同学要说了，sentry 不香嘛？为啥要费时费力自己瞎折腾？关于这点我认为，如果我们只是为了解决问题，那现成的方案拿来用自然是最好的。~~但不折腾的话和咸鱼有啥区别？（逃~~

文章后续还有两个章节，第二章我会打造一个专门收集前端错误信息的插件，第三章我会对于现在很常见的代码压缩后如何定位问题提供一些自己不太成熟的方案。

另，文笔拙劣，方案幼稚，看官们若有任何意见或建议，欢迎在评论区留言，我们一起讨论讨论。

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

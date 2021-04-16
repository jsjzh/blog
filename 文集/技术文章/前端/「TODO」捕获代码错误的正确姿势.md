# 捕获代码错误的正确姿势（一）

<div align="center">
  <image src="https://i.loli.net/2020/04/03/2ytMJEfTCxbjR4H.jpg" />
</div>

## 前言

不知道同学们是否有这样的体验，项目本地开发调试时候一切正常，但是一旦发布到现在就会出现各种奇怪的问题，原因也是多种多样，宿主环境不同，接口返回的数据格式不同，代码逻辑问题等等。

有些错误在测试环节能被发现，有些错误甚至要到了线上才会被发现，此时傻乎乎的等着用户反馈未免太过被动，所以，一个收集项目代码错误的方案策略就显得尤为重要。

系列文章旨在介绍浏览器环境下前端收集代码错误的方法，打造一个错误收集的插件，如何快速定位压缩过的代码错误位置等等。

## 常见错误类型

在开始我们的课题之前，我们首先需要了解一下常见的错误类型。

### 代码书写错误

此类错误是最容易排除的，往往只需要借助编辑器的自动检测，或者 `eslint` `tslint` 等三方工具，在开发阶段就能解决。

如果有同学是用文本编辑器书写代码的，请在收下我膝盖的同时，下载一个 vscode 体验更好的代码编写环境。

```js
function foo()
cont bar = "string"
```

### 代码执行错误

这个可以说是平时开发中遇到最多的了，比如下面这个例子，相信有不少同学在使用 `vue` 或者 `react` 时，通过接口获取数据，如果后端同学对于空数据没有返回 `[]`，而是返回了 `null` 或者 `undefined`，就会碰到这种令人头秃的情况。

也有的同学会说，ts 大法好，接口定义香香的（但其实并不能解决接口返回不一致的问题），但若遇到同事类型定义皆 `any` 的情况。。。

```js
undefined.map(console.log);
```

若使用了一个未定义的变量，比如下面的例子，但是一般这种错误都可以在开发阶段规避掉。

```js
console.log(helloWorld);
```

### 异步代码 `reject`

不知道同学们在使用 `promise` 的时候有没有 `catch` `reject` 的习惯，我相信除了我大部分人都是有的（违心），那对于未主动 `catch` 的 `reject` 我们应该如何捕获并处理？

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

另外，下面说内容都有详细的代码和注释，同学们可以直接 `clone` 代码在本地进行调试，具体的说明可以看项目的分支的 `README.md`。

[示例地址](https://github.com/jsjzh/tiny-codes/tree/demo/catch-code-error)

```bash
git clone -b demo/catch-code-error https://github.com/jsjzh/tiny-codes.git
```

### 代码执行错误

在平时写代码的时候，如果遇到**可能会执行错误**的逻辑，我们一般会用 `try catch`，包裹，然后单独做处理，但是对于一些意料之外的错误，比如上面的接口返回；还有异步执行的代码中若发生错误，通过 `try catch` 是无法捕获到错误的，这个时候，`window.onerror` 就派上用场了。

简单说一下他们俩的区别，`try catch` 用来捕获可以预料到的错误，`window.onerror` 用来捕获预料之外的错。

```js
/**
 * message {String}: 错误信息
 * fileName {String}: 发生错误的文件
 * col {Number}: 错误代码的行
 * row {Number}: 错误代码的列
 * error {Error}: Error 对象
 */
window.onerror = (message, fileName, col, row, error) => {
  console.log("message", message);
  console.log("fileName", fileName);
  console.log("col", col);
  console.log("row", row);
  console.log("error.name", error.name);
  console.log("error.message", error.message);
  console.log("error", error);
};
```

#### 主动 `try catch` 代码

下面的代码我们主动 `try catch` 了错误，说明我们知道 `try` 的逻辑里面可能会出错，在 `catch` 里面做了处理，那其实这种错误情况可能是不需要上报的，因为我们已经做了对应的错误处理。

> ps: 若认为仍旧需要上报错误，可以在 `catch` 中增加一段 `throw error` 的逻辑，这样就可以将错误冒泡至 `window`，然后被 `window.onerror` 捕获。

```js
// 同步代码，通过 try catch 可以捕获错误
try {
  console.log(helloWorld);
} catch (error) {
  // 在这里做一些自定义处理
  // ...
  // 若做了自定义处理后，仍旧需要上报错误
  // 增加 throw error
  // throw error;
}
```

#### 意料之外的报错

以下这种情况，就是我们没有预料到会报错，则报错会直接被 `window.onerror` 捕获。

> ps: 这里在浏览器环境访问了 `process` 对象，这是 `node` 环境才有的全局变量。

```js
// 未使用 try catch 捕获错误
// 说明我们未预料到这段代码可能出错
console.log(process);
```

#### 异步代码执行错误

异步的逻辑代码中若有代码执行错误，我们通过 `try catch` 无法直接捕获到，这个时候只能通过 `window.onerror` 来捕获错误。

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
}
```

### 异步代码 `reject`

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

那 `promise` 的 `catch` 可以捕获到什么错误呢？通过 `reject` 展示的错误，如下。

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
    // error.message === 'promise reject'
    console.log(error.message);
  });
```

上面的内容只是为了达成我们之间的共识，接下来我们来说说如何捕获未 `catch` 的 `reject`，就是通过 `window.onunhandledrejection`。

> ps: 有一点需要提醒的，对于抛出的自定义错误，尽量使用 `new Error(...)`，这样我们不仅可以获取到完整的错误信息，还可以获取到文件名，出错行以及出错列。

> ps2: 如果是被 `promise.catch` 捕获的 `reject`，则不会被 `window.onunhandledrejection` 捕获，若仍想将此错误上报，可以通过 `throw error` 的方式，此时将被 `window.onerror` 捕获。

```js
window.onunhandledrejection = (promiseRejectEvent) => {
  // Error 对象，由 reject(new Error()) 生成
  const reason = promiseRejectEvent.reason;
  if (reason) {
    console.log(reason.name);
    console.log(reason.message);
    console.log(reason.stack);
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

### 资源加载错误

对于资源加载错误，不管是 `js`、`css`、`image`，我们都可以通过 `window.addEventListener("error")` 来监听捕获错误。

但是对于使用 `background-image: url(./error.png)` 导致的资源加载错误，以及 `new Image().src = "./error.png"` 导致的资源加载错误，通过该方法无法捕获到。

> ps: 若有小伙伴知道有什么办法可以讲这些加载错误兜底的，还劳烦告诉我，我自测成功后会更新到文章中。

```js
window.addEventListener(
  "error",
  (sourceErrorEvent) => {
    const targetElement =
      sourceErrorEvent.target || sourceErrorEvent.srcElement;
    const url = targetElement.src || targetElement.href;

    console.log("sourceError", url);
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

### 意外的小问题

相信有了上面的三个方案，我们已经能够捕获大部分报错了，但是，在调试的时候发现，当加载的 `js` 资源是跨域的，就只会报错 `script error.`。

为啥会这样？浏览器的策略。。。我们也有对应的解决办法。

这方面的文档可以参见 MDN 对于 `script` 标签的 [`crossOrigin`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Attributes/crossorigin) 的属性说明。

我们需要做两件事，第一：

给跨域加载的 `js` 资源增加一个 crossOrigin="anonymous" 属性

```html
<script src="http://127.0.0.1:7002/index.js" crossorigin="anonymous"></script>
```

第二：

保证跨域资源的服务器设置了 `Access-Control-Allow-Origin: hosts` 头，并且保证你的浏览器发送 `Origin: host` 在其列表内，嫌麻烦的服务端可以直接设置 `Access-Control-Allow-Origin: *`.

> ps: 你不需要主动添加 `Origin` 头部，这是浏览器的自动行为

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

再小小的强调一下，以上的例子你都可以在如下示例中找到。

[示例地址](https://github.com/jsjzh/tiny-codes/tree/demo/catch-code-error)

```bash
git clone -b demo/catch-code-error https://github.com/jsjzh/tiny-codes.git
```

## 后语

至此，捕获代码错误的正确姿势（一）就结束了，在书写文章的时候了解了许许多多平时不会关注的点，对于神奇的前端代码监控系统也有了自己的心得体会，这里先吹个牛，在系列文章完结之后，每个人都可以打造一个属于自己的前端代码监控系统。

当然，有的同学要说了，sentry 不香嘛？为啥要费时费力自己瞎折腾？关于这点我认为，如果我们的目标只是为了解决问题，那现成的方案拿来用自然是最好的，但既然都做前端了，不折腾的话和咸鱼有啥区别？（逃

文章后续还有两个章节，第一章我会打造一个专门收集前端错误信息的插件，第二章我会对于现在很常见的前端代码压缩后如何定位问题提供一些自己不成熟的方案。

另，文笔拙劣，方案幼稚，若看官们有任何意见或建议，欢迎在评论区留言，我们一起讨论讨论。

## 页脚

> ps: 许久没有写文章了，有些手生

代码即人生，我甘之如饴。

> 技术不断在变
> 头脑一直在线
> 前端路漫漫
> 我们下期见
>
> by --- 裤裆三重奏

我在这里 [gayhub@jsjzh](https://github.com/jsjzh) 欢迎大家来找我玩儿。

欢迎小伙伴们直接加我，拉你进群一起学习前端呀，记得备注一下你是从哪里看到我的文章的哦～

<div align="center">
  <image src="https://i.loli.net/2019/03/11/5c867208cc9c0.jpg" />
</div>

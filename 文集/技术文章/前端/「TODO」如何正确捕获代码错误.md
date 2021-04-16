# 捕获代码错误的正确姿势

<div align="center">
  <image src="https://i.loli.net/2020/04/03/2ytMJEfTCxbjR4H.jpg" />
</div>

## 大纲「待删」

## 前言

不知道同学们是否有这样的体验，项目本地开发调试时候一切正常，但是一旦发布到现在就会出现各种奇怪的问题，原因也是多种多样，宿主环境不同，接口返回的数据格式不同，代码逻辑问题等等。

有些错误在测试环节能被发现，有些错误甚至要到了线上才会被发现，此时傻乎乎的等着用户反馈未免太过被动，所以，一个收集项目代码错误的方案策略就显得尤为重要。

系列文章旨在介绍浏览器环境下前端收集代码错误的方法，打造一个错误收集的插件，如何快速定位压缩过的代码错误位置等等。

## 错误类型

资源加载出错
同步代码出错
异步代码出错

## 后语

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

```md
# cors

https://developer.mozilla.org/zh-CN/docs/Web/HTML/Attributes/crossorigin

# 脚本错误捕获

## 错误类型

async 异步错误

拼写错误

undefined.map(() => {})

## 如何捕获

try catch

window.onerror

## 包装一个捕获插件

try catch 直接 throw error，让 window.onerror 捕获

对于 window.onerror 增设一个 temp

onLine 为 false，可以先缓存等 onLine 为 true 统一上报，可以把数据先存在 localStroage 中，等下次登录，有网络的时候统一上报

window.ononline

window.onoffline

上报的时候需要应用的版本信息

cookieEnabled

onLine

[connection](https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/connection)

如果是混合应用，可以获取更多的信息，想象空间巨大

## 发现问题

### 跨域无法正常捕获错误

html5 crossorigin

### 若是压缩过的文件，报错信息无法定位错误所在

source-map

### 无法捕获 script image dom 元素的插入报错

## 其他

发现在 script 中设置 id，然后能在里面访问到这个元素，比如设置 id="demo"
在 demo.js 中可以直接获取到 demo，并且 demo 就等于 script 这个元素

react 和 vue2.x vue3.x 的错误如何捕获

fetch 请求如何捕获错误

create-react-app 报错之后，出现一个 iframe，点击报错的方块，会直接跳转到 vscode 对饮的代码位置，如何实现

无法捕获 script image 等资源加载失败的错误
```

# 你值得拥有更好的，lerna 了解一下

## 前言

相信到了 0202 年，大家或多或少都了解过 lerna，毕竟各大框架都是使用这个工具管理源码的，vue、react、babel 等等。

平时的工作中经常会用到 lerna，但是发现新入职的同学还不知道这玩意儿怎么用，就干脆结合实际场景，出一个 lerna 最佳实践，希望看完文章的你对 lerna 又有新的理解。

## 正文

首先来说一下 lerna 最适合的应用场景，当你有很多包是相互依赖的，并且各个包都是可以单独发布使用的，当其中一个模块更新，另外一个模块必须要升级自己的模块依赖，而且每个包都需要单独发布，有一说一，换我我是不愿意的，太麻烦了。
当然，也不是说一定要互相依赖的包才可以用 lerna，独立的包也可以，但是可以借助 lerna 进行统一 publish。

### 安装指令

直接将 lerna 全局安装，相信我，lerna 绝对值得在你的 global node_modules 占领一席之地。

```bash
npm install -g lerna
```

### 介绍指令

### 从一个例子开始

关于这个工具我想多说一些，博主不才，说不来太高深的，也记不住他所有的 API，我们就创建一个应用场景，以此来推进内容。

应用场景：

写一个命令行爬虫类库，有两个爬虫，还有爬虫通用工具类库

两个爬虫可以单独安装运行，也可以通过主爬虫来通过命令行运行

这样就分为下面四个库

1. allCrawler
   1. ACrawler
   2. BCrawler
2. crawlerShared
3. ACrawler
4. BCrawler

以下是会涉及到的内容

```bash
lerna init
lerna create
lerna.json
root package.json scripts
root package.json devdep
lerna link
lerna changed
lerna diff
lerna publish
lerna bootstrap
lerna clean
```

## 后语

「TODO」

## 大纲

「TODO」

## 页脚

代码即人生，我甘之如饴。

```
技术不断在变
头脑一直在线
前端路漫漫
我们下期见
```

我在这里 [gayhub@jsjzh](https://github.com/jsjzh) 欢迎大家来找我玩儿。

欢迎小伙伴们直接加我，拉你进群一起学习前端呀，记得备注一下你是从哪里看到我的文章的哦～

![wechat](https://i.loli.net/2019/03/11/5c867208cc9c0.jpg)

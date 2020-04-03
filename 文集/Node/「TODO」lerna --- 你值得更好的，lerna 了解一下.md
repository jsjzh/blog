# 标题

## 小剧场

「TODO」

## 前言

「TODO」

## 正文

重磅介绍的好工具，当你有很多包是互相依赖的（甚至没有包互相依赖的情况，这个工具也可以给你一个真香警告），重复的 publish 会让人崩溃，用这个工具，可以解放你的双手。

```bash
npm install -g lerna
```

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

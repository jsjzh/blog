# npm 实用知识大合集

<div align="center">
  <image src="https://i.loli.net/2020/04/03/2ytMJEfTCxbjR4H.jpg" />
</div>

## 前言

npm 作为前端一大利器，那必须是要好好掌握，在平时的开发中，用的最多的应该就是 `npm install`，不过，这么强大的工具，作用肯定不止如此。

现将自己所知道的有关 npm 的知识给整理出来，大都是平时用的很多的，整合出来不仅是方便查找，更重要的是身为社会主义的接班人，少先队员所应具备的良好品质也在时刻提醒我，要为社会作出应有的贡献。写到这，我不禁低头一看，胸口的红领巾好像更红了，太阳公公好像也在向我微笑点头。

在开始前还要再说一下，合集会持续更新，因为知识总是不断在补充的。但也不会频繁更新，一般会攒个大再放，**持续更新，建议收藏**，文章会放在我的 blog 文集里，blog 里面还是会快速更新的啦。

再多嘴一句，windows 的同学可以试试 windows 的命令行神器 [cmder](https://cmder.net/)，这可以减少很多使用 windows 原生命令行时奇怪的错误。

## 命令合集

强烈建议最好不要使用 cnpm，会有各种奇怪的 bug，相对 cnpm，给 npm 设置 registry 是一个好选择，**使用 nrm 是更好的选择**。

### `npm init` 创建基础 package.json

```bash
# 会用命令行交互式完成创建
npm init
# 直接略过交互式，使用 defaultValue 创建 package.json
npm init -y
```

### node 运行时内存溢出

在运行内存比较小的电脑上可能你会碰到内存溢出导致程序被 kill 的场景，使用 `--max-old-space-size` 或许可以解决这个问题。

```json
{
  "scripts": {
    "start": "node --max-old-space-size=4076 index.js"
  }
}
```

当然，如果说 package.json 中 scripts 中的命令很多，你就需要在每个地方增加该指令，已经有 issue 提到了该问题，或许在 node 之后的版本会解决这个问题。

[Add max-old-space-size to npmrc](https://github.com/nodejs/help/issues/1783)

再者，我们不仅可以在 scripts 中添加，还可以在执行命令中添加，如下，一个命令行类库的 bin 目录下的执行文件。

```js
#!/usr/bin/env node --max-old-space-size=4096

const cli = require("../src").default;
cli.run();
```

### `npm publish` 发布 npm 包

这里只列出发布的步骤，具体怎么写 npm 包可以到网上搜搜相关内容，各路大佬已经整理了不少，博主就不在此班门弄斧了。

需要注意的是，**我们需要先把 registry 切换到 npm 起始源**，而不能使用 taobao 的，taobao 是同步了 npm 包的国区资源，实际资源还是来自 npm，所以需要先发布到 npm 再等 taobao 自动同步（顺便可以刷一波下载量）。

```bash
# 切换 registry 至 npm
nrm use npm

# 第一次使用 npm publish
# 如果你是第一次使用 npm publish，你需要添加账号
npm adduser

# 非第一次使用 npm publish
# 登陆账户，这一步其实也可以不用做，直接看 whoami 即可
npm login

# 查看当前账户
npm whoami
# 发布
npm publish
```

### `npm view` 查看某一库详细信息

对打了 tag 的库还是挺方便的，可以查看对应 tag 名称，如果需要安装某一个具体 tag 的库，可以使用 `npm install ${packageName}@${tag}`

```bash
npm view @vue/cli

# 查看 view 之后，安装指定 tag
npm install -D @vue/cli@next
```

### `npm root` 获取当前项目的 node_modules 路径

可以在命令行类库中使用，会向上查找 node_modules 的路径，并返回绝对路径。

但是也是有 bug 的，如果你在空文件夹下使用 `npm root`，会直接以当前路径加上 node_modules 返回，**即使你的目录下并没有 node_modules 文件夹**。

```bash
npm root
```

举个例子，如果在很深的路径中，需要使用当前项目依赖的 `node_modules/.bin` 中的指令，我们有两个办法找到目录

```js
const moduleRootPath = path.join(process.cwd(), "node_modules");
```

```js
const moduleRootPath = execa.sync("npm", ["root"]);
```

### `npm list` 查看全局安装的模块

```bash
npm list -g --depth=0
```

### 删除全局安装的所有模块

**慎用，除非你知道你在做什么，否则不要用**。

```bash
rm -rf /usr/local/lib/node_modules
```

### `npm cache` 清除 npm 缓存

报错 `npm resource busy or locked` 时候，或者其他莫名报错的时候，可以试试看。

```bash
# force 表强制清除缓存
npm cache clean --force
```

## 好库推荐

排名不分先后，会按照博主的记忆顺序来记录。其中有命令行工具，还有好用的三方库推荐，大抵不会有主流三大框架的三方库（你们懂得肯定比我多），会以 node 类库为主。

### yarn 依赖处理工具

这么好用的东西不说太多了，大部分人应该都用着，对标 npm，可以更好的处理项目依赖（但是也发现有情况是 npm 可以安装成功，yarn 安装反而报错）。

甩上命令，一个字就是冲。

```bash
npm install yarn -g

# 安装 devdep 依赖
yarn add -D @types/node
# 安装 dep 依赖
yarn add axios
# 安装 peerDep 依赖
# peerDep 依赖意思是，如果你安装了我，那你最要也安装 XXX
yarn add -P vue
# 全局安装依赖
yarn global add @vue/cli
```

### mirror-config-china 自动配置国区镜像

在下载某些库的时候发现即使配置了 registry 的时候，下载还是异常缓慢，甚至半天都不动一下？你可能需要这个。

这个库会自动配置很多第三方库（例如 electron）的地址到国区镜像，虽然也可以在需要的时候手动配置，但是，有一键操作那还要啥自行车！下载速度嗖嗖嗖。

```bash
npm install -g mirror-config-china
```

### nrm 管理 registry

当你经常需要切换 registry 源（比如你需要 `npm publish` 代码，这时候就需要切换到 npm 起始源），这个工具可以帮忙。

```bash
npm install -g nrm

# 添加 npm registry 至 nrm
nrm add npm http://registry.npmjs.org
# 添加 taobao registry 至 nrm
nrm add taobao https://registry.npm.taobao.org
# 使用 taobao 的 registry
nrm use taobao
# 使用 npm 的 registry
nrm use npm
```

### http-server 快起静态服务

可以很方便的在本地起一个静态服务，当然还有很多参数，这里就不多做演示，只做个抛砖引玉，具体可以查看以下文档。

[http-server](https://github.com/http-party/http-server#readme)

```bash
npm install -g http-server
```

举个小例子，当你兴高采烈开发完一个项目打包至 dist，想要看看效果如何，你就可以进入 dist 文件夹，然后启动 `http-server` 服务，他会默认以 `index.html` 作为入口启动静态服务，并且还会监听文件改变。

```bash
cd dist
http-server
```

### nvm 管理多版本 node

虽然可能不多见，但是你还是会碰到需要在同一台电脑上运行不同版本的 node 这种问题，这个库可以解决。

[nvm](https://github.com/nvm-sh/nvm#install--update-script)

```bash
# 查看当前使用的 node 版本
nvm current
# 安装 node 指定版本
nvm install 12.14.0
# 切换 node 版本
nvm use 12.14.0
# 使用 12.14.0 版本的 node 运行 index.js
nvm run 12.14.0 index.js
```

<!-- ### @oishi/cli 创建命令行

硬广来啦～这个是博主写的一个库，可以快速创建 node 命令行类库，具体可以参见以下文档，另外，在安装过程中命令行也有操作提示，方便的话给个 star 就更好啦～低头致谢。

[oishi](https://github.com/jsjzh/oishi)

```bash
npm install -g @oishi/cli

# 创建基础命令行类库
oishi create:cli hello say
# 创建基础 ts 项目
oishi create:ts my-ts-app
```

### lerna 集合包管理工具

重磅介绍的好工具，当你有很多包是互相依赖的（甚至没有包互相依赖的情况，这个工具也可以给你一个真香警告），重复的 publish 会让人崩溃，用这个工具，它可以解放你的双手。

```bash
npm install -g lerna
```

关于这个工具博主是想好好说说的，但是限于篇幅，并且这篇 blog 只是介绍向，不是指导向，就不在此赘言，这里就不说太多了，甩出我另一篇 blog 的链接，强烈建议大家学习该工具。

「TODO 放上 lerna 学习的链接」 -->

## 后语

最近头发的脱落速度和瑞幸咖啡的下跌速度类似，忘性也越来越大，经常会代码写着写着就忘了刚才我要做什么，对于一件事情以前能够保持四五个小时的专注，现在思维很容易打岔，想着，啊是不是年纪大了，以前从来没有对岁数有过恐慌，但是看着公司里优秀的新生代，不免会有些焦虑。如何才能不焦虑？不是废寝忘食的工作，而是应该让自己成为不可替代的那个人，感情是如此，工作亦是如此。

这里不是蹭热点，司徒正美大大的消息想必大家或多或少都了解了一些，惋惜，说是天才的陨落也不为过。所以大家在平时的开发学习中，除了要关注技术细节，更要多多关注自己的身体。赚钱的机会有很多，但唯有健康的身体才能支撑起你伟大的理想。

ps: 写完之后发现自己了解的也不多，写的挺杂的，如果各位大佬有私货可以偷偷摸摸分享给我，我琢磨琢磨整理整理给加上去，蟹蟹。

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

## 待办

- npm scope
- lerna 学习
- @oishi/cli
- npm version

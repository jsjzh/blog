# 我就不信这些 npm 指令你全知道

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

### `npm cache` 清除 npm 缓存

报错 `npm resource busy or locked` 时候，或者其他莫名报错的时候，可以试试看。

```bash
# force 表强制清除缓存
npm cache clean --force
```

### `npm version` 快速更改项目版本

如何正确的修改项目的版本号？手动修改是不是有点 low？下面教你如何正确的修改版本号。

上面我们提到过 npm 的版本规则，这里重新提一下 `major.minor.patch-pre`，后面多了个 pre，我们把它当做临时版本的意思，没有在上面提就是因为临时版本用的很少。

```bash
# 获取当前项目的依赖版本
npm version

# 修改当前项目版本为 1.1.5
npm version 1.1.5
```

如下是正确修改版本的姿势，需要注意的是 `npm version prerelease`，这个是修改递增临时版本的指令。

```bash
# 升级主版本 1.1.5 -> 2.0.0
npm version major
# 升级次版本 1.1.5 -> 1.2.0
npm version minor
# 升级补丁版本 1.1.5 -> 1.1.6
npm version patch

# 升级主版本的临时版本 1.1.5 -> 2.0.0-0
npm version premajor
# 升级次的临时版本 1.1.5 -> 1.2.0-0
npm version preminor
# 升级补丁版本的临时版本 1.1.5 -> 1.1.6-0
npm version prepatch

# 你以为重新执行 npm version pre* 就可以递增临时版本号了？
# 并不是，所有 pre* 临时版本通过如下指令来递增
npm version prerelease
```

提一句，yarn 也可以用指令更改版本，只不过你需要使用 `yarn version --new-version patch` 而已。

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

### npm scope \@ 是什么？

安装依赖的时候发现依赖有前缀 `@`？这个是 npm 的 scope 机制，可以理解成组织，比如 `@types/node`、`@vue/cli`，前面的 `@` 都是组织，只有包管理者才可以把包上传到对应的 scope 下。如果你想发布 `@types/hello` 是不行的，因为你没有 `@types` 的权限。

那如何发布带 `@` 前缀的项目呢？你必须先在 npm 管理界面申请对应的 `@${scope}` 才可，如下图。

![申请 scope](https://i.loli.net/2020/04/07/ovZtp26dA8uFIn9.png)

### npm 版本号说明

先来说一个通识，npm 的版本号规则是 `major.minor.patch`，代表的是主版本、次版本、补丁版本，当发布的时候，我们需要遵从如下原则。

1. 如果只是修复 bug，则更新 `patch`。
2. 如果新增功能，但是向下兼容，则更新 `minor`。
3. 如果有大变动，向下不兼容，需更新 `major`。

那些一看就知道啥意思的在这里就不说了，比如 **1.1.0**、 **>1.1.0**、 **>=1.1.0**、 **<1.1.0**、 **<=1.1.0**、**1.1.x**、**1.1.\***，来说说比较特殊的。

**~1.1.0**

`~1.1.0` 可以匹配到 `1.1.n`

`~1.1` 可以匹配到 `1.1.n`

`~1` 可以匹配到 `1.n.n`

**^1.1.0**

从版本号中最左边非 0 的数字开始，右边的数字可以是任意。

`^1.1.0` 可以匹配到 `1.n.n`

`^0.1.0` 可以匹配到 `0.1.n`

**latest**

latest 是一个特殊的版本号，上面我们有提到 `npm view`，大家可以试试 `npm view @vue/cli`，可以看到 `dist-tags` 有两个，`latest` 和 `next`，如果发布包的时候我们不指定 tag，默认发布的都是 `latest`。

并且，如果我们如果把依赖版本设置为 `latest`，这就代表我们每次都会安装 `laetst` 最新的版本。

但是 `next` 是怎么出现的呢？我们可以在 `npm publish` 的时候 `npm publish --tag next` 即可，这样子发布项目就会多一个 tag。

在安装的时候，除非特别指定 `npm install @vue/cli@next`，否则是不会走到该 tag 下。

### npm scripts hook 脚本钩子

npm 的 hook 可以简单分为两种，pre 和 post，pre 代表在指令之前执行，post 代表在指令之后执行。

npm 内置的指令一共有如下几种 `start`、 `stop`、 `test`、 `restart`、 `version`、 `install`、 `publish`，内置指令是什么意思？就是可以直接通过 `npm start`，而不是 `npm run start` 执行的。

**`start`、 `stop`、 `test`、 `restart`、 `version`**

把这里几个拎出来讲，因为这几个的指令都差不多，前面加个 pre 或者 post 而已，大家可以复制下面的指令到你的 package.json 里面，然后运行对应的 `npm start`、`npm test`、`npm stop` 等，试试。

需要注意的是，`npm version` 和 `npm run version` 是不一样的，`npm version` 我们前面提到过是可以设置版本的，而 `npm run version` 只是单纯的运行指令而已。

```json
{
  "prestart": "echo prestart",
  "start": "echo start",
  "poststart": "echo poststart",

  "prestop": "echo prestop",
  "stop": "echo stop",
  "poststop": "echo poststop",

  "pretest": "echo pretest",
  "test": "echo test",
  "posttest": "echo posttest",

  "prerestart": "echo prerestart",
  "restart": "echo restart",
  "postrestart": "echo postrestart",

  "preversion": "echo preversion",
  "version": "echo version",
  "postversion": "echo postversion"
}
```

上面的脑中过一下就行了，这里还有比较特殊的用法，可以自定义指令，并且增加 pre 和 post 前缀。

当然，这里运行就不能使用 `npm hello` 而要使用 `npm run hello` 了。

```json
{
  "prehello": "echo prehello",
  "hello": "echo hello",
  "posthello": "echo posthello"
}
```

**`install`、 `publish`**

再来说两个比较特殊的指令，`npm install` 和 `npm publish`，这两个除了拥有 pre 和 post 的前缀，还有几个隐藏的 hook。

先来说一下 `npm install`，如下可以看到除了 `preinstall` 和 `postinstall` 之外还执行了 `prepublish` 和 `prepare`，但是这里只给出了 hook，实际应用场景就各自发挥了。

```json
{
  "preinstall": "echo preinstall",
  "install": "echo install",
  "postinstall": "echo postinstall",
  "prepublish": "echo prepublish",
  "prepare": "echo prepare"
}
```

再来看看 `npm publish` 的 hook，这个就比较有用了，简单举个例子，`prepublishOnly`，这个 hook 可以在发布前执行，比如项目的打包或者编译之类的，防止忘记编译直接发布了。

另外，在发布成功之后也可以通过 `postpublish` 来上传包信息到数据库等。

```json
{
  "prepublish": "echo prepublish",
  "prepare": "echo prepare",
  "prepublishOnly": "echo prepublishOnly",
  "prepack": "echo prepack",
  "postpack": "echo postpack",
  // 发布成功之后才会执行
  "postpublish": "echo postpublish"
}
```

### npm scripts &、&&、|、||

先来区分一下，&& 和 || 是一伙的，属于逻辑标识符，而 | 和 & 是另一伙的，属于 linux 的功能。

**| &**

这里把前端同学可能不太了解的先说，| 是管道，而单一个 & 表示将指令放入后台工作。

```bash
# | 管道
# 获取 test.txt 的文件内容
# 并把内容传给 less，方便浏览
# less 是 linux 的指令，可以随意浏览文件
cat test.txt | less
```

接下来的这个命令前端同学可能不了解，服务端的同学要知道一些。

比如 `npm run start`，服务启动了之后都是直接挂在 terminal 前台的。

挂在前台什么意思？在服务启动了之后，我们可以尝试输入 `ls` `l` 或者其他指令，可以发现在控制台没有输出。

![前台](https://i.loli.net/2020/04/09/rdCkcQ4HgUwliTN.png)

但是如果运行 `npm run start &`，以 & 结尾，把服务在后台启动，这个时候如果输入 `ls` 或者其他指令，可以看到控制台就有输出了。

> 不用在意前面的输出，那个是 vue-cli-service 的输出，是直接输出到屏幕的。

![后台](https://i.loli.net/2020/04/09/1hDtwOAaGlBmKW8.png)

既然说到这里，那就再说几个常用的 linux 指令好了。

```bash
# 将服务挂在后台
npm run service &
# 查看挂在后台的服务
# 会返回一个带 id 的服务，如下
# [1]  + 1967 running    npm run service
jobs -l
# kill 在后台运行的服务
kill 1967
```

有兴趣的同学可以去了解下面的 linux 指令。

```bash
# 将后台运行的或挂起的服务切换到前台运行
fg
# 将暂挂的服务在后台运行
bg
# 也是将服务挂在后台运行
# 也可以通过 jobs 查询到，并用 kill 停止
# 但是无法查询日志
# 所以一般在 node 中，我们用 pm2 来挂起任务
nohup npm run service &
```

当然，指令除了启动服务挂在后台，他还有其他用处，比如你需要启动的不是服务，只是想运行指令，`npm run script1 & npm run script2 &`，这两个指令是会同步运行的，因为他俩都被挂到后台运行了。

**|| &&**

这俩就是我们比较熟悉的逻辑运算符了，|| 表示前一个如果运行错误，比如代码里有 `process.exit(1);`，才执行下一个，&& 表示前一个运行成功才执行下一个。

```bash
# 执行 test，如果抛出错误则打 log
npm run test || npm run loginfo
# 执行 install，之后再执行 start
npm run install && npm run start
```

### npx 是个啥？我也想用到自己的库里去

npx 是个啥，npx 可以理解成语法糖，背后的逻辑都是一样的，平时主要有两个用法。

**在项目 package.json 中使用**

假设项目中 package.json 如下，当你想要执行 `tsc` 的时候需要在 scripts 中写 `./node_modules/.bin/tsc --project ./`。

```json
{
  "scripts": { "build:ts": "./node_modules/.bin/tsc --project ./" },
  "devDependencies": { "typescript": "^3.7.4" }
}
```

但是使用 npx 可以省略 `node_modules/.bin/` 一长串内容，如下。

```json
{
  "scripts": { "build:ts": "npx tsc --project ./" },
  "devDependencies": { "typescript": "^3.7.4" }
}
```

**保证执行最新脚本代码**

在创建项目中使用 npx，npm 会将库下载下来存放临时文件，并且执行完毕就删除，这可以保证每次运行时候都是最新的代码。

`npx create-react-app my-react-app`，是不是很熟悉？我们想将自己写的库用 npx 运行也很简单，只要在 package.json 中配置如下

```json
{
  "name": "@scope/my-cli",
  "bin": {
    // 非必须，这里是该库原先的使用方式
    "my-cli": "bin/index.js",
    // 这里就是 npx 实际运行的代码了
    // 我们只要保持 key 和 packpage.name 一样即可
    "@scope/my-cli": "bin/index.js"
  }
}
```

接着，发布你的库，然后使用 `npx @scope/my-cli hello`，这就等于全局安装 `@scope/my-cli` 再执行 `my-cli hello`。

### 删除全局安装的所有模块

**慎用，除非你知道你在做什么，否则不要用**。

```bash
rm -rf /usr/local/lib/node_modules
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

## 后语

最近头发的脱落速度和瑞幸咖啡的下跌速度类似，忘性也越来越大，经常会代码写着写着就忘了刚才我要做什么，对于一件事情以前能够保持四五个小时的专注，现在思维很容易打岔，想着，啊是不是年纪大了，以前从来没有对岁数有过恐慌，但是看着公司里优秀的新生代，不免会有些焦虑。如何才能不焦虑？不是废寝忘食的工作，而是应该让自己成为不可替代的那个人，感情是如此，工作亦是如此。

这里不是蹭热点，司徒正美大大的消息想必大家或多或少都了解了一些，惋惜，说是天才的陨落也不为过。所以大家在平时的开发学习中，除了要关注技术细节，更要多多关注自己的身体。赚钱的机会有很多，但唯有健康的身体才能支撑起你伟大的理想。

ps1: 写完之后发现自己了解的也不多，写的挺杂的，如果各位大佬有私货可以偷偷摸摸分享给我，我琢磨琢磨整理整理给加上去，蟹蟹。

ps2: 有点标题党，~~发现精心写的文章阅读量比不过面试文章（指某些），心里不平衡~~，如果你全知道，请加群来群里嘲讽我吧。

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

## TODO

- npm info xxx version
  - latest
- npm info xxx versions
  - all version
- npm info xxx
- npm xxx --json
- npm ls xxx
- npm ls xxx -g
- npm ls xxx --json
- npm audit
- npm bin

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

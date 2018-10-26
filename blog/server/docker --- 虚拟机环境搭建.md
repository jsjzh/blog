![f703738da977391281957edbf0198618377ae2dd.jpg](https://user-gold-cdn.xitu.io/2018/8/7/16512863934417bb?w=1240&h=819&f=png&s=84239)

## 小剧场
测试：裤裆你这页面刷新就白屏啊，怎么了啊，而且你看这 `network`，怎么这些 `js` 这么大啊，很耗流量而且加载速度还很慢诶。

我：嗯，大佬说的是，页面刷新白屏是因为没有服务器没有配置找不到资源重定向，`js` 可以用 `CDN` 或者启动一下 `gzip`，这个让后端或者运维小妹妹配一下就好了。

后端：啥，你说啥，我不会，话说，上次那个接口返回 `null` 的问题我还想找你，为啥我返回 `null` 就不行，数据库就是 `null` 啊，你前端做一下判断不就好了！

![docker](https://user-gold-cdn.xitu.io/2018/8/7/1651286393f01779?w=500&h=229&f=png&s=38433)

我：。。。干，我自己来！

## 前言
以上的小剧场大家当成个段子看看就好了，我相信大部分的前端后端都是挺和谐的，一般来说合力怼产品才是程序员的第一要务（PM 别打我），关于剧场里提到的是否要返回 `null` 的问题，建议大家以后直接反手一个链接给后端 [一千个不用 null 的理由](https://my.oschina.net/leejun2005/blog/1342985)，深藏功与名。

17年毕业到现在算算也有一年多了，前端的工作越来越得心应手，完成日常业务已经没有大问题，前端方面对于一些比较前沿的工具也有了不少心得体会，前段时间手写了一个 `webpack 4.x` 结合 `vue-loader` 的脚手架替换掉了公司项目里比较老旧的脚手架，自己写的插件也在项目里面吭哧吭哧的跑的不亦乐乎，也可以带领公司新入职的前端学习更多的姿势了。

但是，总感觉还缺少了什么，以前写过 `PHP`，对于接口如何实现的也没有那么好奇，无非就是连接数据库写写 `sql`，再大不了就是建建视图查询，用用 `redis` 之类的，再深入一点就是消息队列 `MQTT` 结合 `websocket` 的应用了，那还缺少了什么，没错，就是服务器部署，关于这个，我很好奇！

![我很好奇](https://user-gold-cdn.xitu.io/2018/7/22/164c00a4dc82038e?w=750&h=422&f=jpeg&s=35529)

关于工作初期，我的想法是先要有技术广度的了解，再要技术深度的了解。曾经也有过写脚手架写上瘾的时候，连续一个星期回家写代码写到两三点（大城市的大佬就不要吐槽我了 =3=），各种跑测试，最后出了成果成功运行在线上项目的成就感我至今还记得，但是期间碰到了不少的问题，很多问题在其他领域的人看来甚至都不是问题，只是因为自己知道的太少了，所以被自己限制住了。

想象一下，如果自己能够掌握整个项目从零开始，从第一行代码开始，到发布上线的整个流程，不管是前端后端服务端，自己都能够了解到每个细节，那和后端理论起来，底气都要足不少，不是么？=3=

废话太多，开始正题，本篇文章可以帮助任何对 `docker` 有兴趣的人有一个基础的认识，最后会有一个简单的基于 `vue-cli` 搭建的 `nginx` 服务器的实战应用。

## 环境安装
题主的系统环境是 `windows win7 64位` 系统，所以需要个 `linux` 系统的虚拟机，并且出于装逼的目的，将不使用桌面版的 `linux` 系统，所有的操作均在命令行完成。

### 安装 `virtualBox`
> `virtualBox` 是一个开源的虚拟机软件，相比较 `VMware` 他更小，且开源免费，对于本篇文章想要实现的功能，已经是非常足够。

首先是安装 `virtualBox` 虚拟机，属于一直下一步到天亮的操作，但也附上安装操作教程。

[virtualBox 下载地址](https://www.virtualbox.org/wiki/Downloads)

[virtualBox 安装教程](https://jingyan.baidu.com/article/9c69d48f49186b13c9024e30.html)

### 安装 `ubuntu-server`
> `ubuntu` 是一款 `Linux` 系统，记住 `Debian` 这个单词单词可以在某些时候起到提示作用。另外，就我愈加深入了解服务器端，发现公司搭建更多用的是 `centos`，不过没事儿，说来说去都是 `linux` 系统，对于用户而言就是文件目录或者下载工具不太一样而已。

下一步，下载 `ubuntu-server` 的镜像文件，我用的是 `18.04.1 LTS` 版本的，过个几年如果这文章还会被人翻出来的话，那时候还请下载自己喜欢的版本。

[ubuntu-server 下载地址](https://www.ubuntu.com/download/server)

[ubuntu-server 安装教程](https://jingyan.baidu.com/article/93f9803f5582a3e0e46f55d3.html)

### 配置
- `virtualBox`
  - 新建
    - 这个基本上也是一直下一步到天亮的操作，值得一提的就是关于不能安装 `64位` 系统的 `ubuntu` 的处理，有的电脑会发现新建的时候没有 `ubuntu 64位` 的选项，可以的话请参考这篇文章。
      - [开启 BIOS 的虚拟化技术](https://blog.csdn.net/zhao1949/article/details/53403828)
  - 设置
    - 存储
      - ![存储设置](https://user-gold-cdn.xitu.io/2018/8/7/1651286393f5cbec?w=725&h=439&f=png&s=42483)
      - 如上图，点击那个位置将你下载的 `ubuntu-server` 镜像载入即可。
    - 网络
      - ![网络设置](https://user-gold-cdn.xitu.io/2018/8/7/16512863952d10ef?w=725&h=439&f=png&s=36597)
      - 如上图，使用**桥接网卡**选项，可以设置该虚拟机网络环境为同一局域网下的另一台设备，方便我们后续通过主机访问虚拟机搭建的网络。

## 系统装填成功，开始系统内配置
在此，默认各位已经在虚拟机中安装好了 `ubuntu` 系统，并且也成功进入了系统，在我们开始了解 `docker` 之前，有必要对我们所处环境的墙做一些处理，你知道的，这墙就是那墙。

`ubuntu` 默认安装的下载、更新用的软件是 `apt`，软件安装更新的时候都是从一个存放了大量软件的地方下载的，至于从哪里下载，默认的如下。
```bash
$ cat /etc/apt/sources.list
```

重新设置下载源，这里还备份了一份以防不时之需。`sed` 指令是替换一个文件中的指定字符串为另一个字符串，注意中间的竖线分隔符号，前面是被替换的字符串，我们将地址替换为国内的源地址。
```bash
$ sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
$ sudo sed -i 's|deb.debian.org|mirrors.ustc.edu.cn|g' /etc/apt/sources.list
$ sudo sed -i 's|security.debian.org|mirrors.ustc.edu.cn/debian-security|g' /etc/apt/sources.list
```

更新一下 `apt-get` 的源的地址，这个操作我们以后会经常做。
```bash
$ sudo apt-get update
```

安装我们后续需要的包，里面包括了对 `https` 地址的解析所用的工具包等。
```bash
$ sudo apt-get install apt-transport-https ca-certificates software-properties-common curl
```

为确保系统里面没有自带的 `docker` 软件的残留，我们要清除一下旧版本的 `docker`，虽然新系统一般都不会有就是了。
```bash
$ sudo apt-get remove docker docker-engine docker.io
```

添加 `docker` 的 `GPG` 密钥，并添加 `docker-ce` 的软件源，这告诉了 `apt` 去哪里下载 `docker-ce`。
```bash
$ curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

更新软件包缓存。
```bash
$ sudo apt-get update
```

下载 `docker`。
```bash
$ sudo apt-get install docker-ce
```

设置开机的时候启动 `docker`，并启动 `docker`。
```bash
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

添加当前用户到 `docker` 的用户组，以后可以不用输入 `sudo` 直接使用 `docker`。
```bash
$ sudo groupadd docker
$ sudo usermod -aG docker $USER
```

更改 `docker-ce` 内部使用的下载镜像的地址源，对于 `ubuntu 16.04 +` 的系统，我们只要在 `/etc/docker/daemon.json` 更改即可，注意，你可能需要 `root` 的权限才可以对该文件做更改。
```bash
# 使用 root 权限
$ sudo -i
$ vim /etc/docker/daemon.json
# 修改 daemon.json 为如下
{"registry-mirrors": ["https://docker.mirrors.ustc.edu.cn/"]}
# 重启 docker 服务
$ sudo systemctl restart docker
# 记得退出当前的 root 环境
$ exit or ctrl + d
```

至此，对于前期环境的配置已经完成的差不多了，我们已经替换了 `apt-get` 下载新的软件的源地址为国内地址，也替换了 `docker-ce` 下载新的镜像时候使用的源地址为国内地址，现在让我们来测一发 `docker`。

`docker run` 为运行一个镜像文件，如果本地没有找到该镜像，会去镜像地址地址下载拉取。如果提示 `permission` 之类的错误，但你已经做了添加当前用户到 `docker` 用户组的操作，重启一下虚拟机即可，这是权限不够的意思。
```bash
$ docker run hello-world
```

因为我们后期会用到 `git` 到我的 `github` 上拉取 `vue-cli-base` 的代码，所以这里可以提前安装一个 `git`。
```bash
$ sudo apt-get install git
# 配置用户名和伊妹儿
$ git config –global user.name "你的用户名"
$ git config –global user.email "你的伊妹儿"
```

然后既然是用了 `vue-cli` 那 `nodeJs` 自然是不能少的，因为我们会用 `webpack` 进行打包，那就顺便安装个 `nodeJs`，我这里选择的 `nodeJs` 官方长期支持的版本 `8.x`，需要安装 `10.x` 版本的把 `setup_8.x` 改为 `setup_10.x` 即可。
```bash
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ sudo apt-get install -y nodejs
```


下载了 `nodeJs` 那自然是需要对 `nodeJs` 切换一下国内的源。
```bash
$ npm config set registry https://registry.npm.taobao.org
```

至此，前期准备已经告一段落，接下来就是开始实战部分。

## `docker` 实战
`docker` 是个啥，我就近期在 `linux` 系统中使用之后的使用感受来简单介绍一下。
首先来几个英文单词的解释。

- `image`
  - 镜像
  - 可以理解成操作系统的镜像，但又不是所有的镜像都是操作系统。
- `container`
  - 容器
  - `docker` 跑起来的时候会新建一个容器，该容器里面会运行镜像。
- `registry`
  - 镜像源；仓库
    - 可以理解成 `docker` 需要拉取镜像的时候所访问的地方。

有一些比较特殊的概念，可以看着留个印象，`docker` 里面的镜像是分层的，打个比方，一个操作系统的镜像，里面可能分别安装了 `git node`，然后加上系统的基础镜像，一共三层，我们称他为 A镜像，如果这时候还有个 B镜像，他里面有 `git node docker`，然后加上系统的基础镜像，那 `git node` 层 AB镜像 就可以共用。

### `docker nginx` 基础操作
相信作为前端，“`nginx` 做个反代理就好了”，这种话应该看到不少过，实战我们就直接用 `nginx` 做个主要例子，期间会对对常用的一些 `docker` 语法做解释。

首先，我们先拉取 `nginx` 的镜像文件。
```bash
$ docker pull nginx
```

新建一个用于测试 `docker` 的文件夹，方便管理。
```bash
$ mkdir /home/$USER/docker-demo
$ cd /home/$USER/docker-demo
$ mkdir html
```

新建一个用于 `nginx` 的默认展示页，我们的第一步就是要让这个代码跑在页面上。
```bash
$ cd html
$ echo "<html><h1>Hello World</h1></html>" > index.html
$ cd ..
```

将 `docker` 跑起来吧，当然在跑起来之前，还是需要解释一下参数。

- `docker run`
  - 这将新建一个容器，并在里面跑一个镜像。
- `-d`
  - 将该容器运行在后台，我们使用 `nginx` 那肯定是需要持续跑在后台的，又叫守护进程。
- `-p`
  - 将**容器对外曝露的端口**映射到**宿主机的端口**（也就是我们上面花了半天功夫装的虚拟机）。
  - 说白了就是访问宿主机的 `8888` 端口就好像访问容器的 `80` 端口一样。
- `--name`
  - 给我们新建的容器起一个名字。
- `-v`
  - 这是 `docker` 里面比较特殊的东西，`volume`，可以这么理解，冒号前面的为宿主机的路径，后面为容器内的路径，这个参数可以将宿主机路径下的东西**同步**到容器内，因为 `/usr/share/nginx/html` 这个目录就是 `nginx` 中默认的对外展示的目录，所以我们将刚才新建的 `html` 文件夹同步到默认目录里面，我们就可以访问该页面了。
- `nginx`
  - 这里是指向我们新建容器所需要用到的镜像文件，如果你上面没有做 `docker pull` 的操作，`docker` 也会自动到仓库去拉取过来。

```bash
$ docker run -d -p 8888:80 --name my-first-nginx -v $PWD/html:/usr/share/nginx/html nginx
```

ok，不出意外会输出一串字符串，这个就代表了你新建容器的 `id`，我们新建的所有的容器都会有一个用作标识的 `id`，不过我们不需要记住它，以后可以用 `docker ps` 来查询。

查看一下我们新建的容器是否有在好好工作。
```bash
$ docker ps
```

若见到类似如下，那代表我们的容器有在好好工作，验证一下吧。

![docker ps](https://user-gold-cdn.xitu.io/2018/8/7/1651286394ebfe82?w=1147&h=51&f=png&s=3048)


在命令行中看看效果。
```bash
$ curl localhost:8888
```

看到如下，那就成功了。

![curl](https://user-gold-cdn.xitu.io/2018/8/7/1651286394f68a1e?w=408&h=37&f=png&s=1328)

命令行看着不过瘾，我们到真机上看看效果，不过在这之前，我们需要知道我们宿主机的 `ip` 地址，在上面的步骤中我们已经将网络改成了**桥接网卡**，所以现在我们的虚拟机用是和主机存在同一个局域网下的另一个 `ip`。

查看网络接口上分配的 `ip` 地址，再加个筛选 `IPv4` 的参数。
```bash
$ ip -4 a
```

找到如下，`192.168` 开头的，这就是虚拟机的 `ip` 地址了。

![ip -4 a](https://user-gold-cdn.xitu.io/2018/8/7/16512863af6a4972?w=816&h=51&f=png&s=2945)

接着，我们只需要打开主机，访问 `192.168.0.200:8888` 不要忘记带上端口号即可。

![主机访问](https://user-gold-cdn.xitu.io/2018/8/7/16512863b4c8a1e0?w=527&h=250&f=png&s=13749)

### `nginx` 实际应用
上面那些都是基操，接下来我们来点不一样的，我们会找一个基于 `vue-cli` 的 `webpack` 脚手架，然后依旧是跑在 `nginx` 容器里面的应用，会有 `shell` 脚本的书写，方便运维朋友们进行项目一键更新发布。这更加适用于日常我们的项目发布流程。

在准备阶段我们安装了 `nodeJs`，我也准备了一个基础的 `vue-cli` 项目，用于打包生产项目。

在开始之前，我们要先停止刚才我们跑的容器。
```bash
# 这是停止容器的指令
# 因为 nginx 是运行在后台的，所以要先停止他才可以移除
# 还记得上面生成的容器唯一 id 么，这里只需要输入前几位即可停止该容器
$ docker stop 5af87
```

当然，光光停止还不够，出于洁癖的心理，还要把容器移除，减少占用空间。
```bash
$ bash rm 5af87
```

接下来创建一个新的文件夹，用于演示接下来的项目。
```bash
$ mkdir /home/$USER/docker-demo-two
$ cd /home/$USER/docker-demo-two
```

复制 `github` 上的项目到该文件夹，这是基于最新版的 `vue-cli` 生成的例子，当然官方还没有用上 `webpack 4.x`，不过问题不大，演示足矣。
```bash
$ git clone https://github.com/jsjzh/vue-cli-base.git
```

进入 `vue-cli-base` 之后就是前端基操了，使用 `npm` 包管理工具安装项目依赖包，由于我们这里是要得到生产环境的代码，所以直接执行 `npm run build` 将项目进行打包。
```bash
$ cd vue-cli-base
$ npm install
$ npm run build
```

项目打包好之后查看一下是否有相关的文件生成。
```bash
$ cd dist
$ ls
```

看到如下目录就没问题了。

![webpack 打包生成 dist](https://user-gold-cdn.xitu.io/2018/8/7/16512863b97e1eb1?w=578&h=50&f=png&s=1741)

完成之后进入到开始新建的目录，进行容器的 `volume` 挂载操作。
```bash
$ cd /home/$USER/docker-demo-two
$ docker run -d -p 8889:80 -v $PWD/vue-cli-base/dist:/usr/share/nginx/html nginx
```

还是老样子，命令行会输出该容器的 `id`，但我们还是需要查看一下 `nginx` 有没有好好的运行在后台。
```bash
$ docker ps
```

查看到容器之后，在主机打开 `192.168.0.200:8889` 即可看到熟悉的 `vue-cli` 的界面了。

但是这还没完，总不能让运维小妹妹每次都跑一遍 `npm` 吧，作为贴心的开发哥哥，自然是要准备脚本供小妹妹使用，新建一个 `shell` 脚本，并保存内容如下。

> 提示：`vim` 编辑器编辑的操作是按 `i`，编辑完成之后 `Esc ---> :x` 即可。

```bash
$ cd /home/$USER/docker-demo-two
$ vim update-project.sh

# 脚本内容
cd vue-base-cli
git pull
npm install
npm run build
```

保存好之后，以后项目需要更新的时候，只需要跑一跑脚本就可以完成。
```bash
$ cd /home/$USER/docker-demo-two
$ /bin/bash update-project.sh
```

为看到效果，我们直接试着更改一下项目内容。
```bash
$ cd vue-cli-base/src
$ vim main.js

# 加入以下内容
console.log("file is change!");
```

退回到上层，执行我们的脚本。
```bash
$ /bin/bash update-project.sh
```

等运行完之后，在主机打开 `192.168.0.200:8889`，按 `F12` 打开控制台，就看到了我们输入的内容，至此，项目更新已成功。

![主机访问](https://user-gold-cdn.xitu.io/2018/8/7/16512863bef281b2?w=1185&h=646&f=png&s=55425)

不过既然说 `docker` 跑的都是容器，那自然是可以进到容器里面去一探究竟的，我们进入 `nginx` 的容器里面看看。

- `docker exec`
  - 在运行中的容器中执行命令。
- `-it`
  - 保持终端打开状态。
- `/bin/bash`
  - 这里就是需要被执行的命令，这个命令是提供一个 `bash` 环境。

```bash
$ docker exec -it 26e52b8c6ffd9 /bin/bash
```

如果你在命令行看到 `root@26e52b8c6ffd9:~#` 就代表你进入了这个容器，我们再去看看 `/usr/share/nginx/html` 文件夹下有没有 `dist` 中的文件。
```bash
$ cd /usr/share/nginx/html
$ ls
```

这里可以看到 `index.html` 文件和 `static` 文件夹，我们再去看看刚才的 `file is change` 在哪里。
```bash
$ cd static/js
# 注意 这里的名字是都不一样的，你可以先敲个 app 再按 tab 键，会进行自动补全
$ cat app.86602636cb9e13f553fb.js
```

在底部的小角落我们看到了刚才输入的代码。

![static 中的 app.js](https://user-gold-cdn.xitu.io/2018/8/7/16512863c6c4fc6c?w=1240&h=39&f=png&s=14290)

别忘了从容器里面退出来，依然是 `$ exit` 或者 `ctrl + d` 退出容器。

### docker 一些便捷的指令
```bash
# 批量删除容器
$ docker rm `docker ps -a -q`

# 批量删除镜像
$ docker rmi `docker images -q`

#卸载 docker
$ sudo apt-get purge docker-ce
$ sudo rm -rf /var/lib/docker
```

## 后语
至此，`docker` 简单的应用已经结束了，后续还会有关于 `docker` 的应用教程，想一想，如果我们可以把 `static` 目录下的文件都传到云空间，利用 `CDN` 加速，或者再配置一下 `nginx` 的 `gzip` 压缩，再来一下反代理，然后了解一下容器新建数据库，跑一个 `nodeJs` 的接口服务，再用 `docker-compose` 把前端后端数据库连接起来，实现环境的一键部署，那不是自己一个人就是一个军队？

想想还有点小兴奋，不过饭要一口口吃，代码自然也是要一行行敲，在日益稀疏的头发中找到代码的真谛，不求闻达于诸侯，只求一方天地宁静。

代码如人生，我甘之如饴。

我在这里 [gayhub@jsjzh](https://github.com/jsjzh/blog) 欢迎大家来找我玩儿。

## 大纲
- 环境配置（DONE）
  - `virtualBox` 下载安装
  - `ubuntu-server` 下载安装
  - 虚拟机相关配置
- 系统装填成功，开始系统内配置（DONE）
  - 配置 `apt-get` 下载源
  - 配置 `docker` 镜像源
  - 下载 `git` 和 `nodeJs`
- `docker` 实战（DONE）
  - 简单的 `nginx` 应用
  - 结合 `vue-cli` 的 `nginx` 应用
- TODO（TODO）[分篇]
  - `Dockerfile`
  - `docker-compose`
  - `nginx`
  - 上传 `app.js` 等至云空间
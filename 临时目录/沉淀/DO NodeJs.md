# NodeJs 备忘录

## 好文分享

- [NodeJs 学习笔记](https://github.com/chyingp/nodejs-learning-guide)

## 模块分享

### yarn

```cmd
npm install yarn -g
```

### mirror-config-china

这个库会自动配置很多第三方库的地址为中国镜像，下载速度嗖嗖的

```cmd
npm install -g mirror-config-china --registry=http://registry.npm.taobao.org
```

### nrm

切换 `npm` 源地址很方便的工具

```cmd
npm install -g nrm
nrm add npm http://registry.npmjs.org
nrm add taobao https://registry.npm.taobao.org
nrm use taobao
nrm use npm
```

### live-server

可以方便的在本地起一个目录运行一个静态网站，并且会监听文件改变

### now

可以在网络上部署自己的静态网站应用

### nvm

用来管理多版本的 `node`

[文档](https://github.com/nvm-sh/nvm#mac-os-troubleshooting)

## 发布 npm 包

```cmd
nrm use npm
npm whoami
npm login
npm publish
```

## 零散记录

```cmd
# 查看所有全局安装的模块
npm ls -g --depth=0
# 删除所有全局安装的模块
rm -rf /usr/local/lib/node_modules
# 报错 npm resource busy or locked
npm cache clean --force

```

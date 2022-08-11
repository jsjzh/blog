# 单台电脑 git 配置多域名

## 步骤

### 生成密钥

生成 gitlab 密钥

```cmd
ssh-keygen -t rsa -C '这里填写你的 gitlab 邮箱' -f ~/.ssh/gitlab-rsa
```

生成 github 密钥

```cmd
ssh-keygen -t rsa -C '这里填写你的 github 邮箱' -f ~/.ssh/github-rsa
```

配置完之后会在 `~/.ssh` 目录下生成四个文件，`.pub` 后缀的是需要配置到 `github` 和 `gitlab` 上的公钥

### 配置文件

在 `~/.ssh` 目录下新建名为 `config` 的**文件**，用于配置多个不同的 `host`

```cmd
# gitlab
Host gitlab.com
  HostName gitlab.com
  IdentityFile ~/.ssh/gitlab-rsa
  User 这里填写你的 gitlab 登陆名

# github
Host github.com
  HostName github.com
  IdentityFile ~/.ssh/github-rsa
  User 这里天蝎你的 github 登录名
```

- `Host` 不支持 `*` 和主机名混用，即类似 `*.example.com` 的方式是不行的，单独的 `*` 表示匹配所有规则，也就是默认规则

配置完 `config` 之后，只要符合 `Host` 的地址就会走其下的 `rsa` 配置，比如 `git@github.com:jsjzh/example.git`，当你 `git push` 的时候就会走 `github` 的配置

### 配置到网站

首先登陆你的 `github` 或者 `gitlab` 账号，打开右上角的 `Setting`，打开 `SSH Keys`，点击 `Add key`

```cmd
cat ~/.ssh/gitlab-rsa.pub
```

将以上的内容复制到 `key` 一栏即可，`github` 也是类似操作

### 测试是否成功

```cmd
ssh -T git@github.com
ssh -T git@gitlab.com
```

如果看到 `Welcome` 消息，代表就是成功了

### 提交用户信息配置

全局提交信息配置，配置了如下之后，以后的提交都会带上这个信息，如果是公司的电脑，公司的项目是主项目，可以在此配置你的公司信息，在自己的项目里面使用 `git config --local` 来配置当前项目的信息

```cmd
git config --global user.name '这里写你的登录名'
git config --global user.email '这里写你的邮箱'
```

每次新建一个项目的时候，在 `git init` 之后，最好都配置一下如下配置，这个是设置当前的项目的提交人信息

```cmd
git config --local user.name '这里写你的登录名'
git config --local user.email '这里写你的邮箱'
```

### 其他

另外，稍微提一下生成全局 `rsa` 的代码

```cmd
ssh-keygen -t rsa -C '这里填写你的邮箱'
```

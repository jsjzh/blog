# git 备忘录

## 好文分享

- [学习 git 指令](https://www.yiibai.com/git/git_push.html)

## 常用命令

```cmd
# 查看当前状态
git status
# 查看区别
git diff <file>
# 查看历史
git log
# 查看历史记录简洁版
git log --pretty=oneline
# 回滚上一个版本
git reset --head HEAD^
# 查看执行过的操作
git reflog
# 将某文件的更改丢弃
git checkout <filename>
```

## 分支

```cmd
# 查看当前分支
git branch
# 创建分支
git branch <name>
# 删除分支
git branch -d <name>
# 查看所有远程分支
git branch -r
# 查看远程和本地所有分支
git branch -a
# 拉取远程分支并创建本地分支
git checkout -b <本地分支名> <远程主机名，一般为 origin>/<远程分支名>

# 切换分支
git checkout <name>
# 创建 + 切换分支
git checkout -b <name>

# 合并某分支到当前分支
git merge <name>
```

## 贡献项目

- 领取或创建新的 `issue`，添加自己为 `Assignee`
- 把项目 `fork` 到自己的仓库，然后 `clone` 到本地，并设置用户信息
- 修改代码并提交，推送到自己的仓库，注意修改提交的信息为 `issue` 号 + 描述
- 在 `github` 上提交 `pull request`，添加标签，并邀请维护者进行 `reivew`

```cmd
git clone git@github.com:docker_user/docker_demo.git
cd docker_demo
# 对项目做一些修改
git commit -a -s
# "Fix issue #235: describe ur change"
git push
```

如果你经常需要给一个仓库提交你的修改，那就要定期更新自己的本地仓库

```cmd
git remote add upstream 这里写该库的作者的地址
git fetch upstream
git rebase upstream/master
git push -f origin master
```

## git push

```cmd
# 如果当前分支与多个主机存在追踪关系，-u 就会指定一个默认主机
git push

# 将当前分支推送到 origin 主机的对应分支，如果当前分支只有一个追踪分支，那么 origin 主机名也可以省略
git push origin

# 将本地的 master 分支推送到 origin 主机，同时指定 origin 为默认主机
git push -u origin master
```

> tip：不带任何参数的 `git push` 默认只推送当前分支，这是 simple 方式，还有一种 matching 方式，会推送所有对应的远程分支的本地分支，git 2.0 版本之前默认为 matching 方式，现在是 simple 方式

## 零散记录

git 更改最后一次提交的注释信息

```cmd
git commit --amend
```

``

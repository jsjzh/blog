# git 基操

https://learngitbranching.js.org/?locale=zh_CN

## 目录

- [git 基操](#git-基操)
  - [目录](#目录)
  - [commit](#commit)
  - [branch](#branch)
  - [checkout](#checkout)
  - [merge](#merge)
  - [相对引用](#相对引用)
  - [reset](#reset)
  - [revert](#revert)
  - [cherry-pick](#cherry-pick)
  - [rebase](#rebase)
  - [clone](#clone)
  - [fetch](#fetch)
  - [pull](#pull)
  - [push](#push)
  - [pull request](#pull-request)
  - [remote tracking](#remote-tracking)
  - [其他](#其他)

## commit

```bash
git commit
# 已经提交过的内容再进行一点修改
git commit --amend
```

## branch

```bash
git branch dev
# 将 master 分支强制指向一个提交
git branch -f master HEAD~3
# 支持链式操作
git branch dev master~^2~
```

## checkout

```bash
git checkout dev
git checkout -b dev
git switch dev
# 支持链式操作
git checkout HEAD~^2~2
```

## merge

```bash
# 合并 dev 的代码修改到 master
master\*

git merge dev
git checkout dev
git merge master
```

## 相对引用

git hash 基于 SHA-1，共 40 位

```bash
# 向上移动 1 个提交记录
git checkout master^
# 向上移动 2 个提交记录
git checkout master^^
# 当存在两个父节点时，^2 可以选择第二个父节点
git checkout master^2
# 向上移动 2 个提交记录
git checkout master~~
# 向上移动 2 个提交记录
git checkout master~2
```

## reset

本地的提交撤销

```bash
git reset HEAD~
# 之后的提交还在，但是处于未加入暂存区状态
```

## revert

远端的提交撤销

```bash
git revert HEAD~
# 远端并不会和 reset 一样将改变移入暂存区
# 而是创建一个新的 commitId 指向前一个提交
```

## cherry-pick

```bash
git cherry-pick ${commitId} ${commitId}
```

## rebase

取出一系列的提交，线性提交

```bash
dev\*
git rebase master
git checkout master
git rebase dev
```

```bash
bugFix \-\> master
git rebase master bugFix
```

```bash
# 交互式 reabase
# 从当前分支到 dev 分支之间的提交记录都列出来
# 然后通过 vim 选择或者排序想要的分支
git rebase -i dev
```

## clone

**远程分支反映了远程仓库在你最后一次与它通信时的状态**

远程仓库默认名为 `origin/xxx`

假设分支为 `master`，当 clone 了仓库到本地，本地会有一个 `origin/master`，如果 `checkout origin/master` 并 commit，这不会改变 `origin/master` 分支的内容，HEAD 会前移

```bash
git clone
```

## fetch

**fetch 不会修改本地仓库的状态**

fetch 用来获取远程仓库的更新，比如本地的 `origin/master` 落后远程的仓库有两个 commit，执行 `git fetch` 可以获取到这两个 commit，并把本地的 `origin/master` 分支指向 最新的 commitId

```bash
# 更新所有分支
git fetch
git fetch origin dev
# 获取远端的 dev 分支到 master 下
git fetch origin dev:master
# 也可以用 ~ ^ 相对引用符号
git fetch origin dev^:master
# 会自动创建 no-exist-master 分支
git fetch origin dev:no-exist-master
# 如果 fetch 到本地，会创建新分支
git fetch origin :bar
```

## pull

当使用 `git fetch` 更新代码后，需要将本地分支和远程分支合并，即

1. `git fetch`
2. `git merge origin/master`

`git pull` 可以看成是 `git fetch` 和 `git merge` 的合体版本

## push

把远程分支 `origin/master` 中没有的 commit 推向远程仓库，然后把本地的 `origin/master` 分支指向最新的 commitId

```bash
# 如果远程已经有了提交，本地也有提交，该怎么办？
master\*
git fetch
git rebase origin/master
# or git mrege origin/master
# or git pull --rebase
git push
```

`git push ${xxx} ${xxx}:${xxx}`

```bash
git push origin master
git push origin dev:master
# 可以使用 dev^ 来表示 source
git push origin dev^:master
# 自动创建远端不存在的分支
git push origin dev:no-exist-dev
# 如果 push 到远程空仓库，会删除远程仓库的分支
git push origin :foo
```

## pull request

如果策略开启了 pull request，这意味着不能够直接推送代码到 master 分支，需要通过推送新分支，然后创建 pull request 的方式，来告知远端合并

但如果已经在本地的 master 分支推送了代码，怎么办？创建新的分支，并把本地 master 分支 reset 至 origin/master，再推送新分支即可

```bash
git reset origin/master
git checkout -b feature ${commitId}
git push origin feature
```

## remote tracking

从远程拉代码分支的时候，就被自动设置了 remote tracking，比如获取 `origin/master` 分支时，就自动创建了 `master` 分支

```bash
# 指定 newMaster 对接 origin/master
git checkout -b newMaster origin/master
# 指定 newMaster 对接 origin/master
git branch -u origin/master newMaster
# or
# newMaster\*
# git branch -u origin/master
```

## 其他

HEAD 一般代表一个最近的提交记录，原先通常是指向分支，但是可以通过 `checkout` 来指定到其他分支或者 `commitId`

```bash
git checkout ${commitId}
# 建立标签
git tag v1 C1
# 描述
git describe
```

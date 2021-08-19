---
title: git 用法
date: 2021-07-15 22:59:22
tags: [git]
category: other
description: 新人入职不知怎么拉取仓库代码？不知道git fetch 和 git pull的区别? 到底是用git merge还是git rebase? 这里有你想要的答案
---

![](git-用法/github.jpg)

## 前言

别人上班，我摸鱼，别人加班，我摸鱼，然后就写出了这篇 Git 的简单使用教程。

摸摸索索一个多月总算是完成了这篇 git 的学习笔记 😂， 还好最后还是坚持了下来，可能有些有问题，欢迎各位大佬指正，可以通过提[issue](https://github.com/yanxiaolazy/blog/issues)的方式，或者关注我的公众号, 微信搜索 **前端入坑手册**，在后台给我留言。

## 准备

1. 首先需要生成 ssh 秘钥，`ssh-keygen -t rsa -C "你公司内部邮箱地址"` ，如果成功，将会在`~/.ssh`文件夹下生成对应的私钥和公钥。
   `ls`

![](git-用法/image-20210716095654977.png)

2. 复制`id_rsa.pub`文件的内容（如果是 GitHub）到`setting -> SSH and GPG keys-> New SSH key` 并保存

> 记住放到 GitHub 上的是公钥

3. 全局配置账号

   ```bash
   git config --global user.name "xxxx"
   git config --global user.email "xxxx"
   ```

完成以上步骤后，就可以愉快的 pull 或 push 代码了。和 https 拉取方式不同的是，https 方式需要每次提交前都手动输入用户名和密码，ssh 的方式配置完毕后 Git 都会使用你本地的私钥和远程仓库的公钥进行验证是否是一对秘钥，从而简化了操作流程。

## 基础篇

​ 初始化一个 git 仓库，使用命令`git init` ，会在项目下生成一个.git 的文件夹，这就是 Git 的版本库

> 前情提要：git 有工作区和暂存区之分
>
> 工作区：就是我们项目所在的区域
>
> 暂存区：git add 操作之后，实际就是将工作区的内容添加到暂存区

### git add

将工作区的更改，添加到暂存区

```bash
# 添加指定文件，后面可跟多个文件，以空格分开
git add <具体的文件名或某个文件下的所有内容>
# 添加项目下的所有文件
git add .
```

### git commit

将暂存区的内容提交到当前分支，也就是本地仓库

```bash
# 会打开一个可编辑区域
git commit
# 直接将备注添加在后面
git commit -m "xxxx"
# 等价于git add && git commit
git commit -am "xxxx"
# 对最近一次的提交信息进行修改，会更改hash值
git commit --amend
```

### git push <remote> <place>

将本地分支发布到远端分支

- 常用命令

```bash
# 不带参数的push。注意 —— git push 不带任何参数时的行为与 Git 的一个名为 push.default 的配置有关
git push
# 将本地的dev分支，发布到远端同名的dev分支
git push origin dev
# 将本地当前分支，发布到远端与本地当前分支同名的分支上
git push origin
```

- 进阶命令

格式：`git push origin <source>:<destination>`

> `source` 可以是任何 Git 能识别的格式，例如：`HEAD~2`，`foo^2`

```bash
# 将本地dev分支上的dev^处发生的更改提交到远端分支feat
git push origin dev^:feat
```

### git pull <remote> <place>

拉取远端更新分支到本地

- 常用命令

```bash
# 不带参数的pull。 将远端origin上的master上的更新拉取到本地，并与本地的master分支进行合并
git pull
git pull origin
```

- 进阶命令

同样`git pull`也可以使用`<source>:<destination>`格式
格式：`git pull origin <source>:<destination>`

> `source` 可以是任何 Git 能识别的格式，例如：`HEAD~2`，`foo^2`

```bash
# 拉取远端分支上的更新并与本地的某个分支进行合并
git pull origin master:dev
```

### git fetch

将远程主机的最新内容拉取到本地，但不会合并

```bash
# 默认远程主机是origin
git fetch
# 拉取所有远程主机上的更新，默认是origin
git fetch --all
# 拉取远程主机origin上dev分支最新的更新
git fetch origin dev
```

### git merge

将两个或多个分支进行合并，并生成一个新的提交记录，保留合并历史

```bash
# 假设在master分支，以下命令，就是在说，将dev分支合并到master上
git merge dev
# -ff 指fast-forword, 默认采用模式。使用这种模式，不会创建新的commit节点
git merge --ff -m "添加一些message"
# --no-ff fast-forword模式下，仍会创建一个新的commit节点。这是合并一个tag时的默认行为
git merge --no-ff -m "添加message"
```

> 执行`git pull`命令，实际就是`git fetch + git merge`

### git rebase

是另一个合并分支的命令，其实际上就是取出一系列的提交记录，”复制“它们， 然后在另外一个地方着个的放下去。

- 合并分支

  ```bash
  # 当前分支topic
  # 		A---B---C topic
  #	 /
  # D---E---F---G master
  #执行以下任意一条命令
  git rebase master
  git rebase master topic
  # 								A'--B'--C' topic
  # 							/
  # D---E---F---G master
  ```

* `git rebase --abort`: `rebase`时，发生冲突，可以用来阻止`rebase`
* `git rebase --continue`: `rebase`时， 解决了冲突后，用来继续`rebase`
* `git rebase -i`: 合并多个`commit` 记录
* `git pull --rebase = git fetch + git rebase`

### git checkout

这是一个强大的命令，可以做很多事

- 创建新的分支

  `git checkout -b dev` : 基于当前分支创建一个新的`dev`分支，并切换到`dev`分支

  `git checkout -b dev master`: 基于`master`分支创建一个新的`dev`分支，并切换到`dev`分支

  > 等价于`git branch -b dev`

- `git checkout dev`: 切换分支， 切换到`dev`分支

  > 等价于`git branch dev`

- `git checkout <file>`: （`file`可以是个"点", 表示撤销所有文件的更改）撤销工作区中的指定更改，不包含未被`git`监控的文件

  > 等价于`git restore <file> `
  >
  > 如果需要撤销暂存区的更改，可以使用`git restore --staged <file>`

### git cherry-pick

如果你只需要某个分支的部分提交代码，就可以考虑使用这条命令

- `git cherry-pick <commit>`

```bash
# 当前分支 master (以下字母代表commit hash)
# 			e - f - g dev
# 		/
# a - b - c - d master
# 倘若master分支只需要dev分支上的本分提交, 可以使用以下代码
git cherry-pick e...f
# 			e - f - g dev
# 		/
# a - b - c - d - e' - f' master
```

- `git cherry-pick <branch>` : 命令的参数，不一定是提交的哈希值，分支名也是可以的，表示转移该分支的最新提交。
- `git cherry-pick --abort`: 发生代码冲突时，可以阻止合并代码，回到操作前的样子
- `git cherry-pick --continue`: 解决代码冲突后，继续进行`cherry-pick` 命令
- `git cherry-pick --quite` : 发生代码冲突后，可以阻止合并代码，但是不会回到操作前的样子

## 其他命令

### git branch

用于分支管理的命令

```bash
# 列出所有分支
git branch
# 列出所有分支，并带上最进一次的commit记录
git branch -v
# 创建一个新分支dev
git branch dev
# 删除dev分支
git branch -d dev
# git branch --delete --force的简写，用于强制删除
git branch -D dev
# 基于当前分支复制一个新的分支branch-copy
git branch -c branch-copy
# 基于dev分支复制一个新的分支dev-copy
git branch -c dev dev-copy
```

### git stash

将本地的更改暂存。

当本地发生更改，但还未`commit`, 又要进行其他操作( 如更新远端最新代码 )，如果直接`git pull`或`git pull --rebase` 会报错，这时，就需要我们先将更改暂存。

```bash
# 将更改暂存起来
git stash
# 也可以在暂存时，添加备注
git stash -m "暂存"
# 查看stash了哪些
git stash list
# 取出最近的一次stash, 并删除记录
git stash pop
# 取出指定的stash记录, 不会删除
git stash list
git stash apply <前面list 列出的某一项>
# 删除所有的stash
git stash clear
# 删除指定的一次stash
git stash drop <stash 记录>
```

> 如果需要暂存还未被`git`跟踪的文件，可以先`git add` 后再`git stash`

### git reset

版本回退，如果是还未提交到远端仓库，可以直接使用`git reset` 回退，如果已经提交则需要下面提到的`git revert`

```bash
# 回退一个commit
git reset HEAD^
# 向后回退两个commit, 并且丢失回退后的所有commit数据
git reset --hard HEAD~2
```

### git revert

版本回退，会创建一个新的`commit`记录。

```bash
# 撤销最新的commit
git revert HEAD
```

## 参考

[1] https://juejin.cn/post/6974184935804534815?share_token=e6867a4f-d921-469d-84e2-e4a3e1810d0f

[2] https://learngitbranching.js.org/?locale=zh_CN

[3] https://www.liaoxuefeng.com/wiki/896043488029600)

[4] https://www.cnblogs.com/hujunzheng/p/9732936.html

[5] https://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html

###

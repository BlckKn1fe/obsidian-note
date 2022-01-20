---
creation date: 2021-12-28 00:09:59
last modified: 2021-12-30 08:07:30
title: Git
categories:
- tools
tags:
- git
- github
---

# 常用

## 设置用户签名

签名主要的作用就是区分操作者，这个信息在每一个版本的提交信息中都可以查看到。**注意：设置的这个签名和 github 没有关系**

设置用户签名：

```
git config --global user.name username
git config --global user.email email
```

在 Windows 和 Linux 下查看签名信息是在 `~/.gitconfig` 中

## 创建本地仓库

```
git init
```

## 查看仓库状态

```
git status
```

会显示当前所操作的分支、有无 commits，有没有 untracked files、以及缓存区内容。

## 追踪/取消追踪文件

追踪文件，add 后接文件名或文件夹名：

```
git add <file... or dir...>
```

取消追踪文件：

```
git rm --cached <file... or dir...>
```

注意：如果一个文件没有被添加到缓存区，并且不添加 `--cached` 参数，那么文件会直接从工作区中移除。另外 `--cached` 参数只是将文件移出缓存区而已，告诉 git 不在追踪这个文件

## 提交本地库

```
git commit -m
```

后面可以补充上具体的文件名，-m 后面添加对本次提交的描述，内容用双引号包围，提交之后会看到本次提交的`版本号`

## 查看提交版本号

简略查看，准确的说，这个还会版本切换的日志：

```
git reflog
```

详细查看，只显示以当前版本为时间点之前的所有历史提交：

```
git log
```

两种方式都可以看到 `HEAD` 指针的指向

## 版本穿梭

方式一：（不推荐）

reset 这种方式会直接把 HEAD 以及 branch 指针一起拉回那个版本，并且会丢失目标版本之后的内容，虽然可以通过 `reflog` 找到后面的版本，但是这种方式比较强烈的，可能会造成数据丢失

```
git reset --hard <commit>
```

这个 commit 参数就是版本号，另外推荐使用 soft 方式
>--hard 参数说明
>Resets the index and working tree. Any changes to tracked files in the working tree since `<commit>` are discarded. Any untracked files or directories in the way of writing any tracked files are simply deleted.
>
>https://git-scm.com/docs/git-reset

方式二：

```
git checkout <commit>
```

这种方式是最好的方式去回看历史版本，因为它底层会创建一个匿名分支，使 HEAD 处于 detached 状态。当我们切回 master 的时候，这个匿名分支也就会消失。如果我们希望在过去的某个版本进行修改，也可以参考下面的提示：

```
You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>
  
Or undo this operation with:

  git switch -
```

# 分支

分支主要是起并行推进多个功能开发的作用，提高开发效率，各个分支之间互不干扰，如果其中一个分支失败，无法完成，只需要删除该分支即可，不会影响到主分支
![](https://images-1259064069.cos.ap-guangzhou.myqcloud.com/images/20211229001451.png)

## 查看分支

```
git branch -v
```

`-v` 参数会显示的稍微显示一点，会提示目前的 commit 版本是多少

## 创建分支

```
git branch <branch-name>
```

创建之后可以再次通过 `git branch -v` 来查看分支情况，新创建的分支一开始是和父分支的版本一样，所以版本号也一样

## 切换分支

```
git checkout <branch-name>
```

在新的分支修改完，并且 commit 之后，再切回 master，可以发现 master 中的内容并没有受到影响

## 合并分支

```
git merge <branch-name>
```

这个指令会把 `<branch-name>` 的分支，合并到当前所在分支上

合并的时候有可能会出现冲突，产生原因是当两个分支在同一个文件的同一个位置，有两种不同的修改，git 没办法判断留哪一个版本，这时候需要人为去干预。

注意：相邻两行的修改也会算作 conflict，算是 git 特性，具体参考以下链接

>https://stackoverflow.com/questions/29276880/why-does-git-produce-a-merge-conflict-when-lines-next-to-each-other-are-changed

# 团队协作

团队协作分为两种

1. 团队内协作：所谓团队内协作，也就是在一个团队内，每一个成员都可以直接从远程库中 clone 代码到本地库中，然后在对其修改，最后 push 到远程库中，团队内的其他成员也可以通过 pull 来更新最新的代码
2. 跨团队向协作：这个就是两个团队相互独立，其中一个团队 A 可以从另一个团队 B 的远程库中 fork 一份代码到团队 A 的远程库中，相当于一份副本，然后团队 A 的成员从自己的远程库中，也就是团队 A 的远程库中 clone 一份到本地库，进行修改，最后 push 到团队 A 的远程库中；接下来，团队 A 需要向团队 B 发送一个 `Pull Request`，请求团队 B 去 pull 新的代码，如果团队 B 经过审核，觉得 OK，就可以直接 merge 了

# 远程库操作

## 查看当前所有远程库

```
git remote -v
```

## 添加跟踪的远程库

```
git remote add <name> <url>
```

本地库的文件夹名字尽可能与远程库名字保持一致，主要这里的文件夹名字不是上面的 `<name>`，在 add 之后还可以设置很多参数，具体参考官方文档

注意：这个 name 是给远程库的一个别名

## 推送

```
git push [<repository> [<refspec>]]
```

第一个 `respository` 是要推送的远程库的名字，之前在添加的时候起的名字；`refspec` 是要被推送的本地库的一个特定的分支名

## 拉取

```
git pull [<repository> [<refspec>]]
```

第一个 `respository` 是要拉取的远程库的名字；`refspec` 是指本地要接受拉取的本地库的一个分支

## 克隆

```
git clone <url> [-o <name>]
```

这里 `-o` 可以修改默认的远程库的别名、或者说是名字，默认叫 origin，可以修改为其他的自己想要的

>Instead of using the remote name `origin` to keep track of the upstream repository, use `<name>`. Overrides `clone.defaultRemoteName` from the config.

## SSH 免密登陆

（默认还没有 .ssh 文件夹）
使用以下指令创建秘钥：

```
ssh-keygen -t rsa -C <email>
```

然后一路回车，去查看 `.ssh` 目录下的 `id_rsa.pub` 内容

```shell
cat ~/.ssh/id_rsa.pub
```

然后去 github 的账号设置中，找到 `SSH and GPG keys`，在里面添加 SSH keys

# IDEA 继承 Git

## ignore

要求 git 去忽略某些文件的追踪，是可以通过以 `.gitignore` 为后缀的文件来进行设置。

当 git 决定是否忽略某个文件或路径的时候，是按照一下 gitignore 文件的顺序来判断，从高到低：
1. 命令行中的 pattern
2. 项目内中的 pattern，并且越深的目录层级中的 gitignore 文件优先级越高
3. `$GIT_DIR/info/exclude`（不太懂）
4. 全局的`core.excludesFile`，可以通过 `git config --global -e` 打开进行设置，注意一点！全局配置的这个 ignore 文件是以 `.ingore` 结尾的！


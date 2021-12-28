---
creation date: 2021-12-28 00:09:59
last modified: 2021-12-28 07:47:56
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

```
git reset --hard <commit>
```

这个 commit 参数就是版本号
>--hard 参数说明
>Resets the index and working tree. Any changes to tracked files in the working tree since `<commit>` are discarded. Any untracked files or directories in the way of writing any tracked files are simply deleted.
>
>https://git-scm.com/docs/git-reset


---
creation date: 2022-03-03 00:10:39
last modified: 2022-03-03 02:17:24
title: Shell
categories:
- linux
tags:
- linux
- shell
- bash
---

# Shell 快速入门

根据视频 https://www.youtube.com/watch?v=hwrnmQumtPw 整理

## 文件格式
文件以 `#!/bin/bash` 开头，第一行必须是这个
`#` 用来注解

```shell
#!/bin/bash
```

## 运行
直接通过 `./demo.sh` 的方式就可以运行，如果报错，需要通过 chmod 命令来使其变成可执行文件

```shell
chmod 755 ./demo.sh
```

其中第一个数字代表 the owner of the file 的权限
（ `7` 在这里就是代表可读、可写、可执行） 
第二个数字代表 group 的权限
第三个数字代表 everyone 的权限
（ `5` 代表可读可执行）

其他数字
6：读 + 写
4：读
3：写 + 执行
2：写
1：执行
0：啥也做不了

## 变量
变量可以以 **字母** 或者 **下划线** 开始
给变量赋值的等号两边不可以有空格

声明常量通过 `declare -r` 的方式声明

```shell
declare -r NUM=10
```

## 整数运算
变量与变量之间、或变量本身（自增、自减）操作需要在 `$(())` 之间完成，或者通过 `let` 完成，`$(())` 必须要有变量来接收结果，或者直接打印 

```shell
declare -r NUM_1=10
declare -r NUM_2=10
sum=$((NUM_1+NUM_2))

let sum=NUM_1+NUM_2
let sum++
```

变量之间的操作支持 `+=` ，`-=` 等操作（注意在前面要使用 `let` 关键词）

```shell
rand=10
let rand+=5
```

## 浮点数
浮点数的运算可以借助 python 和 `$()` 来实现，其中 `$()` 中的命令会被先执行，并且把结果赋给一个变量

```shell
num_1=3.14
num_2=4.96
sum=$(python -c "print $num_1+$num_2")
```

## 输出
通过 `echo` 关键词可以输出内容，输出过程中想获取某变量值，通过 `$VAR` 的方式来获取

```shell
num=10
echo $num
```

也可以输出带有运算的内容

```shell
echo $((5**2))
```

打印多行内容，可以通过 `cat` 指令

```shell
cat << XXX
Hello World!
I Love Shell!
XXX
```

（这个 XXX 用什么名字都可以，只要他俩一样）

# Vim 相关操作

d: 删除行

0: 行首

$: 行位

Shift + V: 选择本行

y: 复制所选内容

P: 在当前行粘贴，且当前行原本内容向下移动一行

p: 在当前行的下一行粘贴，若当前行的下一行以后内容，则向下移动一行
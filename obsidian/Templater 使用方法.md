---
creation date: 2021-12-14 17:32
title: Templater 使用方法
categories:
- obsidian
tags:
- obsidian教程
---

# 术语

1. 一个 `template` 是一个包含 `commands` 的文件
2. 一个以 `<%` 开头，以 `%>` 结尾，且中间包含很多 `variable/function` 的文本称为 `command`

有两种 variables/functions 我们可以使用：
1. 内置 variables/functions，这些都是插件提前定义好的，比如 `tp.file.title`
2. 用户自定义 functions，用户可以在插件设置中设置 functions

# 语法
只需要注意一点，所有的 variable 和 functions 都是在 `tp` 对象的 children

# 模块

一共 7 个模块，具体使用方法参考官方文档：
> https://silentvoid13.github.io/Templater/docs/internal-variables-functions



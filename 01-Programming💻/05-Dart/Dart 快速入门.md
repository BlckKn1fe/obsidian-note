---
creation date: 2022-01-19 21:44:07
last modified: 2022-01-19 22:18:53
title: Dart 快速入门
categories:
- dart
tags:
- dart
---

# 变量

Dart 可以不预先定义变量类型，用 `var` 来声明，Dart 自己会推断；Dart 也可以直接给一个变量类型。如果一开始用 `var` 声明一个变量为字符串类型，后面又修改为整数类型的话，它会报错。

## final 和 const

这俩都可以用来声明一个常量，const 在一开始的时候就直接把值限制死了，后面不可以修改；而 final 一开始的时候可以不给一个变量赋值，后面再赋值也可以

```dart
final a = new DateTime.now();

// const b = new DateTime.now();  这个过不了编译
```

还有就是，即使用不同的变量 point to 一个被 const 修饰的对象的时候，也无法修改这个对象，无论是 const 在前还是在后

```dart
const arr_1 = [1, 2, 3, 4];
var arr_2 = const [1, 2, 3, 4];

var temp = arr_1;
// temp.add(5)  报错
```

## 字符串

声明字符串声明支持三个单引号的方式。

字符串的拼接可以通过 `$`，也可以通过 `+` 实现：

```dart
String str_1 = "Hello";
String str_2 = "World!";
// 通过 $ 获取变量值
print("$str_1 $str_2");
// 通过 + 拼接
print(str_1 + " " + str_2);
```

## 集合 List

用 `[]` 中括号表示的为 List 类型，它内置了很多方法，获取元素直接通过 index 来获取。

泛型的使用如下：

```dart
var arr = <int>[1, 2, 3, 4];
// 固定大小
var arr_fix = List<String>.filled(5, "");
```



还有一种方式可以创建固定大小的集合：

```dart
var arr = List.filled(3, "");
```

这里第一参数为该集合大小，第二个参数是这个集合里的元素都是什么。比如第一个参数是 3，第二个参数是数字 0，那么这个大小为 3 的集合的三个元素就全都是 0



## Map

Map 的第一种定位方式和 JSON 基本一样

```dart
var person = {
    "name" : "Tom",
    "age" : 20,
    "hobbies" : ["台球", "乒乓球", "游泳"]
}
```

获取方式和 Python 中一样：

```dart
print(person["name"]);
```

还有第二种方式如下：

```dart
var person = new Map();
person["name"] = "Tom";
// ...略
```



## 类型判断

通过 `is` 关键词判断

```dart
int a = 10;
if (a is int)
    print("int type");
else
    print("other types");
```



## 其他

还有个 Runes 和 Symbols 数据类型，作为了解

Runes:

https://dart.dev/guides/language/language-tour#characters

Symbols:

https://dart.dev/guides/language/language-tour#symbols



# 运算符

**算数运算符**只有一个需要注意，余整是 `~/`

**关系运算符**（略）

**逻辑运算符**（略）

**赋值运算符**需要单独说一下 `??=`，表示当被赋值的变量如果为 null 的话，再付给它一个值：

```dart
int b = 10;
b ??= 20;
print(b);
```

上面这个情况就是，如果 b 为 null 的话，才给 b 赋值 20，所以打印结果为 10



**条件表达式**：

1. 支持 switch 语句，用法和 Java 一样

2. 支持三目运算符

3. `??` 运算符：表示当某个变量为空的时候，赋一个值给变量

   ```dart
   var a;
   var b = a ?? 10;
   // 若 a 为 null，则把 10 赋给 b
   ```

4. 










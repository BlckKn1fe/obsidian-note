---
creation date: 2022-01-19 21:44:07
last modified: 2022-01-29 07:58:10
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

**赋值运算符**需要单独说一下 `??= `，表示当被赋值的变量如果为 null 的话，再付给它一个值：

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

# 类型转换

Number to String: 用 `toString()` 方法

String to Number: 

1. 使用 `int.parse()` 方法
2. 使用 `double.parse()` 方法

注意一点，String to Number 的时候，如果 String 是空，则会报错，要用 try/catch

# 集合

## List 集合

集合常用属性：

1. `length`
2. `isEmpty`
3. `isNotEmpty`
4. `reversed`：这个会直接给一个反转后的可迭代对象（注意这不是方法）可以调用 `toList()` 方法得到 List 集合

常见方法：

1. `add`
2. `addAll`：拼接数组（追加）
3. `indexOf`
4. `remove`：参数为具体值
5. `removeAt`：参数为索引值
6. `fillRange`：在一个数组范围里填充元素，前两个参数为范围，第三个参数为值
7. `insert(index, value)`
8. `insertAll(index, list)`
9. `join`

## Set 集合（略）

## Map 集合

定义方式和 JSON 对象基本一样

```dart
var person = {
    "name" : "张三",
    "age" : 20
};

var user = new Map();
user["username"] = "Lisi";
user["password"] = 123456;

```

常用属性：

1. `keys`：获取所有 key（可迭代对象，需要用 `toList()`)
2. `values`：获取所有 value（可迭代对象，需要用 `toList()`)
3. `isEmpty`
4. `isNotEmpty`

常用方法：

1. `addAll()`：参数为 map 对象

   ```dart
   person.addAll({"height": 180});
   ```

2. `remove`

3. `containsValue`

## 可迭代对象

1. `forEach`：迭代
2. `map`：把一个 function apply 到每一个元素上
3. `where`：用条件筛选
4. `any`：是否有任意元素满足条件并且返回一个 bool
5. `every`：是否所有有元素满足条件并且返回一个 bool

# 函数

## 箭头函数

箭头函数只能写一句话

# Class

## 属性

```dart
class Person {
    String name;
    int age;
}
```

如果要把属性私有需要满足两个条件：

1. 属性前加一个下划线 `_`
2. 对象抽取成一个单独的文件

（方法私有也是加下划线）

## 构造函数

```dart
// 写法 1
Person(String name, int age) {
    this.name = name;
    this.age = age;
}

// 写法 2
Person(this.name, this.age);

// 写法 3 - 具名构造函数
Person.nameAndAge(String name, int age) : this(name, age);
```

## get/set

这东西有点像 Vue 里的 compute attribute，用法大概如下

```dart
class Rect {
    num length;
    num width;
    Rect(this.length, this.width);
    get area { return this.length * this.width; }
}
```

想获取 area 的时候，直接作为属性的方式获取到

```dart
Rect r = new Rect(2, 10);
print(r.area);
```

所以当给私有的属性写 get 的时候，就可以这样写（这里以 User class 为例）

```dart
class User {
  String _username;
  String _password;

  User(this._username, this._password);

  String get username => _username;
  // 或者 String get username { return _username; }

  set username(String value) {
    _username = value;
  }
}

```

## 静态

和 Java 用法一毛一样

## as 操作符

这就是拿来强转用的，比如一个变量，用法大概如下：

```dart
(obj as Person).showName();
```

obj 作为 Person 对象来调用 Person 对象中的 showName 方法

## 级联操作

有点链式调用的意思，以下两段代码等效

```dart
// 1 常规写法
Person p = new Person("Zhang San", 23);
p.showInfo();
p.name = "Li Si";
p.age = 24;

// 2 级联写法
Person p = new Person("Zhang San", 23);
p.showInfo();
p..name = "Li Si"
 ..age = 30
 ..ShowInfo();
```

## 继承

基本和 Java 一毛一样

1. 使用 extends
2. 继承可见属性和方法，构造函数不会被继承
3. 可以复写 get / set 方法

注意：和 Java 一样，父类也有和 Java 类似的 super 内存区

## 抽象类和接口

都用 abstract 关键词来实现，用起来和 Java 一毛一样

## Mixin

这东西有点处于 implement 和 extends 之间

1. 参与 mixin 的类不允许有构造函数，且为正常的 class
2. 若出现重名的情况，with 关键词后面的会覆盖前面的，extends 的也会被覆盖！！
3. extends 可以和 with 同时存在

Mixin：直接把写好的方法拿来用

Extends：继承，方法可以选择重写，也可以选择直接用父类写好的

Implements：所有方法都得自己写

如果 A Mixin 了 B 和 C，那么 `A is B` 或者 `A is C` 都是 True

# 库

安装外部的库，通过：https://pub.dev/

在目录下创建一个 `pubspec.yaml` 文件

```yaml
name: demo
description: Demo

environment:
  sdk: '>=2.14.0 <3.0.0'

dependencies:
  http: ^0.13.4

```

如果 IDE 支持的话，一 save 他就会自己安装，不支持的话就使用

```
pub get
```

库撞名的话用 as 关键词给个别名

show 关键词可以引入库中特定方法

hide 关键词可以隐藏库中特定方法

deferred 关键词开启懒加载


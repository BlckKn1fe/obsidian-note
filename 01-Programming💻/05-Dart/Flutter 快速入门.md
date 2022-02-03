---
creation date: 2022-01-29 07:58:35
last modified: 2022-02-02 21:51:03
title: Flutter 快速入门
categories:
- flutter
- mobile
tags:
- dart
- flutter
---

# 快速开始

## 入口

入口为 `lib/main.dart`，且必须引入：

```dart
import 'package:flutter/material.dart';
```

在 main 中调用 runApp(Widget) 的方式开启动项目

## 自定义 Widget

这里暂时用 `StatelessWidget` 举例

```dart
class AnotherWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: Text("Hello World!"),
    );
  }
}
```

`StatelessWidget` 为一个抽象类，继承后必须实现 build 方法，并且 return 的结果需要是一个 Widget

## MaterialApp 和 Scaffold

其中 MaterialApp 可以看做一个大的页面组件，里面有很多属性给我们设置，比如要设置 title，设置 home，设置 theme 等等

```dart
class AnotherWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Hello Demo",
      home: Scaffold(
        appBar: AppBar(title: Text("Hello World!")),
        body: Center(child: Text("Hello Flutter"),)
      ),
      theme: ThemeData(primarySwatch: Colors.blue),
    );
  }
}
```

# 组件

## Container 组件

这就和 div 有点像，里面的常见属性有

1. height
2. width
3. decoration：比如可以用 BoxDecoration 来创建盒子模型，设置背景颜色，边框等等
   1. color
   2. border
   3. borderRadius：这个的参数是 BorderRadius.all(Radius.circular(double d))
4. alignment
5. margin：同下
6. padding：通过 EdgeInsets 来设置，可以设置 all 也可以设置 4 个方向

## Text 组件

这个可以设置的属性非常多

1. style：用 TextStyle 对象来修饰，可以设置颜色、大小、粗细
   1. fontsize
   2. color
   3. fontWeight
   4. fontStyle
   5. decoration
   6. decorationColor
   7. wordSpace
2. textAlign
3. overflow
4. textScaleFactor

## 图片组件

引入图片可以通过引入本地图片或者引入网络图片两种方式

可以通过 `Image.asset` 和 `Image.network` 两种方式

图片可以设置的属性：

1. scale: 数值越小图片越大
2. alignment: 设置图片在容器内的位置
3. color / colorBlendMode: 这俩要一起用
4. fit: 图片的填充方式

实现圆形图片有两个方法

1. 利用容器的特性，给一个容器设置背景图片装饰，然后修改容器的半径

   ```dart
   @override
   Widget build(BuildContext context) {
       return Center(
           child: Container(
               width: 255.0,
               height: 255.0,
               decoration: BoxDecoration(
                   borderRadius: BorderRadius.circular(20.0),
                   color: Colors.red,
                   image: const DecorationImage(
                       image: NetworkImage("url"),
                       fit: BoxFit.fill
                   )
               )
           )
       );
   }
   ```

   

2. 使用 `ClipOval` 组件：

   ```dart
   @override
   Widget build(BuildContext context) {
       return Center(
           child: Container(
               child: ClipOval(
                   child: Image.network(url),
               )
           )
       );
   }
   ```

   ClipOval 也可以设置高度宽度等参数

当配置本地图片的时候，需要在配置文件里配置 asset 目录路径

```yaml
flutter:
	assets:
		- assets/images/demo.png
		- assets/images/2.0x/demo.png
```

## 垂直列表组件

组件为 `ListView` 组件，里面可以用 `ListTile` 塞东西

```dart
class HomeContent extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    return ListView(
      children: const <Widget>[
        ListTile(
            // 设置在把头的内容
            leading: Icon(Icons.airplanemode_active),
            title: Text("你好你好你好"),
            subtitle: Text("Hello World!")
        ),
        ListTile(
            leading: Icon(Icons.accessible_forward_outlined),
            title: Text("你好你好你好"),
            subtitle: Text("Hello World!")
        ),ListTile(
            title: Text("你好你好你好"),
            subtitle: Text("Hello World!")
        ),
      ],
    );
  }
```

其中 trailing 是设置放在末尾的图片或者 Icon

ListView 里也可以塞任何其他的组件（只要泛型那写 Widget 就可以）

## 水平列表

水平列表就是把 ListView 的 `scrollDirection` 属性修改成 `Axis.horizontal`

在做水平的时候，由于 ListView 中的元素单独设置高不起作用，所以我们可以在 ListView 外面包一个 Container，然后设置 Container 的高度就可以限制一个水平的 ListView 的高度

Container 和 ListView 之间可以互相嵌套实现想要的效果



## GridView 组件

 通过 `GridView.count()` 来创建网格视图

```dart
@override
  Widget build(BuildContext context) {
    return GridView.count(
      // 设置有多少列（或者说一行展示多少个元素）
      crossAxisCount: 3,
      children: const <Widget>[
        Text("Hello"),
        Text("Hello"),
        Text("Hello"),
        Text("Hello"),
      ],
    );
  }
```

一般它可以配合一个自定义函数，然后把数据都丢进 Container 里，然后一个 List 装这些 Container

```dart
List<Widget> _getListData() {
    List<Widget> list = [];
    for (int i = 0; i < 20; i++) {
        list.add(Container(
            alignment: Alignment.center,
            child: Text(
                "This is ${i}th data",
                style: const TextStyle(color: Colors.red, fontSize: 20)
            ),
            color: Colors.blueGrey,
        ));
    }
    return list;
}
```

然后直接在 GridView 的 children 里调用这个函数就行了

常见的 GridView 可以设置的属性有：

1. crossAxisCount: 设置有多少列
2. crossAxisSpacing: 设置**横向**各个元素之间的距离
3. mainAxisSpacing: 设置**纵向**各个元素之间的距离
4. childAspectRatioe: 设置元素们的宽高比，比值越小元素纵向越长，这里是宽比高



GridView 里还有一个 builder 的方法，注意事项见注解：

```dart
@override
Widget build(BuildContext context) {
  return GridView.builder(
    padding: const EdgeInsets.all(5.0),
    // gridDelegate 是必填属性，主要控制元素之间布局的
    gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
      // crossAxisCount 也是必填属性，控制列数
      crossAxisCount: 3,
      crossAxisSpacing: 5.0,
      mainAxisSpacing: 5.0,
    ),
    itemCount: 20,
    itemBuilder: (context, index) {
      return _list[index];
    }
  );
}
```



## Padding 组件

Flutter 中有的组件没有 padding 属性，我们可以通过 Padding 组件来控制组件与其子元素之间的间距

这组件用起来和 Container 基本一毛一样，里面设置两个属性

1. padding: 设置内边距
2. child: 把要设置 padding 的组件写这里就可以了



## Row 水平布局插件

这个和 ListView 的区别在于，元素本身多大，它就给显示多大，不会像 ListView 一样把元素铺满

```dart
@override
Widget build(BuildContext context) {
  return Row(
    children: <Widget>[
      IconContainer(Icons.home),
      IconContainer(Icons.search, color: Colors.blue),
      IconContainer(Icons.fingerprint, color: Colors.pink),
    ],
  );
}
```

有几个常用属性可以设置：

1. mainAxisAlignment: 设置元素在一行中所在位置，比如选 center，三个元素就会挨着居中；如果选了 spaceEvently，那元素们会均匀分散开，保证元素与元素之间，和元素与边框之间的距离是一样的
2. crossAxisAlignment: 这个是设置元素们相对它外层，在纵轴上的位置。不设置的话默认水平居中



Column 用法和 Row 一毛一样，不重复了



## Expanded 组件

这个就是用来实现 flex 布局效果用的，用法和 Padding 一样，提供 flex 和 child 两个属性

```dart
Expanded(
  flex: 2,
  child: IconContainer(Icons.home)
)
```



## Stack 层叠组件

Stack 组件与 Align 或 Positioned 组件一起使用来实现相对定位布局，丢进 Stack 里的组件会全都堆叠在 z 轴上。另外，丢进 Stack 元素的顺序会影响 z 轴优先级

```dart
// 结合 Align 组件实现定位
@override
Widget build(BuildContext context) {
  return Center(
    child: Container(
      height: 400,
      width: 300,
      color: Colors.pink,
      child: Stack(
        children: [
          Align(
            // 这里可以写 Alignment(double x, double y) 来实现精确定位
            // 值为 -1 ~ 1 之间
            alignment: Alignment.center,
            child: Icon(Icons.home, size: 40,),
          ),
          Align(
            alignment: Alignment.bottomCenter,
            child: Icon(Icons.home, size: 40, color: Colors.white),
          ),
          Align(
            alignment: Alignment.bottomRight,
            child: Icon(Icons.home, size: 40, color: Colors.blue),
          )
        ],
      ),
    ),
  );
}
```

Positioned 和 Align 差不多，只不过变成了要设置上下左右四个属性来实现定位



## AspectRatio 组件

用法还是和 Padding 很类似，可以设置元素相对其父元素的宽高比（也可以单独拿出来使用然后做图片平铺）



## Card 组件

就是个做出来像卡片一样的组件...



## Wrap 组件

这东西简单来说就是 X 轴放不下了，可以自动换行继续放

1. spacing: 元素横向的间距
2. runSpacing: 元素纵向的间距
3. alignment: 简单来说就是所有元素在换行之后的对齐方式，通过 WrapAlignment 来设置
4. runAlignment












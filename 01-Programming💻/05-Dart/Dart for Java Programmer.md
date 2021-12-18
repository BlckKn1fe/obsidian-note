---
creation date: 2021-12-17 02:36:19
last modified: 2021-12-18 00:14:52
title:
categories:
- front-end
tags:
- dart
---

Use code as reference to understand how is the dart syntax, features of dart, key points of dart, etc. Almost all code is from the official code lab. I add the link at the beginning of each part. The official site provides a lot of condensed information in **Observations** section. Highly recommend to read it.

# Declare a Class

> https://developers.google.com/codelabs/from-java-to-dart#1

In a dart file, all variables and functions can be written inside or outside of the class. Below is a demo of decalring a class. The class declares some fields and functions with comments.

```dart
class Bicycle {
  // Constructor for class
  Bicycle(this.cadence, this.gear);

  int cadence;
  // The name of the field starting with "_" will be forced as a private field in the file where the class is declared
  int _speed = 0;
  int gear;

  // getter or accessor for field
  int get speed => _speed;

  // function to change the private field
  void applyBrake(int decrement) => _speed -= decrement;
  void speedUp(int increment) => _speed += increment;

  // The way to override toString function
  @override
  String toString() => "cadence: ${cadence} speed: ${_speed} gear: ${gear}";
}

void main(List<String> args) {
  var bike = Bicycle(2, 1);
  print(bike);
  bike.speedUp(10);
  print(bike);
  bike.applyBrake(5);
  print(bike);
}
```

Notice:

1. Each variable (even if it's a number) must either be initialized or be declared nullable by adding `?` to its type declaration.
2. Use string interpolation to put the value of an expression inside a string literal: `${expression}`. If the expression is an identifier, you can skip the braces: `$variableName`. (But I recommand include the braces)

# Optional Named Parameters

>https://developers.google.com/codelabs/from-java-to-dart#2

This is the way for dart to overload a function.

```dart
import 'dart:math';

class Rectangle {
  Rectangle({
    this.origin = const Point(0, 0), 
    this.width = 0, 
    this.height = 0
  });

  int width;
  int height;
  Point origin;
  
  @override
String toString() =>
      'Origin: (${origin.x}, ${origin.y}), width: $width, height: $height';
}

main() {
  // we can assign any new value to replace the default value used in the 
  print(Rectangle());
  print(Rectangle(width: 29));
  print(Rectangle(origin: const Point(10, 10)));
  print(Rectangle(origin: const Point(10, 20), width: 100, height: 200));
}

```

Notice:

We can also write the constructor in the following way:

```dart
Rectangle(this.origin, {this.width = 0, this.height = 0});
```

For creating a Rectangle obeject:

```dart
var r1 = Rectangle(Point(4, 5));
var r2 = Rectangle(Point(4, 5), width: 20);
```

# Factory

> https://developers.google.com/codelabs/from-java-to-dart#3

```dart
import 'dart:math';

abstract class Shape {
  // factory is a keyword in dart
  factory Shape(String type) {
    if (type == "circle") return Circle(2);
    if (type == "square") return Square(2);
    throw "Can't create ${type}.";
  }
  
  num get area;
}

class Circle implements Shape {
  Circle(this.radius);
  final num radius;

  // implements the function declared in Shape
  @override
  num get area => pi * pow(radius, 2);
}

class Square implements Shape {
  final num side;
  Square(this.side);

  // implements the function declared in Shape
  @override
  num get area => pow(side, 2);
}

main() {
  final circle = Shape("circle");
  final square = Shape("square");
  print(circle.area);
  print(square.area);
}
```

# Functional Programming

not functional-style to print a list of result:

```dart
String scream(int length) => "A${'a' * length}h!";
main() {
  final values = [1, 2, 3, 5, 10, 50];
  for (var length in values) {
    print(scream(length));
  }
}
```

functional-style:

```dart
values.map(scream).forEach(print);
```


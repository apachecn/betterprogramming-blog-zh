# 过程编程和面向对象编程的区别

> 原文：<https://betterprogramming.pub/why-you-should-now-the-difference-between-procedural-style-coding-and-object-oriented-style-coding-f9e25d6d0470>

## 通往干净和可维护代码的踏脚石

![](img/678e8159f8738f25d7d5015a2c0e2db1.png)

[困惑](https://pixabay.com/photos/confused-muddled-illogical-880735/)

# 前言

软件行业的大多数人使用主要为面向对象编程(OOP)设计的编程语言。虽然用面向对象的方式编码是完全有效的，但了解多种编程范例可能会派上用场。

# 介绍

在本文中，OOP 与过程式编码形成了对比。此外，这篇文章解释了为什么在某些情况下您可能想要使用过程式编码。如果您喜欢只阅读代码，可以直接跳到文章的底部。在那里你可以找到 GitHub 上代码的链接。

# 什么是面向对象编程(OOP)？

OOP 是一种软件设计范式，其中程序是围绕对象和数据结构而不是功能和逻辑来构建的。经典的 OOP 编程语言有 Python、Java、C#等等。

## 例子

假设您被要求创建一个程序来计算圆、正方形和矩形的面积。要求是用 OOP 风格写代码。你会怎么做？请随意写一些伪代码或尝试一下并编码它！

## 暗示

1.  你的程序结构会是什么样的？
2.  你的程序有多少个类和方法？
3.  这些类和方法是如何组织的？

**一种可能的解决方案:**

```
// Data Object Asymetry
// Object Oriented example
```

```
case class Square(var side: Double = 0) { println(side * side)}case class Rectangle(var height: Double = 0, var width: Double = 0) { println(height * width)}case class Circle(
    var pi: Double,
    var center: Double = 0,
    var radius: Double = 0
) { println(pi * radius * radius)}object Geometry extends App { val pi = 3.141
  val side = 2.0
  val height = 1.0
  val width = 2.0
  val center = 2.0
  val radius = 0.5 Square(side)
  Rectangle(height, width)
  Circle(pi, center, radius)}
```

该程序设计有封装数据和功能的类。每个类对象都是相互独立的。这使得添加新函数变得困难，因为所有的类都需要改变。然而，添加新的类是很容易的。例如，如果我想打印每一个周长`Shape`，这样的函数需要单独添加到每个类中。然而，添加类`Trapeze`将是一个简单的任务。换句话说，如果我们添加一个类，我们只在一个地方编辑代码。如果我们添加一个函数，我们必须至少在三个地方编辑代码。

# 什么是过程式编码？

过程式代码是一种软件设计范例，其中程序的逻辑被置于程序设计的中心。一些主要的过程语言是 Fortran、COBOL 和 C#，等等。

## 任务—程序示例

接下来，你将如何在程序编码中实现`Geometry`应用？思考与 OOP 示例提示中相同的问题。

## 可能的解决方案:

```
// Data Object Asymetry
// Procedual example
```

```
case class Square(var side: Double = 0) {}case class Rectangle(var height: Double = 0, var width: Double = 0) {}case class Circle(var center: Double = 0, var radius: Double = 0) {}object Geometry extends App { val pi = 3.141
  val square = Square(2.0)
  val rectangle = Rectangle(1.0, 2.0)
  val circle = Circle(1, 0.5)
  val shapes = List(square, rectangle, circle) def hasArea(shape: Any): Unit =
    shape match { case Square(side) => println(side * side) case Rectangle(height, width) => println(height * width) case Circle(center, radius) => println(pi * radius * radius) case _ => println("Not a Shape") } shapes.foreach(shape => hasArea(shape))}
```

## 说明

同样，我们为所需的各种形状创建了 case 类。但是，这些 case 类不包含任何功能。任何方法都是在程序体中顺序实现的。在过程式编码中，很容易添加新的函数。然而，添加新的类更加复杂。

例如，在上面的程序中，如果我们添加一个新的函数'`hasCircumfrance`,代码只需要在一个地方编辑。但是，如果我们想添加另一个类，我们必须至少编辑代码中的两个地方。体内的每一个函数都需要改变，再加上程序顶部的类定义需要添加。

## 什么时候以及为什么要使用这两种方法？

您可能已经注意到了这两种编程风格的不对称本质。要么很难添加新的数据结构，因为所有的功能都需要改变。或者很难添加新的功能，因为所有的类都需要改变。

要编写干净且可维护的代码，您应该了解这两种方法。在某些情况下，您希望能够轻松地添加功能，而在另一个用例中，您希望轻松地添加类。在写代码之前，你应该问问自己哪种风格更好。

编码风格高度依赖于一个人试图解决的用例或业务用例。在几何示例中，如果我期望永远不添加任何新的`Shapes`，而是添加许多函数，我将使用过程化风格编写程序。另一方面，如果我预计随着时间的推移会添加更多的形状，而函数会保持不变，我会选择用 OOP 风格编写程序

# 关键外卖

罗伯特·c·马丁(Robert C. Martin)在他的名著《敏捷软件工艺手册》(A Handbook of Agile Software craftness)中写道:“成熟的程序员知道，一切都是对象的想法是一个神话。有时你真的想要简单的数据结构和在其上操作的程序。最后，知道你想用什么样的风格来编程你的应用程序将会极大地增强这个应用程序的可维护性。

# 结束语

感谢您阅读这篇文章。希望你能从中得到一些有价值的东西。请随意添加评论、问题或备注。祝你一周愉快！:)

## GitHub:

[https://github . com/DataBach-maker/Scala examples/tree/main/OOP _ vs _ PSC](https://github.com/DataBach-maker/ScalaExamples/tree/main/OOP_vs_PSC)

## 参考

R. C .马丁(2011 年)。*干净的代码——敏捷软件工艺手册*。皮尔森教育。
# TypeScript 中面向协议的编程可能吗？

> 原文：<https://betterprogramming.pub/protocol-oriented-programming-in-typescript-really-e4389cdc7842>

## 在您的 TypeScript 代码库中采用这种流行的 Swift 范例

![](img/132aa6c5b3a6d8fd7add94e7a9190087.png)

照片由 [Fotis Fotopoulos](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

# 面向协议的编程是 Swift 中一种常见的抽象方法

面向协议的编程是许多 Swift 程序员用来构建源代码的实践。这是帮助程序员将现实世界的概念建模为软件开发的计算对象的一种常见的抽象方法。既然抽象是面向对象编程中的一个概念，那么其他编程语言是否也有类似的特点呢？

在直接得出结论之前，让我们先看看是否可以在 TypeScript 中实现一个通用的`print()`函数来输出，就像 Swift 中的`CustomStringConvertible`协议一样。

# TypeScript 接口定义了对象的形状

`Interface`是一个关键字，可以用 TypeScript 对计算对象建模。首先，让我们定义两个接口(`ICustomStringConvertible`和`ICustomDebugStringPrintable`)，就像 Swift 中的协议声明一样:

上面的声明是告诉编译器，新增了两种带属性的接口(`description`和`debugDescription`)。然后，构造`print()`和`debugPrint()`来打印由`ICustomStringConvertible`和`ICustomDebugStringConvertible`接口形成的任何类。

# 抽象类定义了对象的类型

以上实现之后，下一步就是设计一个符合`ICustomStringConvertible`和`ICustomDebugStringConvertible`的抽象类。任何从某个抽象类继承的类都意味着这些类基于共享的特征，并被归类到具有相同方法和属性的同一类型中。

# 从抽象对象创建一个新类

现在，是时候把接口、泛型和抽象类放在一起了。新类别`AnObject`可创建如下:

现在让我们回到最初的目标:在 TypeScript 中为输出实现一个通用的`print()`函数，就像 Swift 中的`CustomStringConvertible`协议一样。让我们编写一些代码来检查结果:

# 最后的想法

面向协议的编程可能不是描述过去 TypeScript 中接口的最佳短语。然而，TypeScript 和 Swift 在抽象哲学上确实有一些相似之处。作为敏捷的程序员，采用 TypeScript 的学习曲线可能会更平滑，因为编码风格和经验可以互换。此外，抽象实现也非常有助于形成我们对面向对象编程的理解。
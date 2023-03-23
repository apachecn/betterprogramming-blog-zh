# 使用 TypeScript 进行面向对象编程的 4 个支柱

> 原文：<https://betterprogramming.pub/understand-object-oriented-programming-with-typescript-c4ff8afa40d>

## **封装**、**抽象**、**继承**和**多态**的例子

![](img/8af3949a1a6fb79c0ebf649525c9058b.png)

图片由[黑客资本](https://unsplash.com/@hackcapital?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 对 JavaScript 中面向对象编程的实现产生了重大影响。今天，我想通过给你介绍一些例子来谈谈 TypeScript 中的 OOP。

对了，我已经把所有的例子都上传到了 [Github](https://github.com/hellokvn/medium-oop-in-typescript) 上。检查一下，如果你有问题，请告诉我。

首先说一下 TypeScript 本身。

# 什么是 TypeScript？

TypeScript 是一种基于 JavaScript 的强类型编程语言，为您提供了更好的工具。

这是 JavaScript 的所谓“超集”。你可以说 TypeScript 是 JavaScript，但 JavaScript 不是 TypeScript。因为 TypeScript 被编译成 JavaScript，而 JavaScript 被编译成机器码。

为了展示它与 JavaScript 的视觉差异，让我们创建一个名为 Car 的类。一次用 JavaScript 编写，一次用 TypeScript 编写。

## JavaScript 中的汽车类

JavaScript (ES6)

## TypeScript 中的汽车类

以打字打的文件

如你所见，非常相似。您可以看到的唯一区别是 TypeScript 代码段中到处都有类型批注。我希望这给人一种打字稿的好印象。

# OOP 的四大支柱

有四个核心概念来定义 OOP。这些是封装、抽象、继承和多态。我知道你在想什么。当我开始编程时，我不知道它们是什么意思，但让我解释一下。

## 抽象

OOP 的第一个概念是抽象。OOP 中的抽象意味着只向类的用户公开必要的细节。潜在的一切都不重要。要在 TypeScript 中实现抽象，有多种方法:抽象类/方法、接口和类型。

我将向您展示一个基于抽象类的示例，因为抽象类是一种方法，因为您可以在私有属性和方法中定义 public，这在接口和类型中是不可能的。

正如你所看到的，除了我们为属性分配变量的构造函数之外，我们的抽象类`Character`只是一个定义类。在那种情况下，我们的类 Goblin 依赖于这个抽象类。

## 包装

OOP 的第二个概念是封装。封装是建立在隐藏数据的思想上的。这是我们限制访问特定属性或方法的地方。

在我们的例子中，property _name 是私有的。这意味着我们不能从类外部访问这个属性。为了访问这个私有属性，我们使用所谓的 getter 和 setter 方法。

## 遗产

继承是一个引人注目的特性，它允许您拥有代码可重用性。假设您有一个现有的类，并且您构建了一个新的类，它使用了该现有类的属性或方法，同时您想向它添加额外的特性。那是继承发生的地方。你通常称这些类为子类、子类或低级类。这些类从它们的上层类扩展而来，通常称为超类、基类或父类。

在下面的例子中，你会看到我们的`Orc`类将通过扩展从`Character`类继承而来。

## 多态性

多态性是指创建一个具有多种形式的类的能力。或者换句话说，类有相同的方法，但不同的实现。

在我们的例子中，我们有一个父类(`Character`)和一个子类(`Orc`)。子类正在覆盖其父类的属性和方法。这就是多态性。

感谢您阅读我关于 TypeScript 中 OOP 的文章。我希望我能更新你的知识。别忘了，我已经在 [GitHub](https://github.com/hellokvn/medium-oop-in-typescript) 上传了所有的例子。

干杯！

我希望你喜欢读这篇文章。如果你愿意支持我成为一名作家，可以考虑注册[成为一名媒体成员](https://medium.com/@hellokevinvogel/membership)。每月只需 5 美元，你就可以无限制地使用 Medium。

想支持我？给我买杯咖啡。

# 接下来阅读

[](/higher-order-functions-in-typescript-cae8ad36bd56) [## TypeScript 中的高阶函数

### 举例说明打字稿中的 HOF

better 编程. pub](/higher-order-functions-in-typescript-cae8ad36bd56)
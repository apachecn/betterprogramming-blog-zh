# 在 Ruby 中实现单例设计模式的初学者指南

> 原文：<https://betterprogramming.pub/design-pattern-1-singleton-308c896801d7>

## 设计模式#1

![](img/5ea28276162ca101fa683546cc03bb0e.png)

Henrik dnnestad 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当使用面向对象编程时，你有没有想过为什么我们总是要创建一个类的实例？有时，基于用例，我们可能只需要一个实例，它可以在初始化后全局使用。

我见过这样的情况，人们声明一个类的实例，在项目启动时将它赋给一个全局变量，并在整个项目中使用它。除了使用全局变量，还有更好的方法吗？是的，单例类！

Singleton 是一种创造性的设计模式，最初是四种设计模式的[帮的一部分。](https://www.journaldev.com/31902/gangs-of-four-gof-design-patterns)

# 什么是单例设计模式？

Gangs of Four 收集了《设计模式:可重用面向对象软件的元素》一书中的 23 种设计模式。正如书中提到的，模式可以分为三个主要类别:创造的、行为的和结构的。单例模式是一种创造性的模式，因为它处理对象的创建。

该模式基本上限制了类的初始化，以确保在整个应用程序中只能创建和使用该类的一个实例。

## 单一模式的用例

如前所述，单例模式确保了一个类的整个实现都围绕着唯一的实例，这在设计某些用例时非常有用。

在很多情况下，这种模式非常有用，但是我在下面强调了三种最广泛使用的用例:

1.  日志服务
2.  缓存服务
3.  数据库服务

在上述所有情况下，在项目初始化期间不需要定义单个实例，并且可以通过预定义的实例访问该类的方法，该实例可以在整个应用程序中用于执行定义的操作。因为在任何给定时间都只有一个类实例，所以节省了大量内存。

# 例子

案例 1:类的基本实现

在我们实现单例模式之前，让我们看看如何在整个应用程序中创建和使用一个类的实例。

如上所示，每当需要在类中执行一个动作时，可以创建 config 类的一个实例。

案例 2:单例模式

如上所示，您可以通过使用 singleton 来实现 Singleton 模式

虽然看起来很有趣，实现起来也很简单，但是 Singletons 也有一些缺点:

难以测试——由于 Singleton 类的构造函数是抽象的，您将不得不在单元测试期间模拟初始化。

需要小心处理多线程情况

## 不符合标准—不能一次解决一个问题

**参考文献**

[单例设计模式|实现细节&代码](https://www.youtube.com/watch?v=oeQWxhlnCHM)

[TutorialsPoint — Singleton](https://www.tutorialspoint.com/design_pattern/singleton_pattern.htm)

[Ruby Singleton](https://github.com/ruby/ruby/blob/master/lib/singleton.rb)

# **Cons of Using Singleton**

Though it looks fun and simple to implement, Singletons come with a few cons too:

1.  Difficult to test — As the constructor of the Singleton class is abstracted, you will have to mock the initialization during unit testing.
2.  Need to handle multi-threading cases carefully
3.  Not SOLID compliant — Does not solve one problem at a time

# **References**

1.  [Singleton Design Pattern | Implementation with details & code](https://www.youtube.com/watch?v=oeQWxhlnCHM)
2.  [TutorialsPoint — Singleton](https://www.tutorialspoint.com/design_pattern/singleton_pattern.htm)
3.  [Ruby Singleton](https://github.com/ruby/ruby/blob/master/lib/singleton.rb)
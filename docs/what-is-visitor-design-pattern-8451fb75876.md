# 访问者设计模式是什么？

> 原文：<https://betterprogramming.pub/what-is-visitor-design-pattern-8451fb75876>

## 如果类的某些部分确实不适合，双重分派会很有用

![](img/b074ca42a5d9e8c480c6401a32d4c196.png)

朱利安·杰米森在 [Unsplash](https://unsplash.com/s/photos/door-person?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的[照片](https://unsplash.com/@sirjemison?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

访问者设计模式背后的思想是将具有特定职责的代码部分放在类之外。

该模式最常见的用例是以给定的格式报告和导出数据。这些非常有用的特性很容易变得复杂。它们对存储在对象中的数据进行操作，但是有非常具体的任务要执行。

单一责任原则规定一个类应该只有一个改变的理由。如果我们想在数据结构上添加报告或导出，我们至少有两个职责。

这不是好的做法。此外，使用访问者模式，我们可以独立地为访问者和被访问者添加新功能，因此我们的代码也符合开放/封闭原则。

双重派遣是一个与游客密切相关的概念。当函数的行为依赖于调用者和被调用的类时，就会发生这种情况。

当我们对`JsonExporter`和`PDFExporter`访问者使用它时，在 visited `Company`类中会有不同的结果和行为。尽管两个访问者类共享相同的公共接口。

我们将执行一项非常简单的任务。某大学存在学生缺课的问题。我们需要添加一个函数来计算每个学生因病假而错过的天数。

这似乎是访问者模式的工作。让我们从定义可以被访问的类和访问者的接口开始。

每个 visitable 类都必须实现`accept`方法。根据类的类型，实现会略有不同。

我们的学生模型非常简单，只包含一个名字和一张病假单。

每个病假都有开始和结束日期。这将使手术变得更加容易。

`SickLeave`类也是验证输入数据的好地方(例如，如果开始日期小于结束日期)，但是我们希望这个例子尽可能简单。

该大学也有一个非常简单的模型。它只包含一个名字和一份学生名单。

为了生成每个学生的缺勤天数数据，我们需要计算每个病假的天数，并将其格式化为可读的消息。

我们的报告已经准备好了，我们保持了代码的整洁。如果我们想添加一个关于考试分数、人口统计数据或任何其他数据的报告，我们可以轻松地重用创建的界面，并且不对`Student`和`University`做任何更改。

在不修改源类的情况下实现这种行为的另一种可能性是通过继承。

我们可以有一个负责报告生成的`StudentSickReport`类，但是它也会有很多来自`Student`的不必要的逻辑开销，这是它不应该关心或知道的。

# 访问者具有以下要素

*   访问者(`Visitor` ) —定义访问者的界面。
*   具体访问者(`SickLeaveReport`)—`Visitor`接口中定义的操作的具体实现。
*   Element ( `Visitable` ) —定义“接受”操作接口，以`Visitor`为参数。
*   具体元素(`Student`，`University` ) —元素的具体实现。

访问者模式应该用在稳定的类中。如果被访问的类经常改变，它可能会迫使访问者进行改变，模式的维护就变得很成问题。

它肯定不是最流行的设计模式，因为它的用例范围相对较窄，但它仍然非常方便。

## 优势

*   无需修改源类就可以轻松添加额外的操作。
*   提倡单一责任和开放/封闭原则。

## 不足之处

*   在某些情况下会使代码过于复杂。
*   不适合不稳定的阶层。

# 源代码

完整的源代码和其他一些模式可以在这里找到:

[](https://github.com/jkapuscik2/design-patterns-php) [## jkapuscik 2/设计-模式-php

### 这个项目是一组在现实世界中使用不同设计模式的简单例子。每个人都有一个…

github.com](https://github.com/jkapuscik2/design-patterns-php) 

如果您想了解更多的设计模式，您可以在以下文章中找到它们:

*   [工厂方法](https://medium.com/@j.kapuscik2/getting-started-with-design-patterns-in-php-4d451ccdfb71)
*   [创作模式](https://medium.com/@j.kapuscik2/creational-design-patterns-in-php-db365d3245ce)
*   [观察者](https://medium.com/@j.kapuscik2/observer-pattern-in-php-2ba240f89fb2)
*   [迭代器](https://medium.com/@j.kapuscik2/iterator-pattern-in-php-b7624f6bdbcf)
*   [状态&战略](https://medium.com/@j.kapuscik2/state-strategy-design-patterns-by-example-f57ebd7b6211)
*   [模板方法](https://medium.com/@j.kapuscik2/template-method-pattern-in-php-6116fd7e8ccc?source=friends_link&sk=ac4c483446bd5a5323c09a662bd54116)
*   [轻量级](https://medium.com/swlh/flyweight-design-pattern-in-php-edcda0486fb0?source=friends_link&sk=a0fa3083d5afd7e41af8a4f7a1df05f1)
*   [代理](https://medium.com/better-programming/proxy-design-pattern-and-how-to-use-it-acd0f11e5330)
*   [装饰师](https://medium.com/better-programming/decorator-c04fae63dfff)
*   [依赖注入](https://medium.com/better-programming/dependency-injection-8f09a93ec995)
*   [复合](https://medium.com/swlh/composite-908878748d0e)
*   [适配器](https://medium.com/swlh/building-cloud-storage-application-with-adapter-design-pattern-8b0105a1bda7)
*   [立面](https://medium.com/better-programming/what-is-facade-design-pattern-67cb09ce35d4)
*   [桥](https://medium.com/better-programming/what-is-bridge-design-pattern-89bfa581fbd3)
*   [责任链](https://medium.com/@j.kapuscik2/what-is-chain-of-responsibility-design-pattern-ff4d22abd124)
*   [命令](https://medium.com/@j.kapuscik2/what-is-cqrs-command-design-pattern-5d400fd9f93a)
*   [空对象](https://medium.com/@j.kapuscik2/what-is-null-object-design-pattern-f3b4d3d28636)
*   [流畅的界面](https://medium.com/@j.kapuscik2/what-is-the-fluent-interface-design-pattern-2797645b2a2e)
*   [规格](https://medium.com/@j.kapuscik2/what-is-the-specification-design-pattern-4051dd9e71c3)
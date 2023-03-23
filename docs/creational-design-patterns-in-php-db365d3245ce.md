# 什么是创造性的设计模式？

> 原文：<https://betterprogramming.pub/creational-design-patterns-in-php-db365d3245ce>

## 创建一个新对象可能会很棘手——学习如何正确地完成它

![](img/b563a8b52b09870aac8b697eeb938cd9.png)

在给定的用例中，*创造性设计模式*关注于以最好的方式构建新对象的方法。它们有助于将与这些任务相关的逻辑封装到单独的组件中。

新对象的初始化和创建是每个应用程序非常重要的一部分，也是引入新错误的可能来源。

# 原型

如果您正在创建许多相似的对象，这种模式会很有用。重用现有对象并仅修改您需要的属性通常是有益的。

这种方法可以使您的代码更加整洁，在某些情况下，还可以提高性能。在 PHP 中，与对象复制相关的逻辑应该放在`__clone` magic 方法中。

值得注意的是，在克隆时，并不调用对象`__construct`。如果你想使用克隆，你应该记住这一点。

让我们有一个系统来注册计算机，将它们分配到一个组中，并保存到数据库中。我们的设备模型非常简单。

现在，我们只有一种设备类型—计算机。

每个设备只能分配给一个位置和名称不同的组。

我们必须在我们的系统中注册许多新电脑。他们都在同一个地方。随着原型的使用，现在真的很容易。我们可以创建一个设备模型，复制它，然后只更改唯一的 ID。

对于这样一个简单的例子来说，这似乎不是一件大事，但想象一下，构建每个设备由几个步骤组成，并且每个组都有许多配置，这可能会省去我们很多麻烦。

# 抽象工厂

这种模式有助于使创建不同类型的对象的过程更加清晰明了。你必须为工厂类定义一个接口，该类的唯一目标是创建新对象*。*

然后，您知道如何创建任何类型的对象以及调用哪些方法。工厂的具体实现处理特殊类型对象的创建。

我们在一家生产电视和一些物联网设备(如信标)的公司工作。它们被放置在室内和室外的公共场所。这些设备的细节必须有所不同，以使其在特定条件下更好地工作。

用于室外工作的设备必须防水和防破坏。使用的硬件和安装过程可能会有很大不同。因此，我们应该在应用程序中为它们建立单独的层次结构。

每种生产的设备有两种类型:室内和室外。

我们的`DisplayFactory`制造特殊类型的显示器(这并不奇怪……)。

而`IotFactory`做特殊的物联网设备。

室外和室内产品的界面略有不同，您可以在这些产品上运行特定的操作。

并测试关键的质量属性。

这些接口中的每一个都有具体的实现，但是我认为没有必要在这里包含它们。现在，我们可以轻松地添加新类型的设备，设计用于室内和室外。

# 一个

有些东西应该只创建一次，然后作为单个实例全局使用。如果您在应用程序中使用数据库，那么每个查询使用单个连接而不是多个连接是合理的。

您也不希望在应用程序中使用配置组件的多个实例。

单例有时被认为是一种反模式。它引入了通常不希望的全局状态。它有时会被过度使用，人们应该考虑到它的缺点。

在 PHP 中使用 singleton 所要做的就是通过将`__contruct`和`__clone`方法设置为 private 或 protected 来防止直接初始化。这些方法现在只能从类内部调用。

现在，您所要做的就是通过一个约定允许访问公共静态方法，通常命名为`getInstance`，它为每个调用提供相同的类实例。

在提供的示例中，我们正在检索一个活动用户的数据(假设一次只能有一个用户)。在现实世界中，这样的操作可能需要数据库查询或其他操作。

使用 Singleton，我们可以获得性能优化，并建立关于活动用户的单一真实来源。(在修改的情况下，所有客户都会注意到。)

# 建设者

构建复杂类型的对象可能相当棘手。它可以由几个步骤组成，并以特定的顺序执行。这就是构建器设计模式的作用。

它帮助我们组织一个复杂对象的构造，并将这个逻辑封装到一个单独的组件中。

我们在一家制造不同类型设备的公司工作。构建这些包括几个重要的步骤。每一个都需要特定的软件、硬件，并为客户提供某种形式的 SLA。

指挥者是操作的大脑，并使用专门为每种类型的设备准备的构建器。每个构建器需要有相同的界面。

假设我们创造的设备之一是一面互动的镜子。

为了简单起见，交互式镜子的表示非常简单。

现在制造一种新装置非常容易。如果我们想在生产线上添加电视，我们只需要添加一个新的电视生成器和电视类。

值得注意的是，在构建器模式中，我们的设备不必共享相同的接口。让它们的构建器类有相同的接口是很重要的。

其他模式的完整源代码和描述可从以下网址获得:

 [## jkapuscik 2/设计-模式-php

### 这个项目是一组在现实世界中使用不同设计模式的简单例子。每个人都有一个…

github.com](https://github.com/jkapuscik2/design-patterns-php) 

我希望这些例子有所帮助，并使每个模式背后的思想更容易理解。如果您想了解一些更有用的设计模式，您可以在以下文章中找到它们:

*   [工厂方法](https://medium.com/@j.kapuscik2/getting-started-with-design-patterns-in-php-4d451ccdfb71)
*   [观察者](https://medium.com/@j.kapuscik2/observer-pattern-in-php-2ba240f89fb2)
*   [迭代器](https://medium.com/@j.kapuscik2/iterator-pattern-in-php-b7624f6bdbcf)
*   [状态&策略](https://medium.com/@j.kapuscik2/state-strategy-design-patterns-by-example-f57ebd7b6211)
*   [模板方法](https://medium.com/@j.kapuscik2/template-method-pattern-in-php-6116fd7e8ccc?source=friends_link&sk=ac4c483446bd5a5323c09a662bd54116)
*   [飞锤](https://medium.com/swlh/flyweight-design-pattern-in-php-edcda0486fb0?source=friends_link&sk=a0fa3083d5afd7e41af8a4f7a1df05f1)
*   [代理](https://medium.com/better-programming/proxy-design-pattern-and-how-to-use-it-acd0f11e5330)
*   [装饰者](https://medium.com/better-programming/decorator-c04fae63dfff)
*   [依赖注入](https://medium.com/better-programming/dependency-injection-8f09a93ec995)
*   [复合](https://medium.com/swlh/composite-908878748d0e)
*   [适配器](https://medium.com/swlh/building-cloud-storage-application-with-adapter-design-pattern-8b0105a1bda7)
*   [立面](https://medium.com/better-programming/what-is-facade-design-pattern-67cb09ce35d4)
*   [桥](https://medium.com/better-programming/what-is-bridge-design-pattern-89bfa581fbd3)
*   [责任链](https://medium.com/@j.kapuscik2/what-is-chain-of-responsibility-design-pattern-ff4d22abd124)
*   [来访者](https://medium.com/@j.kapuscik2/what-is-visitor-design-pattern-8451fb75876)
*   [命令](https://medium.com/@j.kapuscik2/what-is-cqrs-command-design-pattern-5d400fd9f93a)
*   [空对象](https://medium.com/@j.kapuscik2/what-is-null-object-design-pattern-f3b4d3d28636)
*   [流畅的界面](https://medium.com/@j.kapuscik2/what-is-the-fluent-interface-design-pattern-2797645b2a2e)
*   [规格](https://medium.com/@j.kapuscik2/what-is-the-specification-design-pattern-4051dd9e71c3)
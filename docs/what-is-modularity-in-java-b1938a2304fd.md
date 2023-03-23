# 什么是 Java 中的模块化？

> 原文：<https://betterprogramming.pub/what-is-modularity-in-java-b1938a2304fd>

## 从 Java 9 开始引入的一个特性

![](img/6efda6de407283e34474007812a64d00.png)

西蒙·戈茨在 [Unsplash](https://unsplash.com/s/photos/modular?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在这篇文章中，我想告诉你关于 Java 模块你需要知道的一切。我打算帮助那些在面试中被问到这个问题的 Java 开发人员，或者那些希望获得 Java 11 OCP 认证的人。如果你有阅读模块的理由，那也很好！如果是这样的话，请随意发表评论，因为我很乐意听到你的意见。

不仅仅是理论(我已经省略了更高级的东西，比如 jdeps)，所以不要害怕被淹没！

# 什么是模块？

模块是 Java 9 中引入的 Java 程序组件。在 Java 9 之前，类驻留在包中，包中的类由类装入器找到。从 Java 9 开始，模块在包和类装入器之间起作用。资源和包(以及这些包中的类)可以从其他模块中封装出来，这在以前是不可能的。它将 Java 中的防御性开发带到了一个更高的层次。作为一名开发人员，这听起来太棒了！

从现在开始，只能使用从其他模块显式导出的公共类。从现在开始，没有模块的开发人员将使用未命名的模块。这些未命名的模块将导出它们所有的包。这对于向后兼容性很好，但是，我强烈建议尽早使用命名模块。迁移过程可能非常耗时，尤其是对于对模块系统知之甚少甚至一无所知的开发人员来说。

模块是一组唯一命名的、可重用的相关包、资源和模块描述符，它指定了以下内容:

*   名字
*   属国
*   导出的包
*   提供的包
*   用过的服务
*   哪些包裹是打开的

# 名字

模块声明指定了一个新的命名模块。我将给出一些有效模块声明的例子:

```
module example {}
```

示例模块是完全有效的模块名，但是 Java 模块必须有唯一的名称。如果可能的话，建议将 Java 模块命名为与该模块中包含的根 Java 包相同的名称。

模块名由一个或多个用“.”分隔的 Java 标识符组成代币。所以这给了我们:

```
module continuum.be.example {}
```

Java 模块遵循与 Java 包相同的命名规则。我建议不要使用下划线，因为下划线字符将来可能会变成保留标识符。除此之外，为了保持一致性，我还建议将模块名的每一部分都保持小写。

# 属国

一方面，一个命名模块指定了对其他模块的依赖，以定义其代码可用的类和接口的总体。依赖关系是由一个`requires`指令所表达的。在下面的模块声明中，`continuum.be.example`模块直接依赖于`continuum.be.accounting`模块。

```
module continuum.be.example {requires continuum.be.accounting;}
```

还有一个 requires static 指令，指示模块在编译时是必需的，但在运行时是可选的。这就是所谓的可选依赖项。最后，您还可以使用 transitive 关键字来确保读取您的模块的其他模块也读取该依赖关系，这就是所谓的隐含可读性。

# 导出的包

Java 模块必须导出模块中的所有包，这些包可供使用该模块的其他模块访问。一个包只能由一个单独的 Java 模块导出，否则，这将导致运行时出现一个`ResolutionException`，因为创建了一个所谓的分割包。

导出的包在模块描述符中声明。这里有一个简单的出口申报:

```
module continuum.be.accounting {exports continuum.be.accounting;}
```

不会导出导出包的任何子包。这意味着，如果会计包包含一个名为`util`的子包，那么`com.continuum.accounting.util` 包是*而不是* 导出的，因为`com.continuum.accounting`是。如果您不想导出父包，也可以不导出。

# 提供的包

想要从另一个模块实现服务接口的 Java 模块必须采取以下步骤:

*   需要另一个模块，在本例中为`com.continuum.service`。
*   用 Java 类`OurServiceImpl`实现服务接口`OurService`。
*   使用**提供的** <接口>和<实现类>符号声明服务接口实现。

```
module com.continuum.implementation {requires com.continuum.service;provides com.continuum.service.OurServicewith com.continuum.service.OurServiceImpl}
```

# 用过的服务

一旦有了服务接口模块(例如 JPA)和服务实现模块(例如 Hibernate)，就可以创建使用该服务的客户端模块。要以模块化的方式使用服务，您必须`require`接口模块，例如`com.continuum.service`并声明您将使用`uses`关键字来使用它。这方面的一个例子如下:

```
module com.continuum.client {requires com.continuum.service;uses com.continuum.service.OurService;}
```

不必声明实现模块的优点是，可以在不破坏客户端代码的情况下交换实现模块。您可以在组装应用程序时决定使用什么服务实现(例如，当您使用 maven 时，通过在 pom.xml 文件中指定版本)。这意味着客户端和服务接口模块与服务实现模块是分离的。

现在，服务客户端模块可以在运行时查找服务接口实现，如下所示:

```
ServiceLoader<GenerationService> load = ServiceLoader.load(GenerationService.class);
```

# 哪些包裹是打开的

关键字`opens`指定当前模块要打开的包的名称。对于其他模块中的代码，这将在运行时(而不是编译时)授予对包中的公共和受保护类型以及这些类型的公共和受保护成员的访问权限。它还为其他模块中的代码授予对包中所有类型及其所有成员的反射访问。

也可以使用`**opens … to**`关键字只为一个或多个模块打开它。强烈建议不要使用 open 关键字来打开整个模块，但是我有什么资格来判断你在业余时间做什么——不管你做什么！

# 把这个理论付诸实践怎么样？

我试验了服务加载器 API、maven 和模块，并创建了一个小型健身应用程序。这表明创建一个模块化的应用程序是多么容易，并且它可能是创建更复杂的工业应用程序的良好起点。

首先，从一个 Spring JPA 应用程序开始，理解新模块系统中发生的事情可能是一个挑战。您最终可能会得到一个无法编译的应用程序。

这个健身应用程序突出了服务加载器 API，它是 [**依赖注入**](https://www.vogella.com/tutorials/DependencyInjection/article.html) 的一个不太为人所知的替代品。对于小型应用程序来说，从一个众所周知的框架引入依赖注入会极大地增加应用程序的大小，这对于必须担心内存使用的程序来说是很糟糕的。是的，我们可以用当地的方式来做！

![](img/72288a998a6978fdd5374c1843a1c579.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Clément H](https://unsplash.com/@clemhlrdt?utm_source=medium&utm_medium=referral) 拍摄

# 锻炼模块

我们的应用程序的第一个模块是练习模块，我们的服务提供者接口模块。练习模块导出一个包`com.continuum.generation.spi`、**、**，并且与实现完全分离。如果这个包改变了，它必须与接口兼容，而不是相反。

在这个包中，我们有一个`GenerationService`接口，允许我们为一个特定的健身`Level` 生成一个`Schedule`(例如跑步时间表，健身时间表)，这只是一个常规的枚举。

GenerationService.java

Schedule.java

# 运动模块

我们健身应用程序的第二个模块是`sports`模块。该模块为`exercise` spi 提供了两种实现方式。因为它们涉及体育运动中的两个不同领域，所以我将`running`和`fitness`时间表生成器放在一个单独的包中。

实现类被两个工厂抽象出来(`FitnessFactory`和`RunningFactory`)。这可以允许根据不同的参数(例如，基于当前的健康水平)生成不同的时间表。

这表明您不必将实现类放在模块声明中。然而，这样做肯定是可能的。

module-info.java

配件工厂

FitnessGenerationServiceImpl

配件时间表

# 主模块

最后，主模块将是我们的类似客户端的模块。这需要`exercise` SPI，并且不直接依赖于实现。我们还指出我们将使用`GenerationService`。提供的实现将取决于运行时导入的实现，这是我们在 maven 文件中指定的。

我们确实需要在 pom 文件中指定提供我们将要使用的接口的依赖项，这是练习模块，以及提供实现的依赖项，在本例中是`sports`模块。

module-info.java

pom.xml

如果我们现在在主模块中运行下面的类:

Main.java

我们获得以下两行输出:

*   健身时间表从一个连续工艺领先水平:高级
*   连续工艺师的运行时间表，等级:中级

还不错！所提供模块的用户可以在运行时使用他现有的代码，或者自己提供一个`GenerationService`实现。这也使得提供一些模拟实现变得容易。

# 现在该怎么办？

这不是很棒吗？

除了跑步和健身，还有更多运动——为什么不增加一个啤酒乒乓球时间表呢？

你可以在这里找到回购协议的更多细节:[https://github.com/djFooFoo/fitness](https://github.com/djFooFoo/fitness)。请随意进一步试验模块在 Java 中的工作方式。

如果有什么遗漏或者可以更好解释的地方，请留下评论，我会尽快回复你。感谢您的阅读！
# Spring 框架中的依赖注入类型

> 原文：<https://betterprogramming.pub/dependency-injection-types-in-spring-framework-4144dfd40cc5>

基于构造函数和设置器的 DI 概述

![](img/7a2360b3ab97b017ae6f2c260dc656a3.png)

由[马克西米利安·魏斯贝克尔](https://unsplash.com/@maximilianweisbecker?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在这一部分中，我们将首先了解依赖注入(DI)的含义，然后了解 Spring 中不同类型的 bean 注入。

# 什么是依赖注入？

*依赖注入* (DI)是一种实现控制反转(IoC)的模式，其中被反转的控制是对象依赖关系的设置。这是一个旨在增加 Java 类独立性的过程，它赋予了重用这些类并独立于其他类测试它们的能力。

Java 类或 beans 表示构成应用程序基础的对象。Beans 由容器或框架实例化、组装和管理。

尽管听起来很复杂，依赖注入实际上使你的代码变得非常简单，更容易理解和测试。依赖注入，也称为*连接，*有助于将这些看似不相关的类粘合在一起，同时增加独立性。

# Spring 中的依赖注入

在许多其他模块中，Spring Framework 实现了控制反转(IoC)原则，通过该原则，对象的控制被转移到容器或框架。

Spring 应用程序中的应用程序上下文加载 bean 定义并将它们连接在一起。

Spring 中的依赖注入可以通过 setters 和 constructors 来完成。

# 基于构造函数的依赖注入

当我们使用[基于构造函数的 DI](http://www.baeldung.com/constructor-injection-in-spring) 时，容器将调用一个带有多个参数的构造函数，每个参数代表我们想要设置的依赖项。每个参数首先根据其类型进行区分，然后根据属性和索引的名称进行区分。让我们看一个简短的例子:

`@Configuration`注释表明该类是 bean 定义的来源。`@Bean`注释用于定义一个 bean。

# 基于 Setter 的依赖注入

基于 setter 的 DI 或基于 setter 的连接是在调用无参数静态工厂方法实例化 bean 之后，通过容器调用 bean 的 setter 方法来完成的。我们可以在下面使用注释的示例中看到这一点:

# 你应该用哪一个？

您可以在您正在进行的项目中使用最适合您需求的 DI 类型。你也可以决定你认为使用起来更简单的方法。

# 从哪里可以了解更多信息？

这篇文章简要介绍了依赖注入的概念，以及 Spring 中不同类型的 bean 注入。

你可以通过阅读 Baeldung 上发表的另一篇更详细的文章[来了解这个话题。](http://www.baeldung.com/inversion-control-and-dependency-injection-in-spring)

你也可以看看马丁·弗劳尔的解释:

*   [控制反转](http://martinfowler.com/bliki/InversionOfControl.html)
*   [控制容器的倒置和依赖注入模式](http://martinfowler.com/articles/injection.html)。

您还可以在 [Spring 框架参考文档](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-dependencies)中获得关于 IoC 和 DI 的 Spring 实现的更深入的参考。

还有一本好书[详细介绍了迪，作者是丹吉·r·普拉桑纳。](https://www.manning.com/books/dependency-injection)
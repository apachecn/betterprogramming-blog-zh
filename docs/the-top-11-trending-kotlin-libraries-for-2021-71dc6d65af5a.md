# 2021 年 11 大热门科特林图书馆

> 原文：<https://betterprogramming.pub/the-top-11-trending-kotlin-libraries-for-2021-71dc6d65af5a>

## 用 Kotlin 开发精简的服务器端应用程序的有用库

![](img/e7c89bea404b225350f0a10fccfc3c0f.png)

马克·赖歇尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我使用 Kotlin 进行服务器端开发已经快两年了，我有机会使用了几个框架和库。有些库有很好的 Kotlin 支持，有些没有。随着越来越多的 Kotlin 原生库的出现，这种说法不再正确。下面讨论的一些库并不是新创建的，但是它们在过去的几个月里获得了很好的支持，并为开发服务器端应用程序提供了很好的支持。

# 无科特

我为与 2021 年相关的[技术写了一篇文章，无服务器是其中之一。](/5-software-development-trends-to-embrace-in-2021-a63d6a23dc93) [Kotless](https://github.com/jetbrains/kotless) 就是为了那个。它代表 *Kotlin 无服务器框架*。它侧重于通过直接从代码中生成无服务器部署来减少无服务器部署的创建。它只是为您提供了一种在 AWS 上部署无服务器应用程序的简单方法！Kotless 使用 Gradle 包装现有的构建过程，并将部署插入其中。它将作为应用程序的包装器。Kotless 能够将现有的 Spring Boot 或 Ktor 应用程序部署到 AWS 无服务器平台上。

# Kotest

[Kotest](https://github.com/kotest/kotest) 是一个测试库。在任何测试库中，都有三个组件，测试框架、模仿框架和断言框架。你通常可以像 JUnit、Mockk 和 assertj 一样混合搭配。Kotest 为您提供了测试框架和断言框架。

所以一个典型的测试用例应该是这样的。

Kotest 的优势在于它是用 Kotlin 编写的。因此，它可以利用 Kotlin 的语言特性，这允许您编写更地道、更简洁的断言。它具有内置的协程支持、将函数用作测试生命周期回调的能力、高级条件评估、强大的数据驱动测试等等。

[**如何找到软件开发员的新工作**](/how-to-navigate-a-new-job-as-a-software-developer-fd4ac2e4154b)

# 暴露的

[Exposed](https://github.com/JetBrains/Exposed) 是一个轻量级的 SQL 库，可以用于 Kotlin web 项目。它非常容易设置和使用。它遵循了非常依赖简单和极简主义的重要设计原则。因此，如果您只想更新一行，这是非常简单的。该库为您提供了控制从事务到隔离的几乎一切的所有构造。它支持 DSL 语法，这使得推理 SQL 代码更加灵活和容易。

它有很好的“入门”指南，文档也越来越好。有时你必须深入挖掘才能找到某个特定问题的解决方案，比如事务隔离或线程池，顺便说一句，使用和处理它们也很有趣。

# Ktor

[Ktor](https://github.com/ktorio/ktor) 是一个使用 Kotlin 在连接系统中构建异步服务器和客户端的框架。简洁明了。当你使用这个框架时，这正是它给你的感觉。它是最小的，易于设置和使用。它的复杂性要低得多，但是有时你需要自己构建更多的东西，这对于任何最小和简单的库都是如此。这就像有一个工作极其出色的小工具箱。[春天](https://spring.io/)，另一方面，就像拥有了整个五金店。一切都在某个地方，但有更多的导航；它又大又神奇。

也就是说，如果你需要一个没有任何 IoC 之类的小应用，Ktor 可以解决你的大部分任务。然而，如果你需要一个巨大的潜在集成 Kafka，多个数据库，测试中的 IoC 等。，那么你应该选择春天。

[**初级和高级开发人员的 6 大区别**](/6-key-differences-between-a-junior-and-a-senior-developer-cf2b3d868dd9)

# Kotlinx 序列化

当您想到 JSON 解析器时，脑海中只会出现这些名字，Jackson、Gson 或 Moshi。这些是 Spring 和 Java 应用程序中使用最广泛的解析器。 [Kotlin 序列化](https://github.com/Kotlin/kotlinx.serialization)是来自 JetBrains 的面向 Kotlin、跨平台、无反射的序列化库。

这就是你如何使用这个数据类来读取 JSON。

# 柯因

[Koin](https://github.com/InsertKoinIO/koin) 是面向 Kotlin 开发者的阿迪框架，完全用 Kotlin 编写。当您使用上面提到的库创建 Kotlin 应用程序时，它会非常方便。假设您正在使用 Ktor，它是为 Kotlin web 应用程序公开的。你需要阿迪框架来管理你的控制器、服务、仓库等等。

这就是你需要 Koin 的地方。在使用 Koin 时，在开始之前我们需要理解一些术语。

*   `module` —在 Koin 中创建一个模块，Koin 将使用该模块提供所有依赖项
*   `single` —创建可作为单一实例在整个应用程序中使用的单一实例
*   `factory` —提供 bean 定义，每次注入时都会创建一个新实例
*   `get()` —在类的构造函数中使用，提供所需的依赖关系。

# 网飞 DGS 框架

网飞最近发布了一个名为[网飞·DGS](https://github.com/Netflix/dgs-framework)的库，用 Kotlin 和 Java 创建 GraphQL 服务器。它是开源的，可以生产，因为网飞已经在内部使用了很长时间。DGS 框架与 Spring boot 结合得非常好，并且建立在 graphql-java 之上。Java 和 Kotlin 都有[示例](https://github.com/Netflix/dgs-examples-kotlin)。

我已经整理了一份使用 GraphQL 的[好处和使用 graph QL 的](https://levelup.gitconnected.com/6-months-of-using-graphql-faa0fb68b4af)[最佳实践](https://towardsdatascience.com/graphql-best-practices-3fda586538c4)的列表。这个库可以帮助您创建一个项目，就像创建一个 Spring Boot 项目一样简单。

# 克蒙戈

KMongo 是一个库，它为 T2 的 MongoDB 做了和 SQL 数据库一样的事情。它支持同步和异步模式，您可以使用协同例程。这是一个相当新的库，还没有被经常使用。这可以与 Kotlin 序列化一起使用，也可以用于对象映射。如果您的 Kotlin 应用程序与 MongoDB 交互，并且您没有使用 Spring Boot，那么这是一个不错的选择。如果你不使用 KMongo，那么你必须使用 MongoDB 的 Java 驱动程序。下面的例子利用 KMongo 和 Kotlin 序列化来创建一个实体，保存并检索它。它真的很容易使用和简单的设置。

# Xodus

另外一个数据库类的是 [Xodus](https://github.com/JetBrains/xodus) 。Xodus 是一个用 Java 和 Kotlin 编写的事务性、无模式的嵌入式数据库。Xodus 的一些优点是:

*   ACID 板载—所有数据库操作都是原子的、一致的、隔离的和持久的。
*   无需管理外部数据库—一切都在您的应用程序中。
*   Kotlin 语言的好处——它充分利用了类型、可空值和属性声明和约束描述的委托。
*   无痛重构——如果您需要添加几个属性，您不必重新构建表。

# 多卡

科特林的 Javadoc 替代者。这是一个为 Kotlin 设计的文档引擎，在 GitHub 上有大约 2k 颗星。它有许多配置选项，并支持 Kotlin 多平台。用来记录 Kotlin 代码的语言叫做 KDoc。 [Dokka](https://github.com/Kotlin/dokka) 理解 Java 文件中的标准 Javadoc 注释和 Kotlin 文件中的 KDoc 注释，并可以生成多种格式的文档，包括极简 HTML、Javadoc HTML 和 Markdown。

# 瓦丁

另一个使用 Kotlin DSL 支持 Kotlin 的 web 框架。有了用于 [Vaadin](https://vaadin.com/) 的 Kotlin DSL，您可以直接开始将内容添加到 UI 类中。有了更少的样板文件，你可以专注于构建你的应用程序。Vaadin 让您忘记 web，以与使用 Swing 等传统 Java 工具包开发桌面应用程序非常相似的方式开发用户界面。Kotlin 非常适合开发 web 应用程序，因为它允许您编写简洁而富有表现力的代码。

# 结论

就是这样。我希望你喜欢这个列表，并且一些库或项目将帮助你创建一个干净和精简的 Kotlin 服务。我写的其他有趣的文章。

[](/failing-one-tech-interview-taught-me-more-than-cracking-three-9ec1870519da) [## 失败一次技术面试比成功三次教会我更多

### “任何可能出错的事情都会出错”——墨菲定律

better 编程. pub](/failing-one-tech-interview-taught-me-more-than-cracking-three-9ec1870519da)
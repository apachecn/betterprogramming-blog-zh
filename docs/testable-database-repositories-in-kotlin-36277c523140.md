# Kotlin 中可测试的数据库存储库

> 原文：<https://betterprogramming.pub/testable-database-repositories-in-kotlin-36277c523140>

## 获得 SQL 和 DynamoDB 代码的完美覆盖

![](img/3dc85f9ddf8e5eae18e22f7c43e3d5e9.png)

JOSHUA COLEMAN 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

我之前已经介绍过如何[编写可测试的 Kotlin 服务](https://medium.com/better-programming/writing-testable-services-in-kotlin-e56d0de0b82b)，但是这一次，我们将扩展数据库 Dao 和存储库。如果您测试服务的主要方法是模拟数据库代码，那么本指南将帮助您确保获得出色的测试覆盖率。

我们将讨论一些我最喜欢的数据库工具:

1.  生 JDBC
2.  [外露 ORM](https://github.com/JetBrains/Exposed)
3.  [Java 2.0 版 AWS SDK 中的 DynamoDB Mapper](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/examples-dynamodb-enhanced.html)
4.  带[的 dynamo db http4k-connect-Amazon-dynamo db](https://github.com/http4k/http4k-connect)

让我们检查一下我们将要实现的`CatsRepo`接口。我们有一个`Cat`模型，它将是一些关键数据库操作的主题。

# 测试合同

接下来，我们将定义一个契约来验证每个存储库的行为。我们将扩展该合同，以涵盖`CatsRepo`的每个实现。

# 生 JDBC

在我看来，原始 JDBC 是一种被低估的 SQL 数据库技术。对于简单的数据模型，它可以快速干净地完成工作；对于任何带有 JDBC 驱动程序的数据库。让我们为 MySQL 实现一个 JDBC `CatsRepo`。

通过要求将`DataSource`注入到我们的`JdbcCatsRepo`中，我们可以无缝地将它用于生产和测试。在我们的`main`方法中，我们用一个简单的`DataSource`来配置它；尽管在生产中，您可能会使用自己选择的连接池。

现在让我们为我们的`JdbcCatsRepo`实现一个`CatsRepoContract`。我们需要一个测试数据库，虽然可以使用 MySQL 容器，但我更喜欢使用难以置信的轻量级纯 Java H2 数据库；在 MySQL 兼容模式下。一旦我们启动了内存数据库，我们就可以创建表，并将其注入到我们的`JdbcCatsRepo`中。

这很简单。但是对于更复杂的 ORM 来说，这是如何工作的呢？

# 暴露的 ORM

[JetBrains/Exposed](https://github.com/JetBrains/Exposed) 是一个备受推崇的 SQL ORM 它是富于表现力的、类型安全的、轻量级的。对于非平凡的数据模型，这是一个很好的选择。我们来实现一个`CatsRepo`。

在这种情况下，我们的`ExposedCatsRepo`需要注射一个暴露的`Database`；这与前面例子中的`DataSource`密切相关。我们的`main`方法与上面 JDBC 的例子非常相似；我们得到一个 MySQL `DataSource`，将其转换成一个暴露的`Database`，并将其注入到`ExposedCatsRepo`。

实现一个`CatsRepoContract`就像生 JDBC 一样简单；我们将使用同一个内存中的 H2 数据库，使用公开的`SchemaUtils`创建表，然后将其注入到`ExposedCatsRepo`。

既然我们已经介绍了一些 SQL 选项；让我们转到 DynamoDB。

# 用于 Java 2.0 的 AWS SDK

Dynamo DB 的增强客户端[提供了一个优秀的映射接口来处理 Dynamo DB 项目。然而，由于这是一个 Java SDK，映射器不能处理惯用的 Kotlin 数据类；我发布了一个插件](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/examples-dynamodb-enhanced.html)来绕过这个限制，我们将在这里使用它。

这个`CatsRepo`的实现需要我们创建一个`DynamoCat`类；用提示对映射器进行注释以生成模式。

我们的`V2DynamoCatsRepo`要求我们注入一个`DynamoDbTable`。在我们上面的`main`方法中，我们创建了一个真正的 Dynamo DB 客户端，并定义了要注入的表。

为了测试新的存储库，我们需要模拟 DynamoDB。对此有几种选择；各有利弊。

1.  [dyna lite 测试容器模块](https://www.testcontainers.org/modules/databases/dynalite/)——高度准确，但需要 docker，并且初始化缓慢
2.  oharaandrew314/mock-AWS-Java-sdk—官方 SDK 的快速内存实现，但是缺少一些高级特性
3.  [http4k/http4k-connect](https://github.com/http4k/http4k-connect) —或类似；绑定到本地 TCP 端口的独立 AWS 模拟器

对于这个例子，我们将使用[oharaandrew314/mock-AWS-Java-SDK](https://github.com/oharaandrew314/mock-aws-java-sdk)。它提供了官方 SDK 的完全模拟实现。

这种方法非常有效，但是有时臃肿的官方 SDK 并不适合包含在类路径中。为了解决这个问题，我们可以研究一个替代方案。

# http4k-连接 DynamoDB 表映射器

[http4k/http4k-connect](https://github.com/http4k/http4k-connect) 有一个替代的 AWS SDK，并且包括一个 Dynamo DB mapper，我们可以用它来实现一个`CatsRepo`。由于 SDK 是建立在 http4k 之上的——它的创建者是可测试性的忠实粉丝——它包含了一个用于我们测试的高效模拟 AWS 环境。

这个 SDK 中没有反射，所以要实现`CatsRepo`，我们需要定义主索引和次索引。在 [http4k-connect 附录](https://github.com/http4k/http4k-connect/blob/master/amazon/dynamodb/client/src/examples/kotlin/using_the_table_mapper.kt)中可以找到关于这个映射器的更详细的指南。

`Http4kDynamoCatsRepo`需要注射`DynamoDbTableMapper`。对于我们的`main`方法，我们将连接到官方的 AWS 后端，并定义一个要注入的表。

实现一个`CatsRepoContract`与`main`方法基本相同。主要区别在于，我们在一个由假 DynamoDB 环境支持的客户机中定义表。

# 结论

有了这些策略，我希望你能在测试中不再忽视你的数据库代码。如果您实际使用您的存储库，那么您可以对您的服务有更大的信心，测试和模拟会少得多。

# 源代码

GitHub 上提供了这些示例的完整源代码:

[](https://github.com/oharaandrew314/testable-databases-guide) [## GitHub-oharaandrew314/testable-databases-guide

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/oharaandrew314/testable-databases-guide)
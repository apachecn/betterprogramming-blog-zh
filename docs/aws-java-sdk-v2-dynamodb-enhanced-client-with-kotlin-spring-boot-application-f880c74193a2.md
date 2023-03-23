# AWS Java SDK V2

> 原文：<https://betterprogramming.pub/aws-java-sdk-v2-dynamodb-enhanced-client-with-kotlin-spring-boot-application-f880c74193a2>

## 用 Kotlin 增强了 DynamoDB

![](img/ac8af8a457d234e615aaf4e10f681680.png)

照片由[卢卡斯·坎波伊](https://unsplash.com/@campoilucas?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 介绍

如果您从版本 1 开始就一直使用 [AWS Java SDK](https://aws.amazon.com/developer/) 用于 [DynamoDB](https://aws.amazon.com/dynamodb/) ，那么您将会实现或遇到`[DynamoDBMapper](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBMapper.html)`类。这个类允许我们定义 DynamoDB 表中的实际项目和应用程序中的 Java POJO 类之间的关系或映射。

换句话说，AWS Java SDK 负责或抽象出底层操作，即，为相应的 CRUD 操作构建相关的 DynamoDB 表达式。这对于我们编写应用程序代码来说非常方便。

长话短说，来了 [AWS Java SDK 版本 2](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/welcome.html) 。这是对版本 1 的重大改写。最初的版本没有版本 1 中的`DynamoDBMapper`类提供的 DynamoDB 抽象。这意味着我们需要处理底层操作，即构造属性值和 Java POJO 类对象。

CustomerRepo.kt —使用 DynamoDbAsyncClient

已经不太方便了吧？

# DynamoDB 增强型客户端

不要失去希望！当 AWS 在 2020 年 4 月发布 Java SDK 版本 [2.12.0](https://github.com/aws/aws-sdk-java-v2/blob/master/CHANGELOG.md#2120-2020-04-20) 中的 [Amazon DynamoDB 增强客户端](https://github.com/aws/aws-sdk-java-v2/blob/master/CHANGELOG.md#amazon-dynamodb-enhanced-client-6)时，他们对我们的抱怨(或需求)有了答案。这相当于版本 1 中的`DynamoDBMapper`类，它提供了 DynamoDB 表中的项目和 Java POJO 类之间的抽象。

在本教程中，我们将讨论如何在我们的 [Kotlin](https://kotlinlang.org/docs/reference/) 应用程序中实现和/或迁移 DynamoDB 增强客户端。值得一提的是，DynamoDB 增强客户端可用于同步和异步操作，并分别依赖于`[DynamoDbClient](https://sdk.amazonaws.com/java/api/latest/software/amazon/awssdk/services/dynamodb/DynamoDbClient.html)`和`[DynamoDBAsyncClient](https://sdk.amazonaws.com/java/api/latest/software/amazon/awssdk/services/dynamodb/DynamoDbAsyncClient.html)`。

我之前写过一篇关于如何构建使用 AWS Java SDK V2 `DynamoDBAsyncClient`的 Kotlin Spring Boot 应用程序的[教程](https://medium.com/swlh/how-to-build-a-reactive-microservice-api-with-spring-boot-spring-webflux-and-dynamodb-using-kotlin-e1be3e99b15e)。如果您以前没有使用 AWS Java SDK V2 编写过任何应用程序，我建议您在继续之前先阅读本教程。

让我们继续克隆从前面提到的教程构建的[库](https://github.com/billydh/dynamodemo)。本教程将基于这个库。

DynamoDB 增强型客户端的实现可以总结为以下四个步骤:

*   包含`dynamodb-enhanced`包作为依赖项。
*   定义表示 DynamoDB 项的数据类。
*   定义一下`DynamoDbEnhancedAsyncClient`。
*   定义存储库类。

现在，让我们详细地看一下它们。

# 将 dynamodb 增强包作为依赖项包含在内

DynamoDB 增强客户端不是标准[software . Amazon . AWS SDK:dynamo db](https://mvnrepository.com/artifact/software.amazon.awssdk/dynamodb)包的一部分。它包含在[software . Amazon . AWS SDK:dynamo db-enhanced](https://mvnrepository.com/artifact/software.amazon.awssdk/dynamodb-enhanced)中，仅从 AWS Java SDK 版本 2.12.0 开始可用。

所以，在`build.gradle.kts`文件中，我们需要做如下操作:

*   将[software . Amazon . AWS SDK:dynamo db-enhanced](https://mvnrepository.com/artifact/software.amazon.awssdk/dynamodb-enhanced)包添加到依赖项中。
*   将[software . Amazon . AWS SDK:BOM](https://mvnrepository.com/artifact/software.amazon.awssdk/bom)包版本提升到 2.15.22，这是本文撰写时的最新版本，或者任何版本≥ 2.12.0。

build.gradle.kts —具有增强的依赖关系

# 定义表示 DynamoDB 项的数据类

在最基本的层面上，DynamoDB 增强客户端库提供了以下注释来启用 Java POJO 类映射:

*   `@DynamoDbBean`
*   `@DynamoDbPartitionKey`

`@DynamoDbBean`注释需要应用于 Java POJO 类，该类有一个默认的公共构造函数，以及该类每个属性的标准命名的 getters 和 setters，正如他们在[博客](https://aws.amazon.com/blogs/developer/introducing-enhanced-dynamodb-client-in-the-aws-sdk-for-java-v2/)中提到的。Kotlin 的数据类免费提供这些标准命名的 getters 和 setters。

另外，我们可以在 Java POJO 类上使用另一个注释，即`@DynamoDbImmutable`。这个注释是专门为不可变的 POJO 类设计的。我们不会在本教程中涉及它，但是，你可以查看[官方文档](https://github.com/aws/aws-sdk-java-v2/tree/master/services-custom/dynamodb-enhanced#working-with-immutable-data-classes)。

此外，`@DynamoDbPartitionKey`注释将应用于数据类中字段的 getter 或 setter，该字段是 DynamoDB 表的主分区键。数据类中没有注释的所有其他字段表示标准的 DynamoDB 项目属性。

我们来看看如何应用以上两个条件。在 [dynamodemo](https://github.com/billydh/dynamodemo) 库中，有一个名为`CustomerPersist`的数据类，看起来像这样。

Customer.kt 中的 CustomerPersist 数据类

我们的 customer 表有一个主分区键，它由一个散列键组成，即`customerId`属性。让我们继续用所需的注释来注释`CustomerPersist`。

如果您不知道`@get`注释是做什么的，它最初是一个 Java 注释，用于将一些注释应用到属性的 getter 方法。更多信息请见 [Kotlin 文档](https://kotlinlang.org/docs/reference/annotations.html#annotation-use-site-targets)。

值得一提的是，用`@DynamoDbBean`注释的类必须有一个默认的主构造函数。这就是为什么我们的`CustomerPersist`的所有字段都被定义为默认值`null`。您可以指定任何您喜欢的默认值。

不管怎样，这就是我们数据类需要做的。

# 定义`DynamoDbEnhancedAsyncClient`

如前所述，DynamoDB 增强客户端依赖于 SDK 版本 2 包中的对应 DynamoDB 客户端，即`DynamoDbEnhancedAsyncClient`需要`DynamoDbAsyncClient`而`DynamoDbEnhancedClient`需要`DynamoDbClient`。在本教程中，我们只打算构造`DynamoDbEnhancedAsyncClient`。

现在，让我们继续更新 dynamodemo 中的`DynamoClientProperties`以包含类型`DynamoDbEnhancedAsyncClient`的`Bean`。

`DynamoDbEnhancedAsyncClient Bean in DynamoClientProperties.kt`

就是这样！现在，我们可以使用这个 Dynamo 客户机与 DynamoDB 进行交互。很简单，不是吗？

# 定义存储库类

类似于使用 SDK 版本 1 中的`DynamoDbMapper`类时需要在 repository 类上做的事情，我们必须在 [dynamodemo](https://github.com/billydh/dynamodemo) repo 中调整现有的`CustomerRepo`类。

这门课有三件事要做:

*   基于`@DynamoDbBean`注释的`data class`定义表模式对象——表模式对象具有`BeanTableSchema<T>`类型。
*   初始化将用于与实际 DynamoDB 表交互的`DynamoDbAsyncTable<T>`对象。
*   更新现有的 DynamoDB 操作以使用`DynamoDbAsyncTable<T>`对象。

这是`CustomerRepo`类完成以上三步后的样子。

使用 DynamoDB 增强客户端的 CustomerRepo

没错。我知道。简单多了！

# 测试

为了测试我们刚才所做的更改，我们可以按照我在上文提到的关于如何构建 Kotlin Spring Boot 应用程序的教程中描述的步骤，在连接到我们的应用程序的 Docker 容器中设置本地 DynamoDB。

# 奖金

我们上面所举的例子非常简单和基本。在本节中，我们将更深入地了解如何做到以下几点:

*   定义由哈希键和排序键组成的主分区键。
*   定义一个二级索引。

## 带有哈希键和排序键的主分区键

在许多情况下，我们的 DynamoDB 表既有一个散列键，又有一个排序键，这两个键构成了它的主分区键。DynamoDB 增强客户端为此提供了`@DynamoDbSecondarySortKey`注释。

假设我们有一个存储客户账户活动的账户表。例如，可以创建、激活和关闭活动。因此，如果我们可以查询一个帐户并通过时间戳查看其活动，这是一个好主意。

下面是我们如何在数据类中表示 DynamoDB 映射。

带有哈希键和排序键的 AccountActivity.kt

## 辅助索引

有时，我们可能希望在 DynamoDB 表中添加辅助索引，以便使用不同的主键集查询该表。

假设我们有一个存储客户银行账户详细信息的表。主分区键将是帐户 id；但是，每个客户可以有多个账户，每个账户可以有不同的金额。

在这个例子中，二级索引的一个用例可能是，我们希望找到属于一个客户的帐户列表，按金额排序。下面是如何在我们的数据类中表示它。

带有二级索引的 Account.kt

# 抓住你了

我还没有玩够 DynamoDB Enhanced Client，但是，我想分享我目前遇到的一个问题。它与更新项目操作相关。

请记住，我们的数据类是可变的，并且有一个默认的公共构造函数，其中所有的字段都有一个默认值`null`。这意味着当我们一个接一个地执行更新条目操作时，如果我们没有为任何字段指定值，一些属性可能会被覆盖为`null`。

假设我们的`CustomerRepo`类有一个更新函数。

CustomerRepo 中更新项目功能的示例

我们这样调用这个函数一次。

```
val customerPersist = CustomerPersist(customerId = "12345", emailAddress = "email@website.com")customerRepo.updateCustomer(customerPersist)
```

这个函数调用将在客户表中创建一个具有两个属性的记录:`customerId` = 12345 和`emailAddress`= email@website.com。

接下来，假设客户更新了他们的名字和姓氏，相应的函数调用如下。

```
val customerPersist = CustomerPersist(customerId = "12345", firstName = "Joe", lastName = "Smith")customerRepo.updateCustomer(customerPersist)
```

您可能期望表中的记录具有这些属性:`customerId` = 12345、`emailAddress`= email@website.com、`firstName` =乔和`lastName` =史密斯。

不幸的是，你错了。

表中的记录应该是:`customerId` = 12345，`firstName` =乔，`lastName` =史密斯。在第二次调用`updateCustomer`函数时，`emailAddress`属性将被覆盖。

这是因为底层的`UpdateItemEnhancedRequest`。它有一个名为`ignoreNulls`的字段，默认设置为`false`。当它为`false`时，如果数据类中对应的字段为`null`，将导致 DynamoDB 表中的属性被删除(或设置为`null`)。见[公文](https://github.com/aws/aws-sdk-java-v2/blob/84679eda4ba57c22a77b373cc0171b64b4821e91/services-custom/dynamodb-enhanced/src/main/java/software/amazon/awssdk/enhanced/dynamodb/model/UpdateItemEnhancedRequest.java#L121-L134)`[ignoreNulls](https://github.com/aws/aws-sdk-java-v2/blob/84679eda4ba57c22a77b373cc0171b64b4821e91/services-custom/dynamodb-enhanced/src/main/java/software/amazon/awssdk/enhanced/dynamodb/model/UpdateItemEnhancedRequest.java#L121-L134)`。

在我们上面的例子中，第二次调用`updateCustomer`，我们将`emailAddress`作为`null`传递(这是默认值)。这就是为什么在 update item 操作完成后，`emailAddress`属性被从 customer 表的记录中删除。

如果您想改变更新项目操作的行为，即，即使 POJO 对象中的各个字段为`null`，也不从 DynamoDB 表的记录中删除属性，修改`updateCustomer`函数如下。

忽略空字段的更新项目功能

# 结论

在本教程中，我们学习了如何实现相对较新的 DynamoDB 增强客户端。我们从使用 SDK 版本 2 中的`DynamoDbAsyncClient`的代码库开始，一直到使用`DynamoDbEnhancedAsyncClient`。

我已经创建了一个 [pull 请求](https://github.com/billydh/dynamodemo/pull/1/files)，这样您就可以轻松地比较和查看实现 DynamoDB 增强客户端所需的所有更改。

希望本教程能够启发您通过实现 DynamoDB 增强客户端来升级和简化您的代码库。

# 参考

以下是我用来编写本教程的一些参考资料:

*   [https://docs . AWS . Amazon . com/SDK-for-Java/v2/developer-guide/examples-dynamo db-enhanced . html](https://docs.aws.amazon.com/sdk-for-java/v2/developer-guide/examples-dynamodb-enhanced.html)
*   [https://github . com/AWS/AWS-SDK-Java-v2/tree/master/services-custom/dynamodb-enhanced](https://github.com/aws/aws-sdk-java-v2/tree/master/services-custom/dynamodb-enhanced#working-with-immutable-data-classes)
*   [https://AWS . Amazon . com/blogs/developer/introducing-enhanced-dynamo db-client-in-the-AWS-SDK-for-Java-v2/](https://aws.amazon.com/blogs/developer/introducing-enhanced-dynamodb-client-in-the-aws-sdk-for-java-v2/)
# 用 Amazon DynamoDB 修复性能反模式及经验教训

> 原文：<https://betterprogramming.pub/fixing-a-performance-anti-pattern-with-amazon-dynamodb-and-lessons-learned-858b4e570b0d>

## 我使用 Amazon DynamoDB 的第一步

![](img/cfd2ffb125ebe22b09b3bd4f3c386b1e.png)

照片由 [Waren Brasse](https://unsplash.com/@wawa01?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我最近一直在做一个个人项目，在这个项目中，我决定使用并学习更多关于 DynamoDB 的知识。虽然我过去在一些小事情上使用过 Dynamo，但我过去的大部分经验都是在关系数据库上，我花了一些时间才理解和 NoSQL 一起工作的意义。

# 什么是 DynamoDB？

DynamoDB 是亚马逊网络服务完全管理的 NoSQL 数据库。

与将数据存储在结构化行和列中的关系数据库不同，Dynamo 数据存储在键值对中。这意味着，与您通常在关系数据库中看到的数据相比，您存储在 Dynamo 中的数据是非结构化的。您必须定义的惟一模式是您的表键:分区键和排序键。除此之外，您的数据可以是无模式的(尽管您的应用程序仍然需要知道如何处理它)。

# 我的错误

在我的项目中，我使用 Python 并通过`boto3`库访问 Dynamo。这是在 AWS Lambda 上运行的。在我的代码中，我有一个数据模型`collection`，我需要对它进行基本的 CRUD(创建、读取、更新、删除)操作。

我的代码看起来像这样:

你明白我的错误了吗？

> 我做的每一个数据库操作，都是在重新初始化`boto3`资源和 Dynamo 表。

这很容易做到(尤其是有了 [GitHub Copilot](https://levelup.gitconnected.com/is-github-copilot-worth-it-3af9d3ee1f81) 基本上就是给我写的这些功能)，我当时也没怎么想过。然而，在审查一些代码时，我的未婚夫 [Myles Loffler](https://medium.com/u/328f007d818d?source=post_page-----858b4e570b0d--------------------------------) 向我指出，这[实际上是与](https://docs.aws.amazon.com/codeguru/detector-library/python/lambda-client-reuse/) `[boto3](https://docs.aws.amazon.com/codeguru/detector-library/python/lambda-client-reuse/)` [客户端一起工作时的反模式，尤其是在 Lambda](https://docs.aws.amazon.com/codeguru/detector-library/python/lambda-client-reuse/) 上。

**注意**:虽然我在使用 Dynamo 时遇到了这种反模式，但是由于 CRUD 操作的性质，您可以在任何时候一遍又一遍地重新初始化同一个`boto3`资源时遇到它！

# 我是怎么修好的

幸运的是，这很容易修复，而且修复它使我的代码总体上更好！

我创建了一个新的`utilities`文件来处理我的数据库连接，并添加了以下函数:

在本例中，Dynamo 表名现在通过存储在 settings 中的一个独立函数进行检索。

然后，我重构了我的 CRUD 函数，只使用了`get_dynamo_table`，而不是直接使用`boto3`。

现在我可以调用新的 get_dynamo_table 函数了。

您可能已经注意到了我的数据库实用程序中全局变量的使用。虽然这通常被认为是一种不好的实践，但是我使用它们是基于 Lambda 最佳实践，该实践建议[利用执行环境重用](https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html)。

# 这有什么不同呢？

这给我的代码带来了四个主要的不同:

## 表演

这明明是大的！

为了进行测试，我部署了应用程序的两个版本，一个使用旧代码，另一个使用新代码，并运行了一些导致 CRUD 操作的工作流。

下面是我的应用程序**在没有**应用此更改的情况下的一些跟踪示例。此操作包括删除一个项目和一个查询:

![](img/c7d51e590e7e359af264e5281b48d0a8.png)

进行此更改之前的删除和查询操作，分别需要 84 毫秒和 76 毫秒。

下面是应用了这一更改的应用程序中相同的两个操作(删除和查询)的痕迹:

![](img/b0c88b6061fdd1b143620bde97233fd2.png)

进行此更改后，相同的删除和查询操作分别需要 7 毫秒和 12 毫秒。

虽然我们只是在谈论几分之一秒的时间，但这些延迟会随着时间的推移而增加！应用此更改后，应用程序感觉明显更快，响应更快。

## 清洁

这种方法不仅更容易阅读，而且从长远来看，肯定会减少我的项目中的代码。每当我需要访问数据库时，我不必导入`boto3`和访问环境变量。现在只有一个函数可以访问 Dynamo，所以如果我需要改变什么，我只需要在一个地方改变它。

## 易测性

测试也更干净。之前，我必须在每个位置模拟`boto3`，这也相当复杂，因为我模拟的是一个返回值的返回值。现在我可以模仿`get_dynamo_table`，它更加简单易懂。

## 复用性

`utils.db`中的新函数根本不关心表名——它是由设置中的`get_dynamo_table_name`函数检索的。随着项目的发展，这些代码可以在其他可能使用不同发电机表的 Lambdas 上重用。

正如我上面提到的，我对使用 Amazon 的 DynamoDB 相当陌生——我还在学习中！
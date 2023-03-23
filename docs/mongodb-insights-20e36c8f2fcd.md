# 如何决定 MongoDB 是否适合你

> 原文：<https://betterprogramming.pub/mongodb-insights-20e36c8f2fcd>

## 选择数据库不应该是一个轻率的决定

![](img/7d9c8bf389d570cbdb158aa279132205.png)

在过去的几年里，我围绕着 [MongoDB](https://www.mongodb.com/) 构建了 web 应用程序。在这篇短文中，我想回答一些大多数开发人员在评估它时经常遇到的问题或误解:

*   什么是许可？
*   MongoDB 是一个 NoSQL 数据库意味着什么？
*   MongoDB 性能怎么样？

# 批准

MongoDB 在 2018 年 10 月 16 日之前在自由软件基金会的[GNU AGPL v3.0](https://www.mongodb.com/community/licensing) 下授权。现在在 MongoDB [服务器端公共许可证](https://www.mongodb.com/licensing/server-side-public-license)下授权。实际上，这两种方法都意味着你对 MongoDB 所做的改进必须发布给社区。任何衍生作品的源代码也必须分发。

您可能想知道您的应用程序是否是衍生作品。我必须承认，我从未找到这样一个术语的简单定义。然而，在 MongoDB 的特定情况下，他们只是简单地[认识到使用他们数据库的应用程序是一项独立的工作](https://www.mongodb.com/blog/post/the-agpl)。

此外，他们支持的驱动程序是根据 Apache License v2.0 发布的。这是一个[许可许可证](https://en.wikipedia.org/wiki/Permissive_software_licence)。它不需要你发布你的源代码，你的应用程序通常只用一个驱动和 MongoDB 对话。

因此，您不需要关心 MongoDB 的许可来围绕它构建您的应用程序。如果有问题，他们甚至会向法律部门发送署名信件，表明他们的承诺。如果签名信还不够，他们还提供商业许可证。

**注:**虽然大量的经验让我相信这个分析，但我不是律师。这里提出的观点是我个人的理解，不是官方的。

# NoSQL

是的，MongoDB 是一个 NoSQL 数据库。这个词很容易让人混淆。我将尝试分析最常见的想法，重点是如何应用于 MongoDB。

## 面向文档

在传统的 SQL 数据库中，数据以表和行的形式排列。每行有固定数量的列，只能存储特定类型的数据(例如，整数、文本、日期时间)。这定义了数据的模式。

在 MongoDB 中，数据以组织成集合*的 [BSON 对象](http://bsonspec.org/)的形式存储。*数据是通常以 [JSON 对象](http://www.json.org/)的形式处理。这使得将对象映射到数据库成为一项简单的任务，通常消除了任何类似于[对象关系映射](https://en.wikipedia.org/wiki/Object-relational_mapping)的事情。

## 事务性的

在 v4 之前，MongoDB 只提供文档范围的事务。写入从未部分应用于插入或更新的文档。该操作是原子性的，即要么失败，要么成功。对于整个文档来说，据说是文档级别的 [ACID](https://en.wikipedia.org/wiki/ACID_(computer_science)) 。

因此，不可能出现跨越多个文档的原子变化。您必须模拟所需的数据库事务(例如，使用[两阶段提交](https://en.wikipedia.org/wiki/Two-phase_commit_protocol))。

从 v4 开始，MongoDB 支持多文档 ACID 事务，这使它成为唯一一个将文档模型与 ACID 保证相结合的开源数据库。

## Schemaless(真的？)

这意味着在能够管理数据之前，您不必告诉数据库您的数据的结构和要使用的基本类型。

这也意味着您可以在同一个数据集合中混合具有不同结构的文档。

最大的好处之一是模式迁移变得更加容易(对数据库的大部分调整都是透明和自动的)。回滚不太可能导致问题。另一个优点是在运行时用自定义属性动态扩展现有数据模型非常简单。

但是这一切并不意味着你根本没有任何图式。如果它没有被显式声明，它会从您的应用程序逻辑中隐含地显现出来。它可能以其他方式声明，以处理表单/数据验证。无论如何，您仍然必须明确地告诉数据库如何创建索引，以确保良好的性能。

事实上，模式设计是制作优秀数据库的基石，不管是不是 SQL。如果你不了解你的数据以及硬件和软件的限制，你就不能有效地设计一个模式。

## 非关系(真的？)

这意味着您不必总是在两个文档之间创建关系来处理聚合数据结构。

事实上，在关系数据库中，SQL `JOIN`子句允许您使用两个或更多表之间的公共字段来组合它们的行。面向文档的数据库，比如 MongoDB，被设计用来存储非规范化的数据。理想情况下，集合之间应该没有关系:如果两个或更多文档中需要相同的数据，则必须重复。最大的好处之一是只需要一次读操作就可以获得所有数据。

但是，如果您愿意或有需要，您仍然可以创建关系并引用另一个文档:

*   通过 ID，然后您可以使用第二个查询或者使用 [DBRefs](https://docs.mongodb.com/manual/reference/database-references/#dbrefs) 手工填充它
*   通过任何其他字段，那么你可以使用`[$lookup](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/)` [操作符](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/)

这使得 MongoDB 非常灵活，允许您根据具体情况选择如何处理对象之间的关系。

# 表演

## 读/写

是的，MongoDB 和其他真正的数据库一样，是为处理海量数据而设计的。简而言之，处理数百或数千个对象对于一个数据库来说不算什么，所以如果您有这样的数量，就不必担心。

你可以在周围找到很多基准。这里有一个简单的给你一些粗略的数量级。存储的文档非常简单，通常代表带时间戳的测量:

```
{
    value: random(0,100),
    timestamp: date
}
```

由于 MongoDB 将内存管理委托给操作系统的方式，拥有更复杂的文档(通常包含数十个属性)不会对结果产生显著影响。

这两个属性都已被索引。MongoDB 自动添加并索引文档惟一 ID。我测试了三个请求:

*   使用[聚合框架](https://docs.mongodb.com/manual/aggregation/)找到集合的最大值
*   找出大于 99.9 的 100 个最大值
*   通过 ID 获取单个文档

由于聚合，最大请求不能从索引中受益，而大于和按 ID 请求可以使用它。您将看到这对性能有多么重要。

测试配置为 MongoDB 3.4.1 64 位—OS Windows 7 Pro SP1—CPU Core i7–4712 HQ 2.3 GHz—16Go RAM—SSD HD，测试结果如下:

因此，如果您构建了正确的索引来查询 10 亿个文档，对于单个服务器上的大多数应用程序来说，它仍然具有足够的性能。如果需要，您可以使用[分片](https://docs.mongodb.com/manual/sharding/)来提高性能。

以下是用于创建/查询该测试数据库的脚本:

和运行命令:

```
// Launch server
./mongod --dbpath "C:\Program Files\MongoDB\Server\3.4\data" --port 27018
// Insertion exemple for 10e7
./mongo --port 27018 --eval "var arg1=10000000" create_collection.js
// Requests
./mongo --port 27018 --eval "" query_collection.js
```

## 记忆

是的，MongoDB 通常看起来使用了所有可用的 RAM。它实际上依赖于不同的存储引擎。 [WiredTiger](https://docs.mongodb.com/manual/core/wiredtiger/) 是 MongoDB 3.2 开始的默认， [MMAPv1](https://docs.mongodb.com/manual/core/mmapv1/) 是 MongoDB 3.2 之前版本的默认。然而，它们的工作原理非常相似。通过文件系统缓存，它们自动使用所有未被引擎缓存或其他进程使用的空闲内存。如果你想有最大的表现，这是一致的。

所以系统资源监视器经常显示 MongoDB 使用大量内存，但是它的使用是动态的。如果另一个进程突然需要一半的服务器内存，MongoDB 会将缓存内存让给另一个进程。

因此，可以优化内存使用的唯一参数就是引擎缓存大小。例如，在默认情况下，WiredTiger 引擎使用 50%的 RAM 减去 1 GB，这对于具有大量内存的服务器来说是相当大的。如果你使用有限内存的容器，这甚至会引起一些麻烦，所以简单地找出适合你的用例的平衡。

# 结论

如果 MongoDB 适合您的需要，我希望您现在对它提供的好处有一个更精确的概念。

最近，MongoDB 启动了一个名为 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) 的数据库即服务产品，可能对您的测试有用。
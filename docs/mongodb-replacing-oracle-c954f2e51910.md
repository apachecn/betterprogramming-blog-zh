# MongoDB 有可能取代 Oracle 数据库吗？

> 原文：<https://betterprogramming.pub/mongodb-replacing-oracle-c954f2e51910>

## 为什么 MongoDB 将成为企业应用程序的下一个大 IT 合作伙伴

![](img/71bea15c8933444f6153dce4148e3cf3.png)

照片由[法比奥](https://unsplash.com/@fabioha?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

NoSQL 数据库非常有趣，并且由于其可靠、灵活和开放源代码而越来越受欢迎。大多数欧洲企业应用程序使用 Oracle 技术和数据库，这表明他们的预算管理效率低下，并且对开源技术不感兴趣。MongoDB 有机会扩展其企业解决方案并成为该市场领域的新 IT 合作伙伴吗？

# 甲骨文为什么这么受欢迎？

甲骨文的技术是企业解决方案营销领域第三大常用技术。这些技术也很受欢迎，分布很广。甲骨文最大的竞争对手之一是微软。微软持有。NET 技术，包括 Windows 服务器、Sharepoint、Office 365 和 SQLServer 作为 DBMS。他们也有我们称之为“开源世界”的东西，包括 PHP、MySQL、NoSQL 数据库、Python 等等。我合作过的许多客户和公司都选择与甲骨文合作，有时甚至是微软。

欧洲开源技术的市值通常较低，因为公司需要支持和企业工具来管理大型数据库。许多公司还要求支持分片、分区或副本集。他们基本上是横向扩展，他们希望能够依靠大公司所能提供的东西。这就是为什么绝大多数政府 IT 基础设施使用 Oracle 作为其主要数据存储引擎的原因。

Oracle 无疑是数据管理服务的顶级提供商，但情况正在慢慢发生变化。NoSQL 数据库是一场伟大的革命，它提供了一种新的数据建模和处理方式。

用于服务器实例、支持、集群等的 Oracle 许可证可能会变得非常昂贵。MongoDB 真的能有所作为，成为甲骨文的新竞争对手吗？对我来说，答案是肯定的，但只是在特定的背景下。

# 需求考虑因素

1.  **MongoDB 并不是任何 web 应用程序的最佳选择**。很难解释为什么，但是 NoSQL 数据库不适合任何软件。BSON 类型可以适用于几乎任何类型的数据，但是请记住，选择 MongoDB 意味着放弃关系、外键、PL/SQL(可以通过 JavaScript 例程，甚至批处理例程来模拟)、结构化数据和强大的 ORM。所以，这基本上取决于你需要对你的数据建模的方式。
2.  **MongoDB 是开源的，提供企业和定价的服务和工具，但 Oracle 仍然更好**。即使你可以依靠 MongoDB Atlas、MongoDB BI、MongoDB DevOps 工具、MongoDB 图表，你可能仍然更喜欢 ElasticSearch 或高级 Oracle 工具。集群监控呢？这是同样的故事。MongoDB 为您提供了很好的工具，但是 Oracle 和其他企业工具更好。
3.  **MongoDB 文档不是 RDBMS 记录。**只有当您能够“层次化”而不是“水平化”地建模数据时，才选择 MongoDB。
4.  **比起 Java** ，MongoDB 更喜欢 JavaScript。通过 JavaScript/NodeJS 处理 JSON 文档比 Java 更容易。在全均值堆栈中甚至更容易。您可能还会发现 Mongoose ORM 功能不太强大，而且它有时会暴露 Hibernate 中不可预测的行为。
5.  **查询 MongoDB 比使用 SQL** 更难。您可以使用`mapReduce()` 和`aggregate()`来设计复杂的查询，但是 SQL 是一种许多人已经使用了多年并且熟悉的语言。如果您的查询非常简单，请选择 MongoDB。否则，您需要花一些时间来掌握分段和内存高效的查询。
6.  我无法列出 MongoDB 的所有特性，也无法比较 Oracle 和 MongoDB 的所有特性。但是请记住，NoSQL 并不适合所有人。

# 使用 MongoDB 的好处

1.  **MongoDB 的性能优于 Oracle** ，如果采用正确的分片方式，它甚至会更快。只要试着把一百万份文件收集起来，你就能自己看到了。或者，谷歌“MongoDB 基准测试”
2.  **如果数据结构简单，MongoDB 很容易使用**。如果您有不太嵌套的文档，查询和操作数据就非常容易。
3.  **MongoDB 易于水平扩展**，因为副本集实现几乎不需要时间，并且提供高可用性。分片技术也很容易实现，但很难设计。但是没关系，因为分片本身就是一个很难设计的概念。
4.  MongoDB 的成本远低于 Oracle。即使您必须支付特定支持或专业 DBA 管理员的费用，也是如此。
5.  **MongoDB 可以使用模式**。你可以在 MongoDB 上设置模式验证，或者在 ORM 级别提供验证(参见[mongose](https://mongoosejs.com/docs/validation.html))。
6.  **MongoDB 的特性比 Oracle 少…但是谁需要它们呢**？如今，处理许多需要驻留在像 MongoDB 这样的数据库中的企业应用程序并不罕见。我们不能加入，不能轻易触发，甚至没有完整性约束……但是我们确定我们需要那些东西吗？

# 结论

我认为变革之风正吹向 MongoDB。只要给它一些时间在欧洲流行起来，它就会改变政府和大公司投资 it 资源的方式。我个人很喜欢它的特性，如果在正确的地方以正确的方式实现，它真的可以给你全新的应用带来很大的改进。
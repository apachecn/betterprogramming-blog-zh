# 在 Amazon Redshift 上构建数据仓库

> 原文：<https://betterprogramming.pub/building-a-data-warehouse-on-amazon-redshift-49a620a5392f>

## 这是适合你的系统吗？

![](img/e6d2f8aa34c7f30806d304018509f8df.png)

Jezael Melgoza 在 [Unsplash](https://unsplash.com/s/photos/warehouse?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

随着组织的成长，其数据存储、监控和分析需求也呈指数级增长。传统数据仓库并不总能轻松处理大量增长。这导致从 2000 年代中期开始需要替代解决方案。一个这样的解决方案是来自亚马逊网络服务的亚马逊红移。

# **什么是亚马逊红移？**

Amazon Redshift 是一个基于云的数据仓库，专为大规模数据集设计。它的创建是为了解决以前的数据仓库存在的许多问题，并对它们进行大幅改进。它通过特定的设计决策来实现这一点，例如以列为中心、能够跨多个节点水平扩展以及大规模并行处理。每一个具体的设计决策都允许 AWS 开发一个数据存储系统，它的运行方式与传统的 RDBMS 截然不同。

# **高层架构**

较大的组织引入了高层架构，这显然是复杂的。红移是怎么处理的？

## **面向列的数据库**

与传统的数据库管理系统相反，Redshift 按列存储数据。通过每次按列对数据进行分区，您可以检索一个值，这也意味着每个分区的单个值减少了硬盘的负载，从而为大量数据带来更快的速度。此外，红移还有压缩的概念。

通过压缩相同值的列，可以进一步改善 I/O，因为每列的所有数据都是相同的数据类型。[这确实需要开发人员根据数据类型](https://docs.aws.amazon.com/redshift/latest/dg/c_Compression_encodings.html)设置正确的压缩类型。

## **红移聚类**

每个红移集群都由多台存储一小部分数据的机器组成。这些机器并行工作，保存数据，因此我们可以有效地处理它。这里，Redshift 有一些计算节点，它们由领导者节点管理，以管理计算节点之间的数据分布和查询执行。将这个分数数据集分配给每个节点后，红移的执行效率会更高。这也使得它具有水平可伸缩性。更大的组织甚至可以使用 Redshift 来处理 Pb 级甚至更高的数据。

## **大规模并行处理(MPP)**

为了更快地处理数据，Redshift 员工使用多个计算节点，这使得工作可以分散到多个节点上(取决于分布情况)，从而提高性能。这使得红移架构能够在最短的时间内提供最大的处理能力。

这些只是一些具体的架构差异，在决定红移是否适合您时，这些差异非常重要。

此外，你的团队需要考虑如何设计他们的桌子。随之而来的是排序键和分布的概念。

# **排序键**

您是否在传统数据库中使用索引来提高查询运行时间？

Redshift 还通过基于键对数据进行排序，提供了改进查询运行时的方法。在这种情况下，它们被称为排序键。红移排序键确定数据行在表中的存储顺序。正确配置这些排序关键字后，它们可以提高查询性能，因为它们减少了返回的数据量。排序键允许查询在执行查询处理时跳过大块数据，这也意味着红移需要更少的处理时间。

有两种特定的排序关键字:

*   **复合排序关键字:**这些关键字包含创建表时红移排序关键字定义中列出的所有列。它遵循类似于 SQL 中子句的顺序，具体来说，就是先对第一列排序，然后对前面的列排序。这通常意味着第一列应该是查询最多的列。复合是排序键的默认类型，这也加快了连接的速度。使用复合排序键，您可以轻松地创建连接操作，如 Group By 和 Order By。
*   **交错排序关键字:**交错排序关键字对每一列赋予相同的权重，这将提高在次级排序列上使用限制性谓词时的查询性能。如果查询中没有主列，那么这个键可能是正确的选择。

# **分配**

另一个至关重要的因素是理解红移中数据存储在哪里。Amazon 红移分布键通过确定数据的存储位置来回答这个查询。从根本上说，数据是由集群跨计算节点存储的，因为如果大量数据存储在单个节点上，查询性能会受到很大影响。优化的自动查询将数据均匀地分布在不同的节点上，这些节点可以在以后进行连接。

这里可能会发生一些有趣的事情，这让你思考如何分配变得很重要。我们将在下面详细讨论这一点，但是，特别是，当您使用密钥分布时，您有可能无法均匀分布。红移有三种主要的分布类型。

## **均匀分布**

默认情况下，这种方法将数据均匀地分布在表中的所有片上。在他们的文档中，AWS 将这种平均分配称为循环法。它允许集群以合理的顺序平均分配所有数据，这使得系统的处理更加简单。然而，在很大程度上，这意味着分布是随机的，不会被任何特定的字段分割。这是下一种发行风格的切入点。

## **密钥分发**

使用这种方法，数据是根据列保存的值分布的。例如，假设您在商店 id 上有一个键集，并且有每天的销售额。分布键将分解和分布数据，以便特定商店 id 的所有数据都在同一个节点上。这样，如果您查询特定的商店 id，就不必遍历多个节点来获取数据。

## **所有配送**

在这种方法中，领导者节点在每个计算节点上放置一个表的副本。这会占用更多的空间，而且更新和插入数据的速度会慢很多，因为每次加载数据时都需要复制数据。但是，它可以使连接更快。这通常是标注样式表的分布设置。

# **亚马逊红移的弊端**

红移为开发数据仓库提供了许多积极的好处。然而，它并不缺少缺点。

## **非实时应用数据库**

抱歉戳破了这个泡泡，虽然 Redshift 在运行查询、处理分析和创建报告方面表现出色，但它不是运行实时 web 应用程序的解决方案。Redshift 不是为处理成百上千的事务而设计的，这些事务可以同时插入、更新和选择。为此，您需要一个缓存层或 Postgres 实例。

## **开发人员技能集**

要使用 Redshift，您必须了解这两个键，因为它们决定了数据的存储和索引方式。因此，当您选择 redshift 来满足您的数据仓库需求时，您还必须理解这些关键字背后的概念，以创建您的组织所追求的优化性能。因为 Redshift 仍然相对较新，所以并不是所有的 BI 和数据专业人员都开发了数据仓库

## **仅支持并行上传**

说到红移，并行加载只得到 Amazon EMR、SE 和 DynamoDB 的支持。他们是唯一使用大规模并行进程并给你高速的。如果数据源不同，则不支持此功能。

# 最终结论:你应该为你的下一个项目选择它吗？

如果您的组织希望创建一个能够轻松管理大量数据和分析查询的数据仓库，那么可能值得考虑 Redshift。虽然这项服务有其缺点，但它仍然有许多值得投资的用例。

即使有学习曲线，如果您试图管理大量数据，这也是值得的。我们发现，许多公司将使用 Redshift 作为基础，然后应用将数据插入某种 RDBMS 的业务逻辑。这可以让数据产品轻松访问数据，而不会与分析发生冲突。
# 使用 SQL 聚合函数提高性能

> 原文：<https://betterprogramming.pub/improving-performance-with-sql-aggregate-functions-e1e126464e93>

## 提升应用程序的性能

![](img/47b648aa73bedf503acc2a65716e0fc9.png)

在本文中，您将了解 SQL 聚合函数是如何以一种简单的方式显著提高应用程序性能的。主要是，您将看到他们如何在一个基于数据驱动的应用程序的真实世界场景中改变游戏规则，该应用程序是为体育行业的初创公司开发的。

现在让我们更深入地研究这个场景，了解为什么在数据科学中不能忽略 SQL 聚合函数。

# **场景**

我最近开发的应用程序旨在通过网络提供体育界的高级数据探索功能。特别是，它需要允许探索原始数据和聚合数据。由于数据库涉及数万亿字节的异构和非结构化数据，挑战主要在后端和数据库端。现在，让我们深入这个场景。

## 技术、服务器规格和架构

我们在 Kotlin 用 [Spring Boot](https://spring.io/projects/spring-boot) [2.5.3](https://spring.io/blog/2021/07/22/spring-boot-2-5-3-is-now-available) 框架和[Hibernate](https://hibernate.org/)5 . 4 . 32 . finalORM([对象关系映射](https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping))开发了后端。我们通过由 [Dokku](http://dokku.viewdocs.io/dokku/) 管理的 [Docker](https://www.docker.com/) 容器将其部署在 8GB 4 CPU [VPS](https://it.wikipedia.org/wiki/Virtual_private_server) 上。初始堆大小设置为 2GB，限制为 7GB，而我们将剩余的 GB 内存分配给基于 Redis 的缓存系统。我们构建 web 应用程序时考虑到了性能。具体来说，它基于这里[描述的](https://medium.com/swlh/designing-a-multi-layered-architecture-for-building-restful-web-services-with-spring-boot-and-a12ef85b77c9)多层 Spring Boot 架构，并涉及多线程处理。

## 数据库结构

我们将数据库实现为运行在 8GB 2 CPU VPS 上的 MySQL 服务器。我们在同一个服务器群中托管后端应用程序和数据库，但是它们不共享相同的 VPS。由于体育数据简单但高度异构，该数据库的结构[避免重复](https://arctype.com/blog/2nf-3nf-normalization-example/)并鼓励标准化。这种结构就是我们选择关系数据库的原因。按照现在的情况，这个数据库涉及数百个表格，由于一个 [NDA](https://en.wikipedia.org/wiki/Non-disclosure_agreement) ，我不能在这里全部展示。

幸运的是，问题最多的表或多或少都有相同的结构。因此，只分析一个表就足够了。具体来说，PositionalData 表如下所示:

```
CREATE TABLE `PositionalData` (
	`id` int(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
	`area1` double DEFAULT NULL,
	`area2` double DEFAULT NULL,
	`area3` double DEFAULT NULL,
	`area4` double DEFAULT NULL,
	`area5` double DEFAULT NULL,
...
	`area140` double DEFAULT NULL,
	`area141` double DEFAULT NULL,
	`area142` double DEFAULT NULL,
	`area143` double DEFAULT NULL,
	`area144` double DEFAULT NULL,
	`value` double DEFAULT NULL,
	`parameterId` int(11) NOT NULL,
	`gameId` int(11) NOT NULL,
	`createdAt` datetime DEFAULT CURRENT_TIMESTAMP,
	`createdBy` int(11) DEFAULT NULL,
	`updatedAt` datetime DEFAULT CURRENT_TIMESTAMP,
	`updatedBy` int(11) DEFAULT NULL,
	`deletedAt` datetime DEFAULT NULL,
	`deletedBy` int(11) DEFAULT NULL,
	`active` tinyint(1) DEFAULT '1',
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

如您所见，它涉及 100 多个列，并且有四个以上的外部 id。平均而言，每个表至少包含 1500 万行。

## 性能问题

前端应用程序的一个关键功能是让用户分析来自一个或多个赛季的所有选定比赛的数百个不同运动参数(例如传球、投球、盖帽)的聚合值。我们开发了一个后端 API 来对前面提到的表执行查询以检索数据。这样的查询只不过是一个从 10k 到 20k 行返回的简单选择。然后，这个数据用多线程进程聚合，存储在 [Redis 缓存](https://redis.io/topics/lru-cache)中，最后在 JSON 中序列化，返回给前端应用。从 API 收到命中的第一个时刻(因此，在 Redis 缓存中提供结果之前)到完成，用户必须等待两到四秒钟。

这种拖延是不可接受的。

# **钻研性能问题**

现在让我们来看看刚才介绍的方法有哪些缺点。

## ORM 数据转换瓶颈

大多数高级 ORM 抽象了它们如何在数据库级别表示数据。换句话说，ORM 执行查询，从数据库中检索所需的数据，并负责将其转换成应用程序级别的表示。这个数据转换过程发生在幕后，但它无疑代表了一种开销。尽管从性能的角度来看，这个过程通常可以忽略不计，但它很快就会成为数千行的瓶颈。

当使用 OO ( [面向对象](https://en.wikipedia.org/wiki/Object-oriented_programming))语言时，这种减速尤其可能。此外，创建新的类实例需要时间和资源。限制对象大小和堆使用的一种方法可能是只选择严格必要的一组列。这种方法会使每个对象变得更轻，即使对象创建过程是主要的开销。所以执行这个转换过程所花费的时间不会有很大的变化。

## 循环需要时间

对包含数千个元素的对象数组执行简单的操作，如求和或求平均值，并不是没有性能的。尽管这比不上 ORM 转换数据所花费的时间，但它确实代表了额外的开销。幸运的是，Java 支持许多线程安全集合并发执行操作。另一方面，打开和管理线程是复杂且耗时的任务。

让我们看看几个 SQL 聚合函数是如何帮助我解决性能问题的。

## 什么是 SQL 聚合函数？

SQL 聚合函数允许您计算几行并获得一个结果值。尽管每种 SQL 语言都有自己的聚合函数，但最常见的有:

*   `COUNT()`:返回所选行数的计数
*   `MIN()`:提取最小值
*   `MAX()`:提取最大值
*   `SUM()`:执行求和运算
*   `AVG()`:执行平均运算

当与 GROUP BY 语句结合使用时，它们代表了一种有效且有用的工具。有了它，您可以首先对所需的数据进行分组，然后利用它们进行聚合。如果你想深入研究 MySQL 聚合函数，你可以在这里找到所有支持的[。我也推荐看看](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html)[这个](https://learnsql.com/blog/sql-aggregate-functions/)和[这个](https://www.sqlshack.com/learn-sql-aggregate-functions/)。

# 用查询替换应用程序级操作

虽然 SQL 聚合函数看起来很有前途，但在看到它们发挥作用之前，我不知道它们是否会有所不同。具体来说，应用程序级操作生成了一个数据结构，该数据结构包含 value 列上的平均值和每个`areaX`(X 从 1 到 144)列上针对所选游戏选择的每个参数的总和。您可以在下面的查询中轻松地表示这一点:

```
SELECT SUM(`area1`) as `area1`,  
	SUM(`area2`) as `area2`, 
	SUM(`area3`) as `area3`,
...
	SUM(`area142`) as `area142`, 
	SUM(`area143`) as `area143`, 
	SUM(`area144`) as `area144`,
	AVG(`total`) as `total`, `parameterId`
FROM `PositionalData`
WHERE `parameterId` IN (:parameterIds) AND `gameId` IN (:gameIds)
GROUP BY `parameterId`
```

如您所见，该查询利用 SQL 聚合函数在数据库级别返回聚合数据。同时在`gameId`和`parameterId`上使用 IN 语句过滤所需的数据，并根据相同的`parameterId`对其进行分组。换句话说，首先根据赛季的选定比赛和需要分析的参数过滤数据。然后，结果信息按参数分组，并由 SQL 聚合函数聚合。

## 定义正确的索引

由于该查询涉及 GROUP BY、IN 和 SQL 聚合语句，因此可能会比较慢。这种潜在的缓慢正是定义适当的索引如此重要的原因。具体而言，最关键和最有效的绩效指标如下:

```
ALTER TABLE `PositionalData` ADD INDEX `PositionalData_parameterId_gameId` (`parameterId`, `gameId`) USING BTREE;
```

那么你应该总是使用集合函数吗？这种方法的一些优点和缺点。

**赞成者**

*   数据库级聚合比在大型数组上循环时在应用程序级执行相同的聚合逻辑要快得多。
*   将涉及 SQL 聚合函数的查询与 GROUP BY 语句一起使用，可以大大减少返回的行数。具体来说，这让我从大约 10k 行增加到与所分析的参数数量相等的行数。因此，这使得 ORM 执行的数据转换过程与时间无关，从而避免了成为瓶颈。
*   当运行相同的请求时，数据库级别的聚合允许您利用数据库缓存的性能优势。这种设置可以降低应用程序级缓存的重要性，从而简化架构。

**缺点**

*   SQL 聚合函数在选择时间运行。当处理强类型编程语言时，ORM 需要知道结果的类型。并不是所有的 ORM 都允许您轻松定义，有时甚至会将 SQL 聚合函数限制在本地查询中。这种现实意味着失去 ORM 引入的抽象优势，并阻碍了它们的使用。
*   涉及 SQL 聚合函数的提取所需数据的查询总是比涉及简单 WHERE 子句的 SELECT 慢。然而，执行时间应该保持在十分之一秒的数量级，并且无论如何，要比在应用程序级别执行相同的操作少得多。
*   可用的 SQL 聚合运算通常被限制在一打以内，其中只有 5 或 6 个是数学运算。

# **性能对比**

让我们比较一下调用相同的 API(涉及数据聚合，没有缓存，参数相同)时的响应时间。

*   在应用程序级别执行聚合时的响应时间:~ 2–4 秒
*   在数据库级别执行聚合时的响应时间:~ 800 毫秒

# **关于 SQL 聚合函数的最终想法**

在处理数据科学时，SQL 聚合函数无疑是将性能提升到一个新水平的绝佳工具。使用它们既简单又有效，尽管并不是所有的 ORM 都完全或天生支持它们。无论哪种方式，知道如何利用它们可能对提高性能至关重要，我写这篇文章就是为了通过真实的案例研究来解释这一点！
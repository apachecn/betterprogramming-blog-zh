# 处理缓慢的 SQL 查询？让“解释”语句来处理它

> 原文：<https://betterprogramming.pub/how-to-understand-explain-query-plans-part-1-of-2-b0ad173af333>

## 了解解释查询计划(第 1 部分，共 2 部分)

![](img/27f16dbd714f2d5ab33509e8cc236a12.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com/s/photos/magnifying-glass?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

您是否曾经遇到过运行缓慢的 SQL 查询，但您甚至不确定从哪里开始寻找优化？如果有某种方法可以准确定位查询的哪些部分导致整个运行缓慢，那么您就可以将精力集中在那里，而不是到处进行调整。

这正是`EXPLAIN`语句允许我们做的。`EXPLAIN`语句将为您提供由数据库的查询规划器生成的查询计划，并为您提供对查询中开销最大的部分的估计。然而，一开始破译查询计划的每个部分告诉我们什么可能有点棘手。

在本文中，我们将介绍如何理解查询计划告诉我们什么，以及如何使用这些信息来提高查询的性能。

本文是 2 篇系列文章的第 1 部分，重点在于理解使用`EXPLAIN`语句的查询计划器输出。

我们在这里将使用 Postgres 作为我们的数据库，所以请注意，如果您使用不同的 RDBMS，可能会有一些微小的差异，特别是在 Postgres 相关的特性方面。如果您正在使用数据仓库，您可能实际上有一套单独的工具来分析您的查询性能，因为 RDBMS 和数据仓库以不同的方式存储和检索数据。

# 设置

首先，让我们创建一个基本表，我们可以使用它来编写查询和分析性能。

```
CREATE TABLE user_event_log (
    id SERIAL PRIMARY KEY,
    event_id INTEGER,
    event_metadata JSONB,
    user_id INTEGER,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

虽然我们不会创建它们，但是我们将假设 event_id 和 user_id 是对单独的事件和用户表的外键引用。

接下来，我们插入一些数据。

```
INSERT INTO user_event_log(event_id, event_metadata, user_id) VALUES
(1, ‘{“field”: “some value”}’, 1),
(2, ‘{“field”: “some value”}’, 1);
```

# 基本解释和相关术语

虽然我们目前只有两行，但是实际上已经有很多有趣的信息可以从这里获得，所以让我们开始使用我们的解释查询。

```
EXPLAIN SELECT *
FROM user_event_log
WHERE user_id = 1;
```

这是我们的输出:

```
Seq Scan on user_event_log (cost=0.00..21.50 rows=5 width=60)
 Filter: (user_id = 1)
```

好吧，这意味着什么？让我们从实际的解释关键字开始，一点一点来。

EXPLAIN 关键字将为我们提供查询计划，即查询计划器认为是最佳的方法，如果执行该查询，将采用该方法来执行该查询。此外，我们还将获得一些估计的成本指标。

在更复杂的计划中，我们将在后面看到，我们可以得到多个节点作为输出，但是在这个查询中，我们只得到一个节点。

我们首先看到的是`Seq Scan`关键字，换句话说，我们有一个全表扫描。这意味着在执行查询时，我们的查询将遍历表中的每一条记录。呀！

当我们只有两行时，这显然不是问题，但是想象一下，有一个有 1000 万行的表，你现在每秒钟运行这个查询多次。这意味着大量的数据将从磁盘读入内存，只有相关的位被保留。

这也是`Filter: (user_id = 1)`语句的用武之地。

这里发生的事情的全貌是，我们的查询遍历我们的整个数据，从磁盘读取数据，并且它找到的符合这个条件的任何记录将被保留以随查询返回，其余的被丢弃。

我们马上就来看看如何解决这个问题。你们中的一些人可能已经知道这里的解决方案是索引，但是让我们一步一步来，看看括号中的输出。

我们也看到这个:`cost=0.00...21.50`。这是什么意思？

这是 Postgres 对这部分查询的成本的估计。成本是以任意单位表示的，它本身并不意味着什么，但是比较查询的不同部分之间的相对成本是非常有用的。在我们的例子中，我们还没有多个部分，但这在以后会很有用。

`0.00`是预计的启动成本。启动成本是对这部分查询生成第一行所需时间的度量。在这种情况下，它是 0，但我们稍后会看到，这并不总是从 0 开始。

`21.50`是这部分查询从开始到结束的总开销。

关于这两个成本组件，需要注意的重要一点是，它们是累积的，这意味着较高节点的成本包括较低组件的成本。

最后，Postgres 估计它将返回 5 行，并且以字节为单位的大小(它称之为宽度)是每行 60 个字节。行估计显然是错误的，但这没关系，我们稍后会在您应该担心的时候讨论行估计。

# 基本优化

现在我们已经看到了我们的查询计划，让我们稍微优化一下。当您看到全表扫描时，通常会考虑通过在列上创建索引来优化它。

```
CREATE INDEX event_log_user_id_idx ON user_event_log(user_id);
```

我们的查询计划现在看起来像这样:

```
Seq Scan on user_event_log (cost=0.00..1.02 rows=1 width=60)
 Filter: (user_id = 1)
```

你现在可能会想，等等，这不就是我们之前的计划吗？

是的，我们实际上已经到了一个有趣的边缘情况。

Postgres 现在必须在使用索引和使用全表扫描之间做出决定，它认为在这种情况下全表扫描实际上是更有效的操作。通常，当您最终返回表中至少 10%的数据时，全表扫描比索引扫描更有效。

这是因为使用索引需要几个 IO 操作—查找索引，然后查找根据索引确定的相关记录。而全表扫描只是直接读入记录。

如果我们使用一个简单的 python 脚本向数据库中添加更多的数据

然后再次尝试我们的`EXPLAIN`查询，我们的查询计划如下所示:

```
Index Scan using event_log_user_id_idx on user_event_log (cost=0.28..8.37 rows=5 width=117)
 Index Cond: (user_id = 1)
```

我们可以看到，我们从全表扫描和过滤器切换到了索引扫描和索引条件。

现在，在我们继续讨论这个节点之前，你们中的一些人可能已经注意到，我们现在总共有 219 个用户。难道不应该有 10 个用户就足够了吗？10%门槛？好吧，是的，但也不是。原因是我们处理的是一个非常小的表，实际上我们还受到这个尺度的缓冲区的影响。我们将在第 2 部分回到缓冲区，但这里不会偏离太多。

回到我们的新输出——我们看到我们在上面创建的索引`event_log_user_id_idx`上有一个`Index Scan`,我们可以看到我们的索引是使用过滤器`(user_id = 1)`中的条件扫描的。这意味着我们遍历索引，通过从索引中查找我们需要的相关记录，从索引中找到我们需要从磁盘中读取的行。

如果我们为额外的用户改变我们的过滤条件，我们将看到`Index Scan`仍然存在，但是我们的索引条件得到更新。这是因为我们可以对所有这些过滤条件使用相同的索引。

```
EXPLAIN SELECT *
FROM user_event_log
WHERE user_id in (1, 12, 87)
```

这给出了:

```
Index Scan using event_log_user_id_idx on user_event_log (cost=0.28..17.09 rows=15 width=117)
 Index Cond: (user_id = ANY (‘{1,12,87}’::integer[]))”
```

然而，将我们的过滤条件更改为也包括例如 event_id，我们添加了一个额外的过滤操作，因为我们在(user_id，event_id)上没有索引。

```
EXPLAIN SELECT *
FROM user_event_log
WHERE user_id = 1 and event_id = 2
```

给予:

```
Index Scan using event_log_user_id_idx on user_event_log (cost=0.28..8.38 rows=1 width=117)
 Index Cond: (user_id = 1)
 Filter: (event_id = 2)
```

这里发生的事情是，我们基于索引查找加载所有与`user_id = 1` 相关的记录，然后使用过滤条件`event_id = 2`过滤那些行。

如果我们在 event_id 和 user_id 上都有一个索引，我们将恢复到只进行一次索引查找。

```
CREATE INDEX event_log_user_id_event_id_idx ON user_event_log(user_id, event_id);
```

导致:

```
Index Scan using event_log_user_id_event_id_idx on user_event_log (cost=0.28..8.30 rows=1 width=117)
 Index Cond: ((user_id = 1) AND (event_id = 2))
```

请注意，这并不总是最好的步骤。一般来说，应该尽量减少表中的索引数量。这是因为每次数据改变时，所有的索引都需要更新。如果你的数据经常改变，这会增加数据库的开销，因为它需要维护所有的索引。因此，请确保您只有实际使用的索引。

同样重要的是，要记住考虑你工作的环境。查询计划提供了关于计划如何执行查询的信息，但是接下来要由您来决定这是否有潜在的问题，或者计划是否真的可以。

例如，您的应用程序中可能有我们上面非常简单的选择查询:

```
SELECT *
FROM user_event_log
WHERE user_id = 1 and event_id = 2
```

您会注意到，随着负载的增加，性能在下降。运行`EXPLAIN`计划，发现实际上只有 user_id 上的索引，而没有 user_id 和 event_id 上的索引。天真地添加(user_id，event_id)索引实际上会导致额外的性能问题，因为实际问题可能是您在元数据列上也有一个索引，而增加的负载会导致您的数据库必须做大量的后台工作来维护这个索引。添加一个额外的索引并不总能神奇地解决你的问题，有时它实际上可能只会让情况变得更糟。

事实上，我记得有一次我们将一个分析 ETL 任务的结果批量导入到我们的 Postgres DB 中，在那里我们确实遇到了这些问题。尽管批量 upsert 本身在大约 15 分钟内就完成了，但是由于索引更新和自动清空过程被触发，我们数据库的 CPU 负载在整整一个小时内都超过了 80%。在这种情况下，从 upserts 迁移到 inserts，只对陈旧数据进行延迟删除作业，并删除大量未使用的索引，为我们解决了这个问题。

所以请记住，永远要考虑你工作的整个环境。

# 更复杂的查询

既然我们已经花了时间来理解和解释节点结果中的每个元素告诉我们什么，那么让我们来看看一些更复杂的查询。

为了帮助我们，我们将在这里创建第二个表，它包含每个 event_id 到它们各自名称的映射。

```
CREATE TABLE IF NOT EXISTS event (
 id SERIAL PRIMARY KEY,
 name TEXT
);
```

并插入一些数据

```
INSERT INTO event(id, name) VALUES
(1, ‘login’), (2, ‘like’), (3, ‘comment’), (4, ‘follow’), (5, ‘subscribe’);
```

现在让我们在 user_event_log 表上执行一个连接，并过滤掉除了`like`事件之外的所有事件。

```
EXPLAIN SELECT uel.*
FROM user_event_log uel
LEFT JOIN event e on e.id = uel.event_id
WHERE e.name = ‘like’;
```

给予:

```
Hash Join (cost=25.95..79.75 rows=5 width=117)
 Hash Cond: (uel.event_id = e.id)
 -> Seq Scan on user_event_log uel (cost=0.00..50.92 rows=1092  width=117)
 -> Hash (cost=25.88..25.88 rows=6 width=4)
     -> Seq Scan on event e (cost=0.00..25.88 rows=6 width=4)
        Filter: (name = ‘like’::text)
```

这里首先出现的是，我们现在有几个节点，并且我们还有嵌套(或子+父)组件。

我们阅读这些查询计划的方式是从内向外。因此，首先发生的步骤是对事件表进行顺序扫描并应用过滤器。

下一层很有趣，因为我们在同一层有两个节点。这里首先发生的是，我们从较低的节点(也称为内部子节点)开始，将带有过滤器的事件表上的序列扫描结果加载到内存哈希表中。

然后，对于上层节点(也称为外部子节点)的每一行，我们使用连接条件在哈希表中查找匹配的行。

SQL 显然不仅仅由 SELECT、WHERE 和 JOIN 语句组成，但是我们在这里学到的原则也可以应用到您将使用的其他类型的语句中。

# 摘要

在本文中，我们看到了如何使用`EXPLAIN`语句来获取和理解查询计划，它告诉我们数据库计划如何执行该查询，以及它认为每一步的开销有多大。

从查询计划中，我们已经能够获得一些有见地的信息，例如我们的数据库认为哪个节点的工作最多，以及它计划在哪里使用索引。

然而，知道我们的查询的实际结果，并且能够将查询规划器的成本估计与实际数字进行比较也是非常有用的。这就是`EXPLAIN ANALYZE`语句允许我们做的事情，你可以在本文的第 2 部分[这里](https://medium.com/@schallwigmax/understanding-explain-analyze-query-plans-part-2-of-2-55a5d7d3404e)读到。

```
**Want to Connect?**This article is also available in video format on YouTube [here](https://youtu.be/p13je5_4QQE).
```
# 关于 PostgreSQL 你可能不知道的 3 件事

> 原文：<https://betterprogramming.pub/3-things-you-might-not-know-about-postgresql-1805cc85bf42>

## 增进您对流行数据库的了解

![](img/349be7d84371f61f69b4cdb34411e566.png)

照片由 [Katerina Holmes](https://www.pexels.com/@katerina-holmes?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 从 [Pexels](https://www.pexels.com/photo/black-boy-watching-video-on-laptop-5905700/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

PostgreSQL 是遵循 SQL 规范的流行关系数据库。自 1996 年首次发布以来，它一直吸引着许多数字企业和开发者。作为一个开源项目，每个人都可以浏览代码并为之做出贡献。它表现出良好的性能，其基准测试能够与其他数据库竞争，如 MySQL、SQL Server 和 Oracle 数据库。

SQL 查询在所有数据库管理系统中都是标准化的。他们可能有一些细微的差别，但这并不重要。工程师们更感兴趣的是，Postgres 不仅仅是存储数据。这可以给团队带来一些关于他们工作的信息的见解。

# 获取任何东西的大小

当您的系统在大规模上运行时，您会很想知道您收集了多大规模的数据。我们希望了解数据库、特定表甚至某一列的大小。此类信息对于了解空间是否得到有效利用以及预测任何结垢事件非常有价值。

Postgres 提供了一组与大小相关的命令。例如，要获得整个数据库的大小，我们可以使用命令`pg_database_size()`。它将以字节为单位打印出特定数据库的大小。命令`pg_table_size()`显示当前数据库中某个表的字节大小，不包括索引。命令`pg_total_relation_size()`几乎做了同样的事情，但是包括索引所需的空间。如果我们想知道混凝土柱需要多少空间，我们可以使用命令`pg_column_size()`。

要运行这些命令，我们需要通过在终端运行`psql`进入 Postgres 控制台。我们也可以使用第三方工具，如 Jetbrains 的 DataGrip 或类似工具。一旦我们进入控制台，我们就可以运行 SQL 查询和这些命令。

```
SELECT pg_database_size('my_database');
```

上面的查询打印出以下输出。

```
 pg_database_size
------------------
          8258415
```

当你不愿意把字节转换成 KB 或 MB 时，有一个专用的命令。用命令`pg_size_pretty()`包装尺寸可以进行可读的格式化。

```
SELECT pg_size_pretty(pg_database_size('my_database')); pg_size_pretty
----------------
 8065 kB
```

这些命令的完整列表可以在足够有表现力的官方文档中找到。

# 获取统计数据

Postgres 有一个收集不同统计数据的内置系统。这些统计数据可以包括对某些表的访问次数、最近的查询、特定数据库中由查询修改的数据的数量。这些统计数据可能不是每天都能看到的。然而，在分析数据库的性能和发现见解时，这是第一个要考虑的地方。

随着统计数据的收集，我们需要做的一切只是查询它。我们可以编写一个常规的 SQL 查询来获取特定的信息。例如，让我们获取数据库中每个表插入、更新和删除的行数。该信息出现在视图`pg_stat_user_tables`中。

```
SELECT relname, n_tup_ins, n_tup_upd, n_tup_del FROM pg_stat_user_tables;
```

上面的查询返回当前数据库中每个表的插入、更新和删除行的列表。在我的例子中，输出如下所示。

```
 relname     | n_tup_ins | n_tup_upd | n_tup_del
-------------+-----------+-----------+-----------
 users       |        51 |         0 |         0
 payments    |       128 |         6 |         0
 profiles    |       139 |         0 |         0
 guides      |       801 |         0 |         8
```

就我个人而言，当常规 SQL 对我不起作用时，我不得不使用它一次。我面临的挑战是计算表中用户的数量。这个故事是巨大的，超过了几个亿。因此，语句`COUNT`永久超时。谢天谢地，使用统计视图成为可能。以下查询从我的数据库中返回 Users 表的估计行数。

```
SELECT n_live_tup FROM pg_stat_user_tables WHERE relname = 'users';
```

`n_live_tup`表示数据库中每个表的估计行数，`relname`显示每个表的名称。视图`pg_stat_user_tables`包含关于用户表的数据，不包括任何系统表。

您可以在以下视图中找到最有趣的统计数据:

*   `pg_stat_database` -数据库范围统计；
*   `pg_stat_all_tables` -统计当前数据库中的所有表格；
*   `pg_stat_activity` -与查询和流程相关的统计；

所有视图及其栏目的完整列表可在[官方文档](https://www.postgresql.org/docs/9.3/monitoring-stats.html)中找到。请查看它，了解您的数据库的性能。

# 了解查询如何执行

你有没有问过自己运行一个查询需要什么？有多少行将受到影响？运行该查询的成本是多少？SQL 有一个非常强大的命令`EXPLAIN`，Postgres 和其他数据库都支持这个命令。它是如何工作的？

当我们运行语句`EXPLAIN`时，它检查下面的查询。它将检查运行它的所有操作，如顺序扫描、索引和位图索引扫描。当查询更复杂(包括连接)时，将会执行更多的扫描。在这种情况下，命令`EXPLAIN`记录所有这些信息，并为显示做准备。

```
 EXPLAIN SELECT * FROM users;
                          QUERY PLAN
--------------------------------------------------------------
 Seq Scan on users  (cost=0.00..10.50 rows=50 width=1388)
```

这里我们分析了简单的`SELECT`查询，从 Users 表中检索所有记录。我们看到的结果是*查询计划*。它只包含一个条目，是对 Users 表的顺序扫描。除此之外，我们还能够看到运行查询的估计成本、返回的行数以及行的平均宽度。

在复杂查询中进行故障排除和识别瓶颈时，这个命令非常有用。可以得到详细的查询计划，轻松发现有问题的语句。由于它在许多其他数据库中可用，它成为每个软件工程师的一个非常有用的工具。

PostgreSQL 有一些隐藏的宝石。了解它们可以给工程团队带来好处。他们将能够实现其解决方案的最佳性能，并让客户满意。
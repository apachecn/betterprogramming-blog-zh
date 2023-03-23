# 理解 PostgreSQL:这三种查询实践正在损害您的后端

> 原文：<https://betterprogramming.pub/understanding-postgresql-how-bad-query-practices-are-hurting-your-backend-1d3a966af4cb>

## 每个查询都使用 SELECT *吗？

![](img/28683818312076b78c989b6c79e3c420.png)

由[汉弗莱·穆莱巴](https://unsplash.com/@good_citizen?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

今天我们将讨论 PostgreSQL。理解你的后端框架是非常重要的，无论是 Django 还是 Spring。

但是同样重要的是理解你的数据库以及它是如何执行各种操作的。原因是，您的框架为了让用户更容易地使用它，并使其适用于所有数据库系统，可能会在性能方面做出妥协。

因为我从事 PostgreSQL 已经有一段时间了，所以我熟悉它的各种设计模式，以及它们如何影响后端应用程序的性能。在本期中，我将重点介绍几个例子。

> ***你知道吗？***
> 
> *当您在 PostgreSQL 中更新一行时，它不会就地更新，而是用更新后的值创建一个新行，并将旧行标记为“死元组”。删除操作也是如此。它不会立即删除该行，而只是将其标记为死行。这样做的目的是，如果有依赖于旧值的正在进行的事务，仍然可以访问它。*

# 没有有效地使用真空、分析和重新索引

上面给出的信息很重要。因为这意味着您所做的任何类型的更新都会导致数据库中的一堆死元组，这些元组会占用不必要的空间。

命令有助于清除这种情况。您可以对一个表或整个数据库运行`VACUUM`。你可以在官方文档[这里](https://www.postgresql.org/docs/14/sql-vacuum.html)读到更多。这个操作是相当昂贵的，所以确保你不要在你的网络流量很高的时候这样做。

PostgreSQL 还存储关于数据库的统计数据。比如每个表中有多少行，这些行是每个表的索引等等。当您在数据库中执行查询时，Postgres 会提出一个有效的计划，以便它能够以最佳方式执行查询。

Postgres 存储的这些统计数据有助于它提出一个最佳查询。例如，如果它知道某个特定的列启用了索引，那么它将进行索引扫描，而不是堆扫描(Postgres 术语，用于遍历整个数据库)。由于这些统计数据在您更新数据库时会不断变化，因此经常更新它们非常重要。这就是`ANALYZE`命令发挥作用的地方。你可以在这里阅读更多关于它的[。](https://www.postgresql.org/docs/current/sql-analyze.html)

对于 Postgres,`AUTOVACUUM`通常默认启用。`Autovacuum`自动执行`VACUUM`和`ANALYZE`命令。如果您想检查您的数据库是否启用了`AUTOVACUUM`，您可以运行以下查询:

```
SELECT name, setting FROM pg_settings WHERE name ILIKE '%autovacuum%';
```

可能会出现索引损坏或变得臃肿的情况(包含大量空白页，这可能会使您的查询不那么优化)。在这种情况下，您可能想要使用`REINDEX`命令来重建您的索引。你可以在这里阅读更多相关信息[。](https://www.postgresql.org/docs/current/sql-reindex.html)

# 对每个查询使用 SELECT *

在使用框架时，我看到开发人员犯的最大错误是使用从数据库获取整行数据的查询，即使他们不需要很多列值。

让我们以 Django 为例，下面是您用来从一个名为`Customers`的假设表中获取所有列的查询:

```
Customer.objects.filter(is_order_placed=True)
```

将此与仅从同一个表中检索 3 列的查询进行比较:

```
Customer.objects.filter(is_order_placed=True).values('name', 'email_id', 'num_orders')
```

与之前的相比，你看到它变得更长更笨重了吗？难怪我们变得懒惰。此外，还有一种令人苦恼的感觉，如果您将来需要更多的专栏，该怎么办？

但是这样做成本很高。当您像这样查询时，数据库必须做更多的工作来获取所有的列。它必须将数据从存储的二进制形式转换成可以通过网络传输的形式。随之而来的还有相关的带宽成本。当您的查询返回数千行时，这一点变得很明显。

由于 Postgres 的严格政策，不允许任何行跨越多个页面，否则这种影响将是巨大的。但是如果你的列包含大的文本字段(例如产品描述)，Postgres 将使用一种叫做`TOAST`的技术来存储它，这基本上是一个不同的表。因此，当您查询所有内容时，现在必须在不同的表中查找！

在这一期中，我不会讨论列存储与行存储的数据存储。但是在这里留下一个提示。如果您的表遵循列存储方法(整个列是顺序存储的)，检索所有列将非常昂贵。

# 偏移的广泛使用

每个人都是分页的粉丝。实现分页意味着我们可以一次发送 1000 个条目，而不是 10 个一组地发送，并且使用延迟加载可以改善用户体验。

但是当您进行分页时，您正在创建包含`OFFSET`命令的查询，这些命令告诉数据库在偏移特定数量的值后返回这些值。例如:

```
SELECT * FROM customers WHERE is_order_placed=true OFFSET 1000 LIMIT 10
```

那么 Postgres 用上面的查询在后台做什么呢？它只取 10 行吗？不，事实上，它必须寻找前(1000 + 10)行，然后丢弃前 1000 行，返回剩下的 10 行。它似乎不再是最优的了，不是吗？

在这种情况下，你需要变得聪明。您的框架将让您轻松地编写这种逻辑，但是正如您所看到的，它非常昂贵。但是您必须以这样一种方式转换它，即您只使用`id`字段进行查询，而不使用 offset。例如，如果您可以计算出您在上一页中提供的 ID，您可以将上面的查询改为:

```
SELECT * FROM customers WHERE is_order_placed=true and id > 1000 LIMIT 10
```

这可以通过向前端传递每个结果集的分页指针来实现。当前端需要下一页的时候，可以传递这个光标，利用这个光标可以计算出下一页的 ID。

# 包扎

你的数据库系统所做的一切也是基于人类设计的算法。虽然它们可能是最好的整体逻辑，但了解您正在使用的系统可以帮助您为您的应用提出最佳算法。

请记住，这些是针对高级用例的，虽然您的应用程序仍处于初级阶段，但您不必为此担心太多。因为这个话题很大，我会在接下来的几期中写更多。敬请期待！

```
**Want to Connect?**You can follow me on [Twitter](https://twitter.com/itsdennian), where I share tweets about Software Architecture, Startup Ecosystem, and books.
```
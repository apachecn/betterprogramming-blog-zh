# PostgreSQL 和外部数据包装器

> 原文：<https://betterprogramming.pub/postgresql-and-the-foreign-data-wrapper-2f5c5aaf20d4>

## 让您的远程数据库不那么远程

![](img/ab1bfb6da1be7adfda8c8ad55c7f2967.png)

资料来源:postgresql.org

较大的数据库部署可能需要跨不同资源或主机运行多个`Postgres`实例。随着时间的推移，管理对多个实例的分布式访问、跟踪权限、管理 IP 等会变得复杂而繁琐。

这让您希望有某种方式来集中您的所有数据…

嗯，有！

不，它不是一些第三方插件或单独的聚合数据库。它是*外国数据包装器*。

这是对`Postgres`的一个非常方便的扩展，允许您将远程服务器添加到现有实例中，并像查询本地表一样查询它们。*牛逼！*

在几个简单的命令中，您可以设置到远程服务器之一的映射。让我们来探索一下这是什么样子的。

# 添加扩展名

只要您使用的是最近的`Postgres`版本，您应该能够用这个简单的命令创建扩展:

```
CREATE EXTENSION IF NOT EXISTS postgres_fdw;
```

## 添加服务器

现在，我们将使用刚刚添加的外部数据包装器扩展来设置远程服务器。请随意给它起一个独特的名字:

```
CREATE SERVER <remote_server_name>
FOREIGN DATA WRAPPER postgres_fdw
OPTIONS (dbname '<database_name_on_server>', ...)
```

这些只是部分选择。有关远程服务器连接选项的完整列表，请访问:[https://www.postgresql.org/docs/9.5/postgres-fdw.html](https://www.postgresql.org/docs/9.5/postgres-fdw.html)。

# 映射用户权限

为了能够查询远程数据库，它必须知道以什么用户的身份执行操作。为此，您必须为刚刚添加的服务器添加一个用户映射。这实际上是将服务器上的本地用户映射到远程服务器上的用户。

```
CREATE USER MAPPING FOR <local_username>
SERVER <remote_server_name>
OPTIONS (user '<remote_username>', password '<remote_pass>', ...);
```

同样，这些并不是所有可用的选项。有关更多信息，请参考上面服务器部分的链接。

# 导入表或模式

现在您已经添加了服务器和用户信息，您可以开始导入数据了。此时你有几个选择。两种主要方法是导入单个表(一个接一个)或导入整个远程模式。

让我们看看第一个选项是如何工作的:

```
CREATE FOREIGN TABLE <table_name> (id bigint, field1 text)
SERVER <remote_server_name>
OPTIONS (schema_name 'public', table_name 'my_table');
```

使用这种方法，您必须指定远程表的列和数据类型细节。在下一个方法中，我们将创建一个全新的模式，并将远程服务器的模式导入其中:

```
CREATE SCHEMA <new_local_schema_name>
IMPORT FOREIGN SCHEMA <remote_server_schema_name>
LIMIT TO (table1, table2, table3, ...)
FROM SERVER <remote_server_name>
INTO <new_local_schema_name>
```

# 查询时间！

现在一切都设置好了，您应该能够向远程服务器查询数据了。假设权限映射正确，上下文相当简单:

```
SELECT * FROM <my_local_schema>.<remote_table> LIMIT 1;
```

如果一切正常，您应该会看到从远程服务器的表中返回一行。

当你在整个过程中给每个元素命名时，如果你能想出一个容易推断的逻辑方案，那将会很有帮助。例如，如果您有一个从`Postgres`实例到主机的一对一映射，那么您可以使用该主机的主机名作为远程服务器和远程模式。这将有助于您编写查询，因为您将能够快速判断什么映射到什么。

外来数据包装器是一种快速有效的方法，可以快速设置对分布式`Postgres`实例的访问。能够从一个集中点进行查询减少了权限和访问管理的管理开销。

*感谢查看我关于* `*Postgres*` *的最新文章！如果你喜欢这篇文章，可以看看我的其他以开发者为中心的文章。*
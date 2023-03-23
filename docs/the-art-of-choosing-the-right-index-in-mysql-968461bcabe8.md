# MySQL 中选择正确索引的艺术

> 原文：<https://betterprogramming.pub/the-art-of-choosing-the-right-index-in-mysql-968461bcabe8>

## 如何索引 MySQL 以获得高性能

![](img/5f997b8e5f93b9097f06bab2f2faaad7.png)

照片由 [Ahmad Odeh](https://unsplash.com/@aoddeh?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄。

如果您曾经使用过 MySQL、Percona Server 或 MariaDB，那么您可能想知道如何提高数据库实例的性能。如果你在这个问题上寻求建议，你可能听说过索引。

MySQL 中的索引可以分为几种类型:

1.  平衡树(B 树)索引是最常用的索引类型。这种索引类型可以与使用`=`、`>`、`>=`、`<`、`<=`和`BETWEEN`关键字的搜索查询一起使用(也可以与`LIKE`查询一起使用)。
2.  空间(R-Tree)索引可以和 MySQL 几何数据类型一起使用来索引地理对象。
3.  散列索引通常只用于使用`=`或`<=>`搜索操作符的查询。它们速度很快，但只能在使用内存存储引擎时使用。
4.  覆盖索引覆盖了完成查询所需的所有列。
5.  聚集索引存储行数据(通常是`PRIMARY KEYs`，如果不存在，则是`UNIQUE`索引)。
6.  多列(复合)索引是在多个列上创建的。
7.  前缀索引只允许您索引列的前缀。因此，索引不会索引列的全部值。它们经常被用来节省空间。

# b 树索引和 MySQL

B 树索引是 MySQL、MariaDB 和 Percona 服务器中最常用的索引。当使用这种索引时，MySQL 可以利用使用`=`、`>`、`>=`、`<`、`<=`或`BETWEEN`关键字或`LIKE`查询的搜索查询。可以将这种索引添加到列中的主要原因之一是为了加快搜索查询。使用 b 树索引时，存储引擎不会扫描整个表来查找相关行。

下面是一些可能使用这种索引的查询示例:

```
SELECT * FROM demo_table WHERE column [ = | > | >= | < | <= ] 'value';SELECT * FROM demo_table WHERE column_a BETWEEN 100 AND 200;SELECT * FROM demo_table WHERE column LIKE 'value%';
```

# 空间索引和 MySQL

空间索引(有时也称为 R 树索引)用于访问空间(地理)对象。要使用这样的索引，您必须使用 MySQL 提供的 GIS 功能。这些功能包括`MBRContains`、`MBRCovers`或`MBREquals`。这些函数可以指示一个参数的最小外接矩形是否包含、覆盖或等于另一个参数的最小外接矩形。

要将这样的索引添加到表中，可以像这样使用`SPATIAL`关键字:

```
CREATE SPATIAL INDEX spatial_idx ON demo_table (demo_column);
```

或者:

```
ALTER TABLE demo_table ADD SPATIAL INDEX(spatial_idx);
```

下面是一些可能使用这种索引的查询示例:

```
SELECT MBRContains(@variable_1, @variable_2);SELECT MBRWithin(@variable_2, @variable_1);
```

请记住，在使用像`variable_1`或`variable_2`这样的变量之前，您应该首先定义它们(参数`WKT value`代表一个众所周知的文本格式值，它代表几何对象):

```
SET @variable_1 = ST_GeomFromText('WKT value');
```

# 哈希索引和 MySQL

MySQL 中的散列索引有一些明显的优点和缺点。哈希索引的主要优点是非常快，但缺点是它们只用于相等比较，并且只在内存存储引擎上工作。它们也不支持范围搜索。要在表上创建这样的索引，请在查询末尾使用`USING HASH`选项:

```
CREATE INDEX idx_name ON demo_table (demo_column) USING HASH;
```

哈希索引如此之快的原因是哈希索引通常非常紧凑，并且它们的主键只访问哈希表中的元素。但是，这也意味着您不能使用范围搜索。散列索引应该只与`=`或`<=>`操作符一起使用。

# 覆盖 MySQL 中的索引

使用 MySQL 时，有时您可能会遇到一些需要以不同方式使用索引的情况。这就是覆盖指数的用武之地。

简单地说，覆盖索引是覆盖成功执行查询所需的所有字段的索引。因此，当使用覆盖索引时，查询可以从索引本身检索结果，而不是访问磁盘来节省磁盘 I/O。例如，假设您有这样一个查询:

```
SELECT column_1, column_2 FROM demo_table WHERE column_3 = 'value';
```

如果您使用的索引已经包含您要搜索的列的值，MySQL 将不会访问磁盘。相反，它将直接从索引中提供结果。

# MySQL 中的聚集索引

聚簇索引本身并不是一种独立的 MySQL 索引类型。它们只是存储数据的不同方法。这种索引存储行数据。如果您的表有一个`PRIMARY KEY`，那么`PRIMARY KEY`就是聚集索引。如果您的表没有`PRIMARY KEY`，聚集索引是第一个`UNIQUE INDEX`，它的所有键列都被定义为`NOT NULL`。

当使用这种索引时，表中的所有行都根据索引键值进行存储和排序。由于行只能有一种排序顺序，所以表不能有多个聚集索引。

聚集索引很容易区分。在 MySQL 中，金钥匙通常代表这样的索引。相比之下，平衡树索引由银色轮廓的键表示(在这种情况下，带有“金色键”的列是带有聚集索引的列，而带有“银色键”的列是带有平衡树索引的列):

![](img/a9c45d5e547194b6f7669174899e5f5d.png)

要在创建表时定义聚集索引，请向列添加一个`AUTO_INCREMENT`，并将其定义为`PRIMARY KEY`。要在创建表时定义平衡树索引，请在列中添加一个`INDEX`并指定要索引的列(如果需要，还可以指定索引的名称):

```
CREATE TABLE arctype ( clustered_index INT(255) NOT NULL AUTO_INCREMENT PRIMARY KEY, demo_index VARCHAR(255) NOT NULL, INDEX idx_name(demo_index));
```

所有键列都定义为`NOT NULL`的`UNIQUE INDEX`也可以是聚集索引。顾名思义，在这种情况下，带有`UNIQUE INDEX`的列中的所有值都是唯一的(即没有重复)。

# MySQL 中的复合索引

复合索引是跨越多列的索引。这是不言自明的:如果我们有一个多列索引，我们有一个复合索引。向表中添加这样的索引可能是这样的(在本例中，`c1`、`c2`和`c3`是列名):

```
CREATE INDEX composite_idx ON demo_table(c1,c2,c3);
```

如果我们处理可能使用索引字段进行选择、连接或过滤操作的查询，复合索引会很有用。例如，如果您希望满足如下查询，那么列`c1`和`c2`上的复合索引可能会很有用:

```
SELECT * FROM demo_table WHERE c1 = 5 AND c2 = 10;
```

在 MySQL 中，复合索引可以由多达 16 列组成，但是要记住 MySQL 从左到右使用索引，反之亦然。

# MySQL 中的前缀索引

有时，您可能会遇到需要索引很长的列的情况，这使得您的索引非常大。在这些情况下，您也可以索引列的前几个字符，而不是索引整个值。这种索引称为前缀索引。

要在 MySQL 的表上创建前缀索引，可以使用如下查询:

```
CREATE INDEX prefix_idx ON demo_table(column_name(length));
```

在本例中，`prefix_idx`是索引的名称，`demo_table`是表的名称，`column_name`是列的名称，`length`是索引的长度。

使用前缀索引时，重要的是要记住，您应该以一种提供适当的选择性并同时节省空间的方式来索引列。所以让我们试着去做。

首先要找到列的选择性。我们将列中的不同(唯一)值除以表中的所有值。理想的指数选择性是`1`的值:

```
SELECT COUNT(DISTINCT column) / COUNT(*) FROM demo_table;
```

现在，我们应该尝试使前缀索引的选择性尽可能接近该值。为此，我们可以发出如下查询:

```
SELECT COUNT(DISTINCT LEFT(column, 5)) / COUNT(*) AS selectivity_5;
```

将显示的值是前缀为五个字符的索引的选择性(要评估不同的字符长度，请调整查询)。

# 摘要

在本文中，我们浏览了一些与 MySQL 中某些索引相关的用例。为您的用例选择一个好的索引可能确实是一件麻烦的事情。毕竟，你有这么多选择。但是一旦您理解了项目的需求以及在 MySQL 中使用某些索引选项的优缺点，选择合适的选项就变得更容易了。

希望这篇文章为您提供了一些关于 MySQL 中索引的有价值的见解。请继续关注 MySQL 世界的更多内容。
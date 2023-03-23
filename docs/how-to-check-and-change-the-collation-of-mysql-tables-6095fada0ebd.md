# 如何检查和更改 MySQL 表的排序规则

> 原文：<https://betterprogramming.pub/how-to-check-and-change-the-collation-of-mysql-tables-6095fada0ebd>

## 了解在数据库中配置排序规则的重要性

![](img/e30e84974c5105e9b34b3b686c0b31c0.png)

由[斯文·布兰德斯马](https://unsplash.com/@seffen99)在 [Unsplash](https://unsplash.com/photos/6Uf-1Ozbvho) 上拍摄。

根据我们希望存储在数据库中的数据，通常的做法是相应地调整表的排序规则，以避免任何不必要的行为。

在本文中，我们将介绍数据库中的排序规则，并讨论相应地配置表的排序规则的重要性。此外，我们将探索一些方法来检查 MySQL(或 MariaDB)中的表的排序。最后，我们将展示如何更改表的排序规则。

# 什么是数据库中的排序规则，为什么它很重要？

归类是一组明确定义如何对字符串进行比较和排序的规则。例如，这些规则可以指示字符是否应该区分大小写，或者字母中的重音是否重要(例如，是否应该将“a”视为独立于“o”的字符)。

根据希望存储在表中的数据来配置排序规则是很重要的。有各种非英语语言有相当奇怪的规则(例如，两个字符被认为是一个字母，等等。)，所以如果必须处理这样的数据，就必须相应地调整表的排序规则。如果您忽略此步骤，则某些查询可能会返回意外和/或不正确的结果。

# 如何检查 MySQL/MariaDB 表的排序规则

`INFORMATION_SCHEMA`是一个数据库，提供对数据库中各表的元数据的访问。可以从`INFORMATION_SCHEMA.TABLES`表中获取表的排序，如下所示(假设我们的表叫做`t_name`):

```
SELECT TABLE_SCHEMA
    , TABLE_NAME
    , TABLE_COLLATION 
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_NAME = 't_name';
```

输出将类似于下面显示的内容:

```
+--------------+------------+-----------------+
| TABLE_SCHEMA | TABLE_NAME | TABLE_COLLATION |
+--------------+------------+-----------------+
| mysql        | user       | utf8_bin        |
+--------------+------------+-----------------+1 row in set (0.00 sec)
```

但是请注意，排序规则可能应用于特定的列，这意味着一个表可以有不同排序规则的列。如果您想要检查特定列的排序规则，可以从`INFORMATION_SCHEMA.COLUMNS`中获取这段元数据。以下查询应该会给您提供所需的信息:

```
SELECT TABLE_NAME 
    , COLUMN_NAME 
    , COLLATION_NAME 
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 't_name';
```

现在，输出将分别显示每个列的排序规则:

```
+------------+--------------------------+------------------+
| TABLE_NAME | COLUMN_NAME              | COLLATION_NAME   |
+------------+--------------------------+------------------+
| user       | Host                     | ascii_general_ci |
| user       | User                     | utf8_bin         |
| user       | Select_priv              | utf8_general_ci  |
| user       | Insert_priv              | utf8_general_ci  |
| user       | Update_priv              | utf8_general_ci  |
| user       | Delete_priv              | utf8_general_ci  |
| user       | Create_priv              | utf8_general_ci  |
| user       | Drop_priv                | utf8_general_ci  |
| user       | Reload_priv              | utf8_general_ci  |
| user       | Shutdown_priv            | utf8_general_ci  |
| user       | Process_priv             | utf8_general_ci  |
| user       | File_priv                | utf8_general_ci  |
| user       | Grant_priv               | utf8_general_ci  |
......
+------------+--------------------------+------------------+
```

# 如何更改数据库、表或列的排序规则

现在，如果您想更改整个数据库的排序规则，可以运行下面的查询:

```
ALTER DATABASE mydb CHARACTER SET utf8mb4 COLLATE utf8mb4;
```

同样，要更改特定表的排序规则:

```
ALTER TABLE t_name CONVERT TO CHARACTER SET utf8;
```

对于单个列:

```
ALTER TABLE t_name MODIFY c_name VARCHAR(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL;
```

# 结论

在本文中，我们讨论了 MySQL 表的排序及其重要性。此外，我们探索了检查表的排序规则的各种方法，并展示了如何更改它。相应地配置表的排序规则是非常重要的，这样才能以正确的方式处理数据，而不会导致任何不必要的行为。
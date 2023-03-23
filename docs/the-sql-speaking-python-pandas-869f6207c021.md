# 一看 SQL 说话的 Python…熊猫！

> 原文：<https://betterprogramming.pub/the-sql-speaking-python-pandas-869f6207c021>

## 从 SQL 到熊猫——了解它们的对等物

![](img/764796e0789cf39c25e39f917cedba42.png)

照片由[翻滚 926](https://unsplash.com/@billow926?utm_source=medium&utm_medium=referral) 上的[退溅](https://unsplash.com?utm_source=medium&utm_medium=referral)

Pandas 是一个广泛用于数据操作和分析的库。说到数据，存储数据最常见的方式是在数据库中，我们将使用 *SQL* (结构化查询语言)来查询它。

我们可以使用 pandas 在数据库中插入/检索和修改数据。Pandas 也可以用于原始 SQL 查询。下面让我们看看 SQL 和它的熊猫等价物。

开始之前，请确保 Pandas 已安装在您的机器上。

```
pip install pandas
```

贴吧里面，熊猫可以导入到你的代码里如下:

```
import pandas as pd
```

在继续之前，我们将从表`table1` 中获取数据，并将其存储在名为`table`的数据帧中。

```
-- SQL
SELECT * FROM table1;--Pandas
table = pd.read_sql('SELECT * FROM TABLE1', con=*engine*/connection_string)The *engine* is an [SQLAlchemy](https://docs.sqlalchemy.org/en/14/core/engines.html) engine.
```

**注意:**数据帧是一种二维数据结构，具有行和列形式的数据。可以认为它类似于 excel 电子表格。想了解更多关于 DataFrame 的信息，请参考上面的熊猫官方文档。

# **1。选择**

*   从表中选择所有列:

```
--SQL 
SELECT * FROM table1;--Pandas
table
```

*   从表格中选择`coulmn1`、`column2`:

```
--SQL 
SELECT column1, column2 FROM table1;--Pandas
table[[column1,column2]]
```

*   从列中选择所有唯一记录

```
--SQL 
SELECT DISTINCT(column1) FROM table1;--Pandas
table[column1].unique()
```

*   从列中选择所有唯一记录的计数

```
--SQL
SELECT COUNT(DISTINCT(column1)) FROM table1;--Pandas
table[column1].nunique()
```

# 2.**用 Where 子句选择**

*   选择列 1 **等于**和`value`的记录:

```
--SQL 
SELECT * FROM table1 WHERE column1 = *value*;--Pandas
table.loc[table[column1]==*value*]
```

*   选择`column1`大于`value1`且`column2`小于`value2`的记录:

```
--SQL 
SELECT * FROM table1 WHERE column1 = *value1* AND column2 = *value2*;--Pandas
table.loc[(table[column1] > *value1*) & (table[column2]< *value2*)]
```

*   选择其中 column1 包含字符串“Hello”的记录:

```
--SQL
SELECT * FROM table1 WHERE column1 LIKE '%Hello%';--Pandas
table.loc[table[column1].str.contains('Hello')]
```

*   选择非空的记录:

```
--SQL
SELECT * FROM table1 WHERE column1 IS NOT NULL;--Pandas
table.loc[table[column1].notnull()]
```

# 3.**用 Group By 子句选择**

*   在`column1`上选择`column1`和`column2`分组的总和:

```
--SQL 
SELECT coulmn1, SUM(column2) FROM table1 GROUP BY column1;--Pandas
table.groupby(column1).sum()
```

*   在`column1`上选择`column2`分组的第 1 列和最小值，其中第 2 列的最小值大于`*value*`:

```
--SQL 
SELECT coulmn1, MIN(column2) as min FROM table1 GROUP BY column1 HAVING MIN(column2)> *value*;--Pandas
table_group = table.groupby(column1).min()
table_group[table_group[column2] > *value*]
```

# 4. **SELECT with Order By 子句(排序)**

*   基于单个列按**升序**对结果进行排序:

```
--SQL
SELECT * FROM table1 ORDER BY column1 ASC;--Pandas
table.sort_values(by=column1,ascending=True,inplace=True)
```

*   基于单个列按降序对结果进行排序:

```
--SQL
SELECT * FROM table1 ORDER BY column1 DESC;--Pandas
table.sort_values(by=column1,ascending=False,inplace=True)
```

*   基于多列对结果进行升序排序:

```
--SQL
SELECT * FROM table1 ORDER BY column1, column2 ASC;--Pandas
table.sort_values(by=[column1,column2],ascending=True,inplace=True)
```

*   按升序对列 1 的结果进行排序，按降序对列 2 的结果进行排序:

```
--SQL
SELECT * FROM table1 ORDER BY column1 ASC, column2 DESC;--Pandas
table.sort_values(by=[column1,column2],ascending=[True,False],inplace=True)
```

# 5.**加入**

## **内部连接:**

```
--SQL 
SELECT * FROM table1 INNER JOIN table2 ON table1.column1 = table2.column1;--Pandas
join_table = table1.merge(table2, on=column1, how='inner')# We can also use the join method as given below.
join_table = table1.join(table2.set_index(column1), on=column1, how=inner)
```

## **多列的左连接:**

```
--SQL 
SELECT * FROM table1 LEFT JOIN table2 ON table1.column1 = table2.column1 AND table1.column2=table2.column2;--Pandas
join_table = table1.merge(table2, how='left', left_on=[column1,column2], right_on=[column1,column2])
```

## **右连接:**

```
--SQL 
SELECT * FROM table1 RIGHT JOIN table2 ON table1.column1 = table2.column1;--Pandas
join_table = table1.merge(table2, on=column1, how='right')
```

# 6.**更新**

*   根据条件更新列:

```
--SQL 
UPDATE table1 SET column2 = 1 WHERE column1 = 'YES';--Pandas
table.loc[table[column1]=='YES', column2] = 1
```

*   基于多个标准更新列:

```
--SQL
UPDATE table1 SET column3 = 1 WHERE column1 = 'YES' AND column2 = 'ACTIVE';--Pandas
table.loc[(table[column1] == 'YES') & (table[column2] == 'ACTIVE'), column3] = 1
```

# 7.**插入**

*   将数据帧中的数据插入到数据库表中。

```
--SQL
INSERT INTO table1 VALUES (column1,column2,column3)--Pandas
table.to_sql(table1, con=*engine*/connection_string)
```

这些只是熊猫的一小部分功能。请参考 [*Pandas 文档*](https://pandas.pydata.org/docs/index.html) 了解完整的功能集。

我还会在接下来的故事中发布更多的熊猫特写。敬请关注。

```
**Want to Connect?**Reach me out on [LinkedIn](https://www.linkedin.com/in/hari-hara-subramanyam-s-b2692636).
```

感谢您的阅读！！
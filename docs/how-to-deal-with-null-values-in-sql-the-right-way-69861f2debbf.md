# 如何正确处理 SQL 中的空值

> 原文：<https://betterprogramming.pub/how-to-deal-with-null-values-in-sql-the-right-way-69861f2debbf>

## 在 T-SQL 中处理空值的提示和技巧

![](img/ec87b5b672470aeb259004c12b802786.png)

杰里米·珀金斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 前言

一个最近刚开始学 SQL 的朋友问我关于空值的问题，以及如何处理。如果您是 SQL 新手，本指南将让您深入了解一个令初学者感到困惑的主题。

# 什么是 NULL？

NULL 在 SQL 中用于表示数据库中不存在某个值。
不要与空字符串或零值混淆。

NULL 表示没有值，空字符串和零都表示实际值。打个比方，就像没有答案不一定代表答案是“没有”，没有值也不代表是零。

误解 NULL 在 SQL 中的工作方式可能会导致意外的错误和输出，所以不再多说，让我们看看如何解决它。

在本练习中，我们将使用`tblSouthPark`表。

```
**SELECT** *
**FROM** tblSouthPark
```

# 1.IS NULL 和 IS NOT NULL 运算符

我们不能使用比较运算符`=,<,>,<>`来测试空值。相反，我们必须使用`IS NULL`和不为空的[谓词](https://docs.microsoft.com/en-us/sql/t-sql/queries/predicates?view=sql-server-ver15)。

*   `IS NULL` **:** 返回包含空值的行

> **语法:**表达式为空

```
**SELECT** ID, 
    Student,
    Email1,
    Email2
**FROM** tblSouthPark
**WHERE** Email1 **IS NULL** ANDEmail2 **IS NULL
ORDER BY** ID
```

上面的查询产生了 Email1 和 Email2 列都为空的所有记录。

*   **IS NOT NULL:** 顾名思义，它是 IS NULL 的逆。

> **语法:**表达式不为空

```
**SELECT** ID, 
    Student,
    Email1,
    Email2
**FROM** tblSouthPark **WHERE** Email1 **IS NOT NULL** ANDEmail2 **IS NOT NULL
ORDER BY** ID
```

上面的查询产生了 Email1 和 Email2 列都不为空的所有记录。

# 2.ISNULL()函数

如果给定的表达式为空，函数`ISNULL`返回指定的值。否则，如果表达式不为空，则返回表达式本身。

**语法:** ISNULL(表达式，值)

让我们通过在我们的`SouthPark`表上运行一个简单的查询来理解这一点。

```
**SELECT** ID, 
    Student,
    **ISNULL**(Father,'Missing') **AS** Father
**FROM** tblSouthPark
**ORDER BY** ID
```

该查询产生以下结果:

由于 Eric Cartman 示例中的表达式计算结果为 NULL，因此`ISNULL`函数返回值`Missing`。对于其他学生，表达式不为空，所以它返回表达式本身。

# 3.COALESCE()函数

`COALESCE`函数返回给定列表中的第一个非空值。与`ISNULL`函数不同，它可以接受多个表达式。

语法: COALESCE(表达式[1…..n])

例如，`**SELECT** **COALESCE**(NULL, NULL, 'red', 'blue', NULL)`返回`red`，因为它是第一个非空值。如果所有的值都是 NULL，那么`COALESCE`函数将返回 NULL。

让我们使用`SouthPark`表上的`COALESCE`功能:

```
**SELECT** ID, 
    Student,
    **COALESCE**(Email1, Email2, 'N/A') **AS** Primary_Email
**FROM** tblSouthPark
**ORDER BY** ID
```

上述查询产生以下结果:

正如所料，由于在 Kenny 的例子中 Email1 和 Email2 都是 null，所以`COALESCE`函数返回`N/A`作为`Primary_Email`。对于 Stan，Email2 作为`Primary_Email`返回，因为它是`COALESCE`函数中的第一个非空值。对于其他人，Email1 作为`Primary_Email`返回。

# 4.格表达式

我们也可以使用传统的`CASE`表达式来替换空值。

以下是`CASE`表达式的一般语法:

```
**CASE**
    **WHEN** expression_1 **THEN** result_1
    **WHEN** expression_2 **THEN** result_2
    .
    .
    .
    **WHEN** expression_n **THEN** result_n
    **ELSE** else_expression
**END**
```

我们可以通过使用`CASE`生成与使用上面的`COALESCE`相同的输出。

该查询将如下所示:

```
**SELECT** ID,
      Student, **CASE**
          **WHEN** Email1 IS NOT NULL **THEN** Email1
          **WHEN** Email2 IS NOT NULL **THEN** Email2
          **ELSE** 'N/A'
      **END AS** Primary_Email
**FROM** tblSouthPark
**ORDER BY** ID
```

该查询产生相同的输出:

*注意:* `*CASE*` *表达式在语法上类似于* `*COALESCE*` *函数。其实* `*COALESCE*` *就像是* `*CASE*` *的简称。前者简短明了，但后者更清晰易懂。*

# 5.NULLIF()函数

`NULLIF`函数接受两个表达式，如果表达式相等，则返回 NULL，否则返回第一个表达式。

**语法:** NULLIF(表达式 1，表达式 2)

```
**NULLIF**('Red','Orange') -- Returns Red
**NULLIF**(0,NULL) -- Returns 0
**NULLIF**(0,0) -- Returns NULL
```

如果数据在一列中包含 null 和空字符串，那么`NULLIF`就派上了用场。我们用一个例子来理解这个。

我们看到表中的`Phone`列包含 NULL 和空字符串。

我们可以通过使用`NULLIF`将空字符串改为 NULL 来对此进行标准化:

```
**SELECT** ID, 
    Student,
    **NULLIF**(Phone,'') **AS** Phone
**FROM** tblSouthPark
**ORDER BY** ID
```

上面的查询产生:

`NULLIF`的另一个好的用例是防止“被零除”的错误:

```
var1 = 1
var2 = 0var1/var2 --This will generate a "division by zero" errorvar1/**NULLIF**(var2,0)--This doesn't trigger a "division by zero" error
```

在第二种情况下，我们没有得到“被零除”的错误，因为在分母中返回 NULL 来代替 0。

# 额外提示

*   `COALESCE`功能是标准 ANSI，而`ISNULL`和`NULLIF`不是。这使得`COALESCE`比`ISNULL`和`NULLIF`更加通用和可移植(跨不同的 SQL 风格和 RDBMS)。
*   NULL 是排序顺序中的最小值。如果我们按包含空值的列排序，那么缺省情况下，包含空值的行将在顶部排序。使用`DESC`排序顺序反向排序。

```
**Tip:** To sort in alphabetical order (ASC) with NULL values at the end, you can use a CASE expression in the ORDER BY clause:**ORDER BY
CASE** **WHEN** column_name **IS NULL** **THEN** 1 **ELSE** 0 **END,** column_name;
```

*   SQL 中的聚合函数(`SUM`、`COUNT`、`AVG`、`MAX`、`MIN`)不处理空值，并在执行任何计算之前消除它们。唯一的例外是`COUNT(*)`函数——它返回所有行的计数，包括所有字段都为空的那些行。
*   在`GROUP BY`子句的情况下，如果一列包含具有空值的行，那么这些行将被分组到一个组中。
*   如果一个列同时包含 NULL 和空字符串，并且您需要创建一个合并列，那么您可以将`COALESCE`和`NULLIF`函数链接起来。例如:

```
INPUT TABLE: Sample
+==============+==============+
|     Work     |     Cell     |
+==============+==============+
|              | 717-735-6382 |
+--------------+--------------+
| 546-373-9363 | 493-353-3638 |
+--------------+--------------+
| NULL         | 657-428-3639 |
+--------------+--------------+QUERY
**SELECT** **COALESCE**(**NULLIF**(Work,''),Cell) **AS** Primary **FROM** SampleOUTPUT
+==============+
|   Primary    |
+==============+
| 717-735-6382 |
+--------------+
| 546-373-9363 |
+--------------+
| 657-428-3639 | 
+--------------+We can also use a quick-and-dirty **CASE** expression to do the same:**CASE** **WHEN** Work <> '' **THEN** Work **ELSE** Cell **END**
```

# 包扎

现在，您应该已经拥有了成功处理 SQL 中的空值所需的一切。当然，我们并没有在这里涵盖所有的边缘情况，但是对于初学者来说，这应该是一个很好的起点。如果你卡住了，[栈溢出](https://stackoverflow.com/questions/tagged/sql)就是你的朋友。一如既往，实践是关键！
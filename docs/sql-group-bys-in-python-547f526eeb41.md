# Python 中的 SQL 分组依据

> 原文：<https://betterprogramming.pub/sql-group-bys-in-python-547f526eeb41>

## 使用神奇宝贝数据练习 GROUP BY 子句

![](img/c27eef0a10c13714d9d5587e4ec214a1.png)

[Jay](https://unsplash.com/@filmape?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/pokemon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我在这个博客中使用的代码可以在我的 GitHub 上找到。

由于我们已经知道如何用 SQL 进行基本查询(如果您没有或需要复习，请阅读:"[Python 中 SQL 基础知识的简明介绍](https://medium.com/better-programming/a-gentle-introduction-to-sql-basics-in-python-b137651ed1ff)")，我们可以开始利用 SQL 提供的越来越多的工具。

在 SQL 中，`GROUP BY`子句是一个非常有用的工具。有了它，我们可以更深入地研究我们的数据，并使用一些函数将相同的数据分组。

如果一个列在不同的行中有相同的值，它会将这些行放在一个单独的桶中。

使用`GROUP BY`子句有三个要点:

1.  `GROUP BY`与`SELECT`语句一起使用。
2.  在查询中，`GROUP BY`位于`WHERE`子句之后。
3.  在查询中，`GROUP BY`放在`ORDER BY`之前(如果使用的话)。

现在我们已经制定了一些规则，让我们来设置笔记本吧！

# 安装

对于这些例子，我们将使用来自 [Kaggle 数据集](https://www.kaggle.com/abcsds/pokemon)的神奇宝贝数据。

虽然这是为了给 SQL 增添趣味，但这些例子可以很容易地应用于更商业化的解决方案，比如按年龄组、收入水平、位置等分组。

让我们从导入我们需要的库并加载 Python 中的 CSV 文件开始:

接下来，我们开始清理数据，然后将它移入一个 [SQLite](https://www.sqlite.org/) 实例:

```
**#find NaN values**
nan_rows = df[df.isnull().T.any().T]
nan_rows.head()
```

这将找到任何空值并返回其中的一些(如果有的话)。

因为所有的空值都存在于 type2 列中，所以我们将所有这些空值都改为`‘none’`。

```
**#change all Type 2 NaN values to 'None':**
df['type2'] = df['type2'].fillna('none')
```

现在，由于 SQL 对表中的字符串非常明确(行中的大小写很重要)，我们将把所有内容都设置为小写。

```
**#change all strings within the dataframe to lower case**
df = df.astype(str).apply(lambda x: x.str.lower())
```

现在我们准备将它设置为一个 SQL 数据库！

```
**#set the database for pokemon**
df.to_sql('pokemon', con=cnx, if_exists='append', index=False)**#function for the SQL queries below**
def sql_query(query):
    return pd.read_sql(query, cnx)
```

太棒了，我们可以开始运行一些 SQL 查询了！

# GROUP BY 的一般语法

GROUP BY 函数的一般语法是:

```
SELECT column_name(s), function_name(column_name)
FROM table_name
WHERE condition
GROUP BY column_name(s)
ORDER BY column_name(s);

**function_name**: SUM(), AVG(), MIN(), MAX(), COUNT().
**table_name**: name of the table. In this example, there is only the pokemon table
**condition**: condition used.
```

有了这个，我们可以重新组织和操作数据，以便我们可以找到更好的见解。

# 简单分组依据

比方说，我们只想找到具有最高(最大)统计值的传奇神奇宝贝的名称、类型和总统计值。我们将从一个简单的`MAX()`查询开始:

这将输出 Mega Mewtwo X，一个精神/战斗神奇宝贝，总属性令人印象深刻的 780！

如果我们想知道最大值最高的神奇宝贝，但在每个类型 1 类别中呢？这就是`GROUP BY`子句派上用场的地方:

现在，不是只有一个神奇宝贝作为输出(Mega Mewtwo X)，而是有 14 个传说中的神奇宝贝。

SQL 查询查找所有传奇神奇宝贝，并根据 type1 列将它们分组到单独的存储桶中。

它把它们分成黑暗、龙、电、仙等。，则查询返回每个桶中神奇宝贝的名称、类型 1、类型 2 和总统计数据。

# GROUP BY 和 HAVING 子句

我们使用`WHERE`子句在列上放置条件，但是在组上放置条件呢？介绍`HAVING`条款！

`WHERE`关键字不能与聚合函数一起使用，所以我们使用带有`GROUP BY`的`HAVING`子句

我们可以使用`HAVING`子句来输入条件，以确定哪个组将成为最终结果的一部分。

另外，聚合函数不能受`WHERE`子句的影响。相反，如果我们想要应用条件，那么`HAVING`子句与聚合函数一起使用。

## **HAVING 子句的一般语法**

```
SELECT *column_name(s)*
FROM *table_name*
WHERE *condition*
GROUP BY *column_name(s)* HAVING *condition* ORDER BY *column_name(s);*
```

# GROUP BY With HAVING 子句示例

假设我们想要找到单个神奇宝贝的数量、类型 1、最小和最大总统计值以及按类型 1 分组的所有神奇宝贝的平均 HP，并且只包括总 HP 高于 4000 的神奇宝贝组:

这有助于我们确定哪组神奇宝贝在他们的职业中有最高的血量，同时剔除低于我们设定的 4000 上限的组。

如果我们想选择高 HP 的神奇宝贝，我们会选择普通类型 1 的，因为它们有最高的平均 HP (77.28)，肯定会受到一两次打击。

`HAVING`子句确实有助于将我们的数据精简为更有用、更有洞察力的东西！

# 结论

写这篇博客帮助我理解了如何利用`GROUP BY`的，我希望它也能帮助你！敬请关注即将发布的下一篇 SQL 博客！
# SQL 查询优化:提升您的 SQL 性能调优

> 原文：<https://betterprogramming.pub/sql-query-optimization-level-up-your-sql-performance-tuning-d93af175b24b>

## 增强您对生产数据系统的 SQL 查询

![](img/206d5c4c42ac24fec46ea73f7e03e50e.png)

来源:[https://pix abay . com/vectors/database-schema-data-tables-schema-1895779/](https://pixabay.com/vectors/database-schema-data-tables-schema-1895779/)

数据是任何应用程序不可或缺的一部分。应该以最快的方式访问数据，以增强用户在使用应用程序时的体验。

# 为什么还需要查询优化？

SQL 是目前最强大的数据处理工具之一。然而，SQL 是一种声明性语言，即每个查询都声明了*我们希望 SQL 引擎做什么*，但没有说*如何做*。然而，事实证明,*如何*——“计划”——才是影响查询效率的因素，所以这非常重要。

效率低下的查询会耗尽生产数据库的资源，并且如果查询包含错误，会导致其他用户的性能下降或服务丢失。优化查询以将对数据库性能的影响降至最低是至关重要的。

没有一步一步的指南。反过来，我们必须使用编写查询的一般准则，使用哪些操作符。然后检查“执行计划”，找出查询中花费时间最多的部分，并以其他方式重写该部分。

# 查询优化的好处

*   **最大限度地减少生产问题:**低效的查询需要大量的 CPU、内存和 I/O 操作，这些操作通常会导致死锁、事务失败、系统挂起和其他生产问题。
*   **性能问题:**慢速查询意味着使用查询的应用程序的响应时间较慢，这会导致较差的用户体验。
*   **节省基础设施成本:**由于非优化查询需要更多的 CPU 和内存，因此会导致更高的基础设施成本。

![](img/ab61896cd3f9c8e1e0d19eb52e37ea67.png)

克劳迪奥·施瓦茨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 查询优化最佳实践

*   **明智地选择数据类型:**与使用`char(10)`存储固定长度数据相比，使用`Varchar(10)`存储固定长度数据(如手机号码)会导致 20%的查询执行损失。
*   **避免使用隐式转换:**当查询比较不同的数据类型时，它使用隐式数据类型转换。因此，在查询中，应该避免比较不同数据类型的列和值，例如:

```
WHERE date >= "2022-01-01"
```

*   **避免使用基于函数的条件子句:**当查询在列的`WHERE`或`JOIN`子句中使用函数时，它不会利用索引，因此会降低执行速度。

```
-- WHERE date(ship_date) = '2022–01–01'-- JOIN T2 ON CONCAT(first_name,' ',last_name) = 'garvit arya'
```

*   **避免同时使用** `**DISTINCT**` **和**`**GROUP BY**`**:**如果您的查询中已经有了`GROUP` BY，就没有必要再单独拥有`DISTINCT`。
*   **避免同时使用** `**UNION DISTINCT**` **和**`**SELECT DISTINCT**`**:**对于具有 UNION DISTINCT 的查询，它会自动删除重复记录，因此不需要使用 SELECT DISTINCT。
*   **永远不要使用**`**SELECT ***`**:**选择不必要的列会浪费内存和 CPU 周期。选择列名总是比选择*或额外的列更好。
*   **尽可能避免子查询:**子查询创建临时表来存储数据，有时它会在磁盘上创建临时表，从而降低查询的执行速度。优先使用 WITH 子句而不是嵌套子查询。
*   **避免在子查询中使用 Order by:**子查询中的排序大多是多余的，会导致严重的性能问题。
*   **不要** `**GROUP**` **数字:**避免按 DOUBLE 或 FLOAT 类型的列分组，因为这可能会由于舍入&精度问题而导致意外行为。
*   **尽量少用** `**SELF**` **连接:**自连接计算量更大，在许多情况下可以用窗口函数代替。
*   **不要用 OR 条件连接表:**可以通过使用`UNION ALL`代替基于`OR`的连接进行优化。
*   **避免使用不等条件连接:**当查询使用`NOT EQUAL`操作符连接时，它会搜索所有行，并使用效率非常低的全表扫描。
*   **避免全文搜索:**当查询搜索开头带有通配符的关键字时，它不使用索引，数据库的任务是在所选字段内的任何位置搜索所有匹配的记录。因此，如果需要，最好只在短语末尾使用通配符。

```
SELECT user_nameFROM testWHERE user_name LIKE '%abc%'
```

*   用`WHERE`代替`HAVING`:最好用`where`代替`having`，因为`HAVING`语句是在`WHERE`语句之后计算的。
*   `IN`与`EXISTS`相比:`IN`操作符在扫描方面比`EXISTS`开销更大，尤其是当子查询的结果是一个大型数据集时。

# 最后的想法

理解执行计划和优化 SQL 查询可能会很乏味，需要一段时间来学习。我已经使用 SQL 好几年了，并且还在不断学习新的技术！

如果您努力遵循上面详细介绍的查询优化技术，您可以受益于查询性能的提高、更少的生产问题以及通过最小化资源来节省成本。

试试这些技巧，并在下面的评论区让我知道它们对你有多有效！

我希望，这篇短文对你有用。感谢您的阅读！

```
**Want to Connect?**You can reach out to me at — [Linkedin](https://www.linkedin.com/in/garvitarya/) | [Twitter](https://twitter.com/garvitishere) | [Github](https://github.com/GarvitArya/).
```
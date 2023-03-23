# AWS Athena 规模

> 原文：<https://betterprogramming.pub/aws-athena-at-scale-a90c58c3b110>

## 它很强大，但很有气质

![](img/fb6f791e1daad6fa56b2062766abf01b.png)

帕特里克·托马索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

如果您使用 AWS 进行数据转换，您迟早会遇到 Athena。Athena 允许您使用众所周知的 SQL 语法(Presto 6.15)跨多个数据存储查询数据。

使用 S3 作为存储解决方案，Athena 承诺为您处理复杂的大型数据库。无服务器计算和存储意味着完全无服务器的数据库体验。你需要做的就是知道所有的危险信号在哪里。在这篇文章中，我列出了过去几个月我发现自己处于的一些情况。

# 雅典娜的分区是不可协商的

当您将数据写入 AWS Glue 表时，应该有一个词在您谈话的最前面:分区*。分区指示 AWS Glue 如何在 S3 将你的文件分组，这样你的查询就可以在尽可能小的数据集上运行。*

几十年来，糟糕的分区策略一直是数据库的祸根。很难做到这一点，因为优化不可避免地意味着在某些方面变得更差，因为你专攻其他方面。以下是在设计分区时应该问自己的问题:

*   如何查询这些数据？
*   这意味着每个分区有多少数据？
*   我们多久查询一次这些数据？
*   这种数据存储会变得完全不同吗？我们该如何处理？

一种非常常见的分区策略是按日期键进行分区。AWS Glue libraries 附带了一种机制，用于指定开箱即用的分区列。使用这些库，您的代码可能如下所示:

```
glue_context.write_dynamic_frame.from_options(
    frame = projectedEvents,      
    connection_options = {
        "path": "$outpath", 
        "partitionKeys": ["date"]
    },
    format = "parquet"
)
```

在这个例子中，我们告诉 Glue 以 parquet 格式编写输出，并在`date`字段上进行分区。如果我们打开 S3，我们会看到[蜂巢式分区](https://docs.aws.amazon.com/athena/latest/ug/partitions.html)的形式:

```
/date=2020-05-01/…/type=2020-05-02/…/type=2020-05-03/…
```

我们的声明是这样的:“我们希望在一天内优化查询。”如果我们计划运行跨越许多天的大量查询，这种分区策略不会帮助我们优化成本。请记住，Athena 是根据扫描的数据量收费的。

## 不要害怕存储数据的多个视图

与读/写成本相比，您的 AWS 存储成本微不足道。如果您可以通过使用不同的分区存储一组重复的数据来极大地优化您的 S3 I/O，这通常会节省成本。

## 拼花地板可以为你节省很多钱

对于外行人来说，你选择的输出格式看起来像是个人偏好(比如:几周前的我)。但是，这种选择会对系统的运营成本产生深远的影响。Parquet 是一种列存储格式，这意味着它不会将整行组合在一起。一个文件可能包含给定行的列的子集。

如果您对一堆 JSON 文件运行这样的查询，您认为 Athena 必须做什么？

```
SELECT name, age, dob from my_huge_json_table where dob = '2020-05-01';
```

它将被迫从整个 JSON 文档中找出与那个`dob`匹配的所有内容，并对其进行扫描。JSON 文件中可能有 100 个不同的列，但是您只对其中的三个感兴趣。

对 parquet 运行相同的查询更容易优化。Athena 可以嗅出它需要的确切文件，而不是提取整个文件。不会很完美。根据文件的大小，Athena 可能会被迫筛选一些额外的数据，但这一额外的维度意味着特定的查询可以在特定的数据集上操作。这导致[潜在的重大](https://aws.amazon.com/athena/pricing/)成本节约。

# Athena 不仅仅擅长提取数据

文档中经常提到 Athena，一旦您对它满意了，就可以用它从表中提取数据。它几乎是 API 可以挂钩的表示层。这是正确的，但有局限性。

SQL 是一种功能强大的数据转换语言，如果使用得当，可以产生运行速度非常快的作业。一些操作，比如窗口函数和聚合函数，在 SQL 语法中工作得很好，并产生更加简单、优雅的代码。随着 [CTAS](https://docs.aws.amazon.com/athena/latest/ug/ctas.html) 的引入，您可以直接将元数据写入 Glue 数据存储，而不需要爬虫。

我们学到的一个教训是，Athena 可以用来清理数据本身。Athena 使用了 Presto 6.15 —阅读一下[文档](https://www.bookstack.cn/read/Presto0.220Documentation/1b5d73f12d2542a7.md)。Presto DBMS 有许多很好的功能可以利用。

雅典娜是强大的，但它有一些怪癖，我们花了一段时间来解决。

# 雅典娜不喜欢连字符

这是一个小问题，但它会导致一些奇怪的行为。不要在表名中使用连字符。他们可以打破你的查询。不是那种“哦，一切都突然变得支离破碎”的感觉。以“哦，这个查询现在完全是随机的”的方式。

![](img/3c8886577058ecc724c1d3788add6a20.png)

我知道生气是一件有趣的事情，但是用下划线就好了。省省你的血压峰值吧。图片来自 [SEO 机械师](https://www.seomechanic.com/seo-101-hyphens-underscores-_-urls/)。

避开垃圾箱，去找下划线。它不会对可读性造成太大的改变，也减少了一件需要担心的事情。如果您坚持使用连字符，您可以用`"`将列名换行。当我看到它突然出现时，这已经修复了问题，但我不知道这是一个真正的修复还是有怪癖。

# 雅典娜真的不喜欢 Global `ORDER BY`

这在其他 DBMS 系统中也会出现，但这是我们的一些 Athena 查询遇到“资源耗尽”错误的原因:

```
AWS Athena Error: Query exhausted resources at this scale factor
```

在出现这种情况的每一个案例中，我们都发现优化查询的最佳方式是将查询中的`ORDER BY`语句的数量限制到最低限度。这是因为对整个数据集使用`ORDER BY`意味着将数据移动到单个节点上，以便对其进行排序。

`ORDER BY`声明只是贪婪的雅典娜查询的罪魁祸首之一。幸运的是，AWS 已经为你准备了一个[很棒的选项列表](https://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/)，让你可以充分利用 Athena，而不至于在都柏林的某个地方烧毁服务器。
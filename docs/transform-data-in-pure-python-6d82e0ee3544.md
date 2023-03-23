# 用纯 Python 转换数据

> 原文：<https://betterprogramming.pub/transform-data-in-pure-python-6d82e0ee3544>

## 不需要外部库

![](img/01809e585cc49be2aa4aafbe6f962e9e.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

开发人员经常选择 Python 来编写数据转换。凭借其庞大的第三方软件包生态系统，功能可以快速集成。

在处理大数据时，像 [pandas](https://pandas.pydata.org/) 、 [PySpark](https://spark.apache.org/docs/latest/api/python/index.html) 、 [NumPy](https://numpy.org/) 、 [TensorFlow](https://www.tensorflow.org/) 和 [PyTorch](https://pytorch.org/) 这样的库允许您快速操作和转换数据。

但是，如果您有少量数据，几百条记录，甚至一千条记录，该怎么办呢？需要使用外部库吗？

并非所有时候，Python 都有简单的内置方法来帮助转换数据。

# 列出理解

列表理解是一个很好的内置特性，具有简洁的数据转换语法。

如果没有列表理解，假设您想要生成一个 5 行 5 列的数据结构，您将需要使用如下的`for`循环。

这是六行代码，不算太糟。如果你能在一行中做同样的事情会怎么样？

```
[[randint(0, 10) for y in range(5)] for x in range(5)]
```

上面这一行要简洁得多，它消除了收集和添加条目到列表中的需要。实际上有两种理解:

```
# Collects rows
[<row> for x in range(5)]# Builds each row
[randint(0, 10) for y in range(5)]
```

# 过滤数据

上面的代码是一个转换数据的例子。它将两个范围语句转换成一个 5x5 的数据矩阵。列表理解也可以使用条件来进一步转换数据。

```
# Select columns 0, 1, 3
[[column for x, column in enumerate(row) if x in (0, 1, 3)] for row in data]
```

上面的行从每行中选择第 0、1 和 3 列，并构建一个新的矩阵。再一次，它实际上是两个列表理解。

```
# Row iterator
[<row> for row in data]# Transforms row
[[column for x, column in enumerate(row) if x in (0, 1, 3)]
```

# 词典释义

字典也可以用理解来构造。以下逻辑将创建基于列的数据视图，键是列号，值是列值。

```
# Assumes all rows have the same number of columns
columns = {i: [row[i] for row in data] for i in range(len(data[0]))}
```

这个例子像前面的例子一样遍历一个列表，但是它增加了一个新的变化。每次迭代都构建一个键值对，而不是只有一个表达式。

# 运行示例

以下是所讨论功能的完整示例。

示例代码

这将生成以下输出:

```
Row view:
[[1, 2, 4, 7, 8],
 [1, 0, 0, 5, 2],
 [0, 0, 2, 3, 12],
 [1, 0, 0, 5, 14],
 [1, 1, 1, 7, 3]]Column view:
{0: [1, 1, 0, 1, 1],
 1: [2, 0, 0, 0, 1],
 2: [4, 0, 2, 0, 1],
 3: [7, 5, 3, 5, 7],
 4: [8, 2, 12, 14, 3]}Stats:
Column 0, min=0.00, max=1.00, mean=0.80, stdev=0.45
Column 1, min=0.00, max=2.00, mean=0.60, stdev=0.89
Column 2, min=0.00, max=4.00, mean=1.40, stdev=1.67
Column 3, min=3.00, max=7.00, mean=5.40, stdev=1.67
Column 4, min=2.00, max=14.00, mean=7.80, stdev=5.31
```

# 结论

Python 已经内置了很多语言。

使用 pandas 和 NumPy 这样的库，所有讨论的功能都变得非常简单。但是，如果您有一个小项目和/或希望限制对第三方的依赖，这并不总是必要的。

用纯 Python 也是个不错的选择。
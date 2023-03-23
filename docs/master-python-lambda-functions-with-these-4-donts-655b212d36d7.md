# 掌握 Python Lambda 函数的这四个不要

> 原文：<https://betterprogramming.pub/master-python-lambda-functions-with-these-4-donts-655b212d36d7>

## 使用兰姆达斯，但不要误用它们

![](img/a34669e566532c6edef819c5dbd56cfc.png)

由[哈契克·西莫尼安](https://unsplash.com/@khachiksimonian?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Lambda 函数是 Python 中的匿名函数。当您需要执行小型工作时，在本地环境中使用它们是一种方便的技术。有些人简单地称它们为 lambdas，它们的语法如下:

```
lambda arguments: expression
```

lambda 函数的创建由关键字`lambda`表示，后跟一列参数和一个由冒号分隔的表达式。例如，`lambda x: 2 * x`只是将任何输入的数字乘以 2，而`lambda x, y: x+y`只是计算两个数字的和。语法非常简单，对吗？

假设您知道 lambda 函数是什么，本文旨在提供一些关于如何正确使用 lambda 函数的通用指南。

# 1.不返回任何值

看一下语法，你可能会注意到我们没有为 lambda 函数返回任何东西。这都是因为 lambda 函数只能包含一个表达式。但是，使用`return`关键字将构成一个与所需语法不兼容的语句，如下所示:

在 lambda 中没有返回

这个错误可能是由于不能区分表达式和语句而产生的。像那些涉及`return`、`try`、`with`和`if`的语句执行特定的动作。但是，表达式是那些可以计算为单个值的表达式，例如数字或其他 Python 对象。

使用 lambda 函数，单个表达式将计算随后使用的单个值，例如由`sorted`函数排序。

# 2.不要忘记更好的选择

一个最常见的用例是为一些内置实用函数的`key`参数设置一个 lambda 函数，比如`sorted()`和`max()`，如上所示。根据情况，我们可以使用其他替代方案。考虑下面的例子:

内置函数的使用

在数据科学中，很多人使用 [pandas](https://pandas.pydata.org/) 库来处理数据。我们可以使用 lambda 函数通过使用`map()`函数从现有数据创建新数据，如下所示。不使用 lambda 函数，我们可以直接使用算术函数，因为它在 pandas 中受支持:

串联λ函数

# 3.不要把它赋给变量

我见过一些人错误地认为 lambda 函数是声明一个简单函数的替代方法，你可能见过有人这样做:

命名 lambda 函数

命名 lambda 函数的唯一用途可能是用于教学目的，以表明 lambda 函数确实是一个像其他函数一样的函数——被调用并具有一种函数类型。除此之外，我们不应该把 lambda 函数赋给一个变量。

命名 lambda 函数的问题是它使得调试不那么简单。与其他使用常规关键字`def`创建的函数不同，lambda 函数没有名字，这就是为什么它们有时被称为匿名函数。考虑下面的小例子来了解这种细微差别:

λ函数的调试

*   当你的代码在一个 lambda 函数(即`inversive0`)上有问题时，`Traceback`错误信息只能告诉你一个 lambda 函数有 bug。
*   相比之下，对于定期定义的函数，`Traceback`会清楚地通知您有问题的函数(即`inversive1`)。

与此相关，如果您想不止一次地使用 lambda 函数，最佳实践是使用带有`def`关键字的常规函数，这也将允许您拥有 docstrings。

# 4.不要忘记列表理解

有些人喜欢用高阶函数的 lambda 函数，比如`map`或者`filter`。考虑下面这个用法的例子:

映射和过滤功能

我们可以不使用 lambda 函数，而是使用 list comprehension，这样可读性更好。如下所示，我们使用列表理解来创建相同的列表对象。正如你所看到的，与 list comprehension 相比，前面使用 lambda 函数的`map`和`filter`函数更麻烦。所以当你创建包含高阶函数的列表时，你应该考虑使用列表理解。

列表理解

# 结论

在本文中，我们回顾了一些人在使用 lambda 函数时可能犯的四个常见错误。通过避免这些错误，您应该能够在代码中正确使用 lambda 函数。

使用 lambda 函数的经验法则是保持简单，并且只在本地使用一次。
# 在 Python 中使用生成器:原因、内容和时间

> 原文：<https://betterprogramming.pub/using-generators-in-python-the-why-the-what-and-the-when-e7d33c0b606>

## 通过 4Ws 解释 Python 生成器

![](img/94dc87e4dae1738661ea673de3d75434.png)

照片由 [Dani Aláez](https://unsplash.com/@danialaez?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

今天，“Python 中的生成器是什么”和“Python 中的生成器有什么用”是一些最受欢迎的 Python 面试问题。

通常，[生成器](https://wiki.python.org/moin/Generators)被认为是 Python 中稍微更中间的概念之一。如果您是学习 Python 的新手，您可能以前没有遇到过 Generator。这里有一个提示，它与函数内部的`yield`语句的使用有关。

在这篇文章中，我将重点介绍在 Python 中使用生成器的一些用例、原因和优势。简而言之，在处理内存受限的大型数据集时，应该考虑使用生成器。

我们再深入一点，好吗？

## TL；速度三角形定位法(dead reckoning)

*   在处理大型数据集时，可以考虑使用生成器
*   考虑在不需要重复多次的场景中使用生成器
*   生成器给我们懒惰的评价
*   它们是以高效的方式生成序列的好方法

# 我为什么要关心发电机的使用

## 内存限制

为了理解你为什么应该使用生成器，我们必须首先理解计算机有有限的内存( [RAM](https://en.wikipedia.org/wiki/Random-access_memory) )。每当我们存储或操作变量、列表等时。所有这些都储存在我们的记忆中。

你可能会问，为什么计算机程序要把它们存储在内存里？因为这是我们写入和检索数据的最快方式。

## 情节

你曾经不得不处理一个大得让你碰到`MemoryError`的列表吗？也许，你已经尝试过从一个超大的 Excel(或`.csv`)文件中读取行。我所记得的是，执行这些任务极其缓慢或者根本不可能。

# 什么是发电机功能

简单地说，生成器函数是一种特殊的函数，它返回多个项。这里的要点是，项目是一个接一个返回的，而不是一次全部返回。

常规函数和生成器函数的主要区别在于 Python 中分别使用了`return`和`yield`语句。

## 生成器给你懒惰的评价

你可能遇到过这种说法。但是，这到底意味着什么呢？

如果你熟悉[迭代器](https://www.w3schools.com/python/python_iterators.asp)，那么一个生成器函数本质上就是这样一个函数。

在后台，生成器不会在实例化时计算每一项的值。相反，当我们要求时，他们会计算。这就是人们所说的发电机给你*懒*的评价。

因此，生成器允许我们一次处理一个值，而不必先将所有内容都加载到内存中。

# 我应该在何时何地使用发电机

当您遇到需要从大型数据集读取数据的问题时，生成器非常有用。从大型数据集中读取间接意味着我们的计算机或服务器必须为它分配内存。

需要记住的唯一条件是，一个生成器只能迭代**一次**。换句话说，只要我们不需要数据集中以前的值，我们总是可以使用 Generator。

## 读取相当大的 CSV

使用生成器的另一个常见用例是当我们处理 Excel 或 CSV 文档这样的大文件时。在不使用生成器函数的情况下，我们可以这样写:

使用常规函数的示例

在运行上面的例子时，我们可能会经历一些缓慢甚至`MemoryError`取决于我们的电脑。

查看上面的代码示例，为了生成结果，`read_csv_from_regular_fn`将打开我们的 CSV 文件，并在一个实例中加载内存中的所有内容。

当处理比我们可用内存更大的文件时，这不是一个好的解决方案。或者，我们可以这样做:

使用生成器函数的示例

在这个场景中，我们基本上使用`read_csv_from_generator_fn`作为我们的生成器函数。这个新的生成器打开我们的大型 CSV 文件，遍历每一行，一次生成每一行，而不是一次生成所有行。

在这里，当从我们的`large_dataset.csv`读取数据时，我们不会遇到任何`MemoryError`，甚至不会因为内存限制而变慢。

要以字节为单位检查内存使用情况，我们可以执行以下操作:

```
import sysprint(sys.getsizeof(read_csv_from_generator_fn())) # 112 bytes
print(sys.getsizeof(read_csv_from_regular_fn())) # 1624056 bytes
```

## 遍历一个大列表(数组)

另一个经常使用生成器的例子是我们打算处理大型列表中的值:

```
**# Example 1**
nums_list_comprehension = [i * i for i in range(100_000_000)]sum(nums_list_comprehension) # 333333328333333350000000
```

根据你的电脑，你可能会遇到`MemoryError`或至少几秒钟的缓慢，当评估上述表达式。

类似于[列表理解](https://www.w3schools.com/python/python_lists_comprehension.asp)，生成器表达式允许我们快速创建一个生成器对象，而不必使用`yield`语句。

为了应对我们的内存限制，我们可以将上面的代码示例转换成一个生成器表达式。下面这一行代码几乎可以立即计算:

```
**# Example 2**
nums_generator = (i * i for i in range(100_000_000))
# <generator object <genexpr> at 0x106ecc580>sum(nums_generator) # 333333328333333350000000
```

在示例 1 中，用于整个范围的`100_000_000`的`i ** i`被预先评估并存储在存储器中。它返回一个完整的列表。

在示例 2 中，`i ** i`仅在迭代时被评估，一次一个。它返回一个生成器表达式。

记住，生成器在被实例化时不会计算每一项的值。

内存使用的差异如下:

```
import sysprint(sys.getsizeof(nums_generator)) # 112 bytes
print(sys.getsizeof(nums_list_comprehension)) # 835128600 bytes
```

# 何时不使用发电机

## 我们需要以前的值

一个生成器只能迭代一次。

下面的例子显示了来自`nums_generator`的生成器表达式只能迭代一次。第二次在它上面使用`sum`的结果是零，因为发电机耗尽了。

```
# Continuing from Example 2
sum(nums_generator) # 333333328333333350000000
sum(nums_generator) # 0, because it can only be iterated once.
```

## 处理相对较小的文件

当处理相对较小的文件或列表时，我们可能不想使用 Generator，因为它可能会降低我们的速度。

我们可以使用前面的例子`[cProfile](https://docs.python.org/3/library/profile.html)`来描述列表理解和生成器表达式在对值求和时的性能差异。

`cProfile`使用列表理解与生成器表达式进行求和

从上面的`cProfile`结果中，我们可以看出，如果我们不遇到内存限制，使用列表理解会快很多。

显然，如果记忆不是问题，我们应该坚持使用常规函数或列表理解。

# 结论

总之，在我们不需要重复多次的场景中，Generator 是 Python 中一个令人惊奇的工具。

由于生成器为我们提供了惰性评估，它们是一种以内存高效的方式生成序列的好方法。在处理大型数据集时，我们应该考虑使用 Generator 来优化我们的程序。

感谢您的阅读！

## [最初发表于 jerrynsh.com](https://jerrynsh.com/using-generators-in-python-the-why-the-what-and-the-when/)

[](/everything-you-need-to-know-about-context-managers-in-python-f83556fbdfb) [## 关于 Python 中的上下文管理器，您需要知道的一切

### 通过示例了解 Python 中“with”语句背后的魔力

better 编程. pub](/everything-you-need-to-know-about-context-managers-in-python-f83556fbdfb) [](/how-to-write-clean-code-in-python-5d67746133f2) [## 如何用 Python 写干净的代码

### 使用 Python 示例编写干净代码的 3 个技巧

better 编程. pub](/how-to-write-clean-code-in-python-5d67746133f2)
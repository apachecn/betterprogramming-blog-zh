# Pydash 的 5 个 Python 生产率技巧

> 原文：<https://betterprogramming.pub/5-python-problems-to-solve-with-pydash-c4bcf5d18fc0>

## 使用这个实用框架来简化事情

![](img/572ff9b04339d6aee69102d319367043.png)

由 [Pakata Goh](https://unsplash.com/@pakata?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

最近，我发现了`pydash`，并且很高兴地知道有一个框架可以解决过去让我有些头疼的简单 Python 问题。

在本文中，我将向您展示 Python 中的 5 个问题，您可以使用 Pydash 轻松解决这些问题。

# Pydash 是什么？

正如它自己的[文档页面](https://pydash.readthedocs.io/en/latest/)所描述的:`pydash`是

> Python 实用程序库的厨房水槽，用于以函数方式做“事情”。

基本上，它是一个以函数方式解决常见 Python 问题的 Python 库。

在下一节中，我们将看看这些问题，看看`pydash`如何为您的开发工作流做出贡献。来看看`pydash`能解决的 5 个常见问题。

# **1。展平列表**

```
pydash.flatten([1,2,[3,4]])
# Output
[1, 2, 3, 4]pydash.flatten_deep([1, 2, [3, [4, 5, [6, 7]]]])
# Output
[1, 2, 3, 4, 5, 6, 7]
```

在过去，我曾经使用列表理解来做这件事，但是`pydash`让展平变得简单，只需要简单地调用`pydash.flatten()`或`pydash.flatten_deep()`。

# 2.**获取多个字典中给定关键字的值**

```
pydash.map_([{'name': 'moe', 'age': 40}, {'name': 'larry', 'age': 50}], 'name')
# Output
['moe', 'larry']
```

# 3.从字典中省略条目

```
pydash.omit({'name': 'moe', 'age': 40}, 'age')
# Output
{'name': 'moe'}
```

这里，通过给`age`键作为`pydash.omit()`方法的参数，我们从输出中省略了键及其相应的值。

# 4.**链接条件，从列表中生成自定义过滤输出**

```
pydash.chain([1, 2, 3, 4]).without(2, 3).reject(lambda x: x > 1).value()
# Output
[1]
```

这里，我们使用`pydash.chain()`方法链接一系列条件来过滤给定的列表，首先用`.without(2,3)`删除 2 和 3，然后用`.reject(lambda x: x>1)`拒绝所有大于 1 的值，最后用`.value()`得到输出值。

# 5.**通过执行一个函数 n 次来简化循环**

```
pydash.times(10, lambda index: 2*index)
# Output
[0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

这里，我们可以使用`pydash.times()`方法迭代 *n* 次，并执行一个 lambda 函数(作为参数给出)来生成所需的列表，而不是创建一个循环并将其附加到一个列表中。

# 为什么要关心公用事业？

`pydash`提供的这些简单实用程序使日常开发工作流程更容易、更快、更简单。

当然，最初的挑战是经历实际学习一个新框架的麻烦，但是一旦我们意识到仅仅通过知道使用哪个简单的`pydash`实用程序就可以节省多少时间，这种方法从长远来看肯定会胜出

如果你喜欢这篇文章，请随时在 [Twitter](https://twitter.com/LucasEnkrateia) 或 [LinkedIn](https://www.linkedin.com/in/lucas-soares-969044167/) 上与我联系。谢谢，下次再见！
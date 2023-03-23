# Python 中 Lambdas 的 4 大误用

> 原文：<https://betterprogramming.pub/the-top-4-misuses-of-lambdas-in-python-e419f426b74f>

## 使用 Python lambdas 的最佳实践

![](img/86a9fc75a84039d7ccef53ac365f0119.png)

约翰·施诺布里奇在 [Unsplash](https://unsplash.com/s/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

被认为是非常 Python 化的，lambdas 是 Python 编程中最受欢迎的特性之一。以至于许多 Python 程序员都想尽可能地使用它们。

当然，lambdas 具有使我们的代码简洁的优势，但是在我们的项目中过度使用它们会导致误用，降低我们代码的可读性和可维护性。

在我们开始探究这些误用是什么之前，让我们先快速回顾一下什么是 lambdas。如果你非常了解他们，你可以跳到下一部分。

Lambdas 也称为 lambda 函数，是匿名函数，接受任意数量的参数，但只有一个表达式。它们的声明由关键字`lambda`表示。基本语法如下。

```
lambda arguments: expression
```

Lambdas 最适合于需要小功能并且只使用一次的地方。lambdas 的一个常见用法是将其设置为内置`sorted()`函数中的`key`参数。这里有一个例子。

```
>>> students = [('Mike', 'M', 15), ('Mary', 'F', 14), ('David', 'M', 16)]
>>> sorted(students, key=lambda x: x[2])
[('Mary', 'F', 14), ('Mike', 'M', 15), ('David', 'M', 16)]
# The students are sorted by age
```

许多教程已经很好地解释了 lambda 是什么以及在哪里可以使用 lambda，因此，没有充分的理由在这里重复它们。

相反，本文的目的是向您展示 lambdas 最常见的误用，以便当您在下面列出的场景之外的其他场景中使用它们时，您可能会正确地使用它们。

# 1.重复发明

lambdas 的第一个误用是忽略现有的内置函数。

我们还是以`sorted()`函数为例。假设我们有一个字符串列表，我们想根据它们的长度对它们进行排序。

当然，lambda 函数`lambda x: len(x)`有效，但是直接使用内置的`len()`函数怎么样？

这是另一个使用`max()`函数的例子。

最佳实践#1:在编写自己的 lambdas 之前，首先考虑内置函数。

# 2.将它赋给一个变量

我在一些教程中看到过 lambda 对变量的赋值，包括我的一些教程，但它主要是为了向初学者展示 lambda 本质上是函数。

然而，一些初学者可能认为这是很好的实践，认为 lambdas 只是声明短函数的一种方便的方式。下面的代码片段向您展示了这种误用。

```
>>> divide_two_numbers = lambda x, y: x / y
>>> divide_two_numbers(4, 5)
0.8
```

为什么要避免这种情况？如果你还记得上面提到的，lambda 应该只使用一次，因此没有理由把 lambda 赋给一个变量。

如果我们确实需要使用相关的功能，我们应该使用`def`关键字来声明一个常规函数，如下所示。

如果你觉得这个简单的函数用两行代码不酷，我们可以用一行来重写:`def divide_two_numbers_fun(x,y): return x / y`，工作方式也一样。

```
>>> def divide_two_numbers_fun(x,y): 
...     return x / y
... 
>>> divide_two_numbers_fun(7, 8)
0.875
```

避免将 lambda 赋给变量的主要原因是出于调试/维护的目的，尤其是在生产/团队合作环境中。

让我们用一个简单的例子来看看可能会发生什么。在真实情况下，事情可能会复杂得多。

正如您在上面看到的，通过正则函数的声明，我们确切地知道是哪个函数导致了错误。相比之下，使用 lambdas，它只告诉我们有一个 lambda 导致了错误。

为什么没有显示函数名？

这是因为 lambdas 是匿名函数，它们都共享同一个名字— `<lambda>`。你能想象如果你的同事发现几十个`<lambda>`有错误会有多沮丧吗？

![](img/b9d3f3a115d106e89d55979fe7632e01.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4785077) 的 [Gerd Altmann](https://pixabay.com/users/geralt-9301/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4785077)

最佳实践#2: 当函数需要多次使用时，编写一个常规函数，而不是 lambda。

# 3.不适当地过度使用高阶函数

当我们说高阶函数时，我们指的是通过将函数作为参数或通过返回函数来对其他函数进行操作的函数。

与当前主题相关的函数有`map()`、`filter()`、`reduce()`，这些函数在很多关于 lambdas 的教程中都或多或少的使用过。但是这导致了 lambdas 和高阶函数的某种误用。

出于演示的目的，我将在本教程中只使用`map()`函数，但是同样的原理也适用于其他高阶函数。

假设我们有一个整数列表，我们想要一个由它们的平方组成的列表。使用带有`map()`函数的 lambda 将如下所示。

我们将获得一个迭代器——来自`map()`函数的 map 对象，然后，为了将它转换成一个列表，我们需要在这个迭代器上调用`list()`函数。

```
>>> numbers = [1, 2, 3, 5, 8]
>>> squares = list(map(lambda x: x * x, numbers))
>>> squares
[1, 4, 9, 25, 64]
```

实际上，同样的功能可以通过列表理解方便地实现——不需要高阶函数或 lambdas。简洁易读多了，不是吗？

当然，掌握列表理解是另一个“Pythonic 特性”主题，需要另一个教程。

```
>>> numbers = [1, 2, 3, 5, 8]
>>> squares = [x * x for x in numbers]
>>> squares
[1, 4, 9, 25, 64]
```

最佳实践#3:考虑用列表理解替换高阶函数。

# 4.表达太繁琐

这种情况比前几次更少见。但一些程序员只是尽可能使用 lambdas 来编写最 Pythonic 化的代码。它有时有一个代价——可读性。

假设我们有一个字符串列表，我们需要使用一个奇怪的要求对它们进行排序:单词中不同元音的数量。在`sorted()`函数中使用 lambda 将如下所示。

```
>>> texts = ['iiiii', 'bag', 'beto', 'blackboard', 'sequoia']
>>> sorted(texts, key=lambda x: len(set([l for l in list(x) if l in ['a','e','i','o','u']])))
['iiiii', 'bag', 'beto', 'blackboard', 'sequoia']
```

它像预期的那样工作，但是它肯定不是最可读的代码。下面的代码怎么样？

当然，我们需要多写几行代码，但是新代码的可读性不是更好吗？

最佳实践#4:如果 lambda 的表达式太麻烦，就写一个正则函数。

# 外卖食品

对于 Python 初学者来说，Lambdas 一直是一个困难的话题，他们一开始会不惜一切代价避免使用它们。

过了一段时间，当他们的恐惧消失了，他们开始学习兰姆达斯，并发现他们并不真的很难。然后他们开始使用兰姆达斯，但不幸的是，有些人可能使用他们太多，导致各种各样的误用，如上所述。

我希望这篇文章可以帮助解决这些问题。

通过避免这些误用并遵循最佳实践技巧，我敢打赌，正确使用 lambdas 的 Python 代码将具有更好的可读性和可维护性。
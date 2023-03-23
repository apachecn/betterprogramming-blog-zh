# 要避免的 3 个混乱的 Python 特性

> 原文：<https://betterprogramming.pub/3-chaotic-python-features-to-avoid-9c609908bc13>

## 避开这些令人困惑的片段

![](img/4d139d12a7ca5de0578aa2832a1b8af9.png)

[Imran Bangash](https://unsplash.com/@imranbangash?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

在过去的几十年里，Python 已经成为最流行的通用编程语言之一。因此，它已经被不同领域的人所采用，例如金融、科学和工程。Python 受欢迎的原因之一是它的多功能性——通常有不同的方法来实现相同的功能。然而，有些方法可能会让其他人感到困惑。在这种情况下，您应该考虑替代方案。

在本文中，我将讨论几个可能令人困惑的 Python 特性——尤其是对初学者而言。作为比较，我将提供实现相同功能的替代方案。正如您将看到的，替代方案可能需要更多的代码，但是由于它们的清晰性，它们应该是首选的。毕竟，可读性是您应该为您的编码项目努力实现的最大目标。

# for/while 循环中的 else 子句

大多数编程语言在`for`和`while`循环方面都有相似的特性。它们具有以下一般形式:

```
for item in list_or_array:
    do something with the itemwhile some_condition_evaluated:
    do something
```

不管我们的编程水平如何，它们对我们所有人来说都是直观的。然而，Python 允许将`else`子句用于`for`和`while`循环。观察以下这些特征:

for 和 while 循环中的 else 子句

我们在`if…else…`语句中最常看到`else`子句，其中`else`子句仅在前一个`if`子句不运行时运行。这种行为可能会让一些人认为同样的事情也会发生在`for`和`while`循环中的`else`子句上。然而，如果您运行上面的代码，您会发现两个`else`子句在每种情况下都运行。

因此，在这些情况下，`else`子句的规则是，只有在`for`或`while`子句中执行了`break`语句时，才会跳过该子句。观察下面的对比:

使用 break 跳过 else 子句

因此，除非在`for`或`while`循环中有一个`break`语句，否则对`else`子句没有任何影响，该子句在完成前一个子句后仍然会运行。换句话说，这是大多数情况下的建议:

```
# Instead of this
for item in iterable:
    do_a
else:
    do_b# Do this
for item in iterable:
    do_ado_b
```

# 对 setdefault 使用默认值

当我们从字典中检索一个值时，如果字典中不存在一个键，Python 会抛出一个`KeyError`:

```
>>> grades = {"John": 90, "Jennifer": 95}
>>> grades["Josh"]
Traceback (most recent call last):
  File "<input>", line 1, in <module>
**KeyError**: 'Josh'
```

因此，有些人可能会使用`setdefault`来检索键值。当在字典中找不到关键字时，它将使用指定的默认值。

```
>>> grades.setdefault("Josh", "N/A")
'N/A'
```

然而，这个方法的名字可能是违反直觉的——特别是如果您有任何其他编程背景，您可能永远不会想到“setter”方法可以返回值。

如果您真的想在检索字典值时有一个回退，更好的替代方法是使用`get`方法:

```
>>> grades.get("Jack", "N/A")
'N/A'
```

您可能想知道为什么 Python 有两个似乎做同样事情的方法。因为他们不完全一样。`setdefault`方法将返回回退值，同时，它将通过设置缺少的键和默认值来更新字典。`get`方法将简单地返回回退值，而不改变字典。如下所示，字典有`Josh`键，但没有`Jack`键:

```
>>> grades
{'John': 90, 'Jennifer': 95, 'Josh': 'N/A'}
```

如果您希望设置与使用`setdefault`方法相关的行为，您可以考虑`defaultdict`数据类型，它是`dict`的子类，具有在缺少键时生成默认值的特性。以下代码向您展示了返回`‘N/A’`并为丢失的键设置值的实现:

```
>>> from collections import defaultdict
>>> grades = defaultdict(lambda: "N/A", {"John": 90, "Jennifer": 95})
>>> grades["Jack"]
'N/A'
>>> grades
defaultdict(<function <lambda> at 0x1058071f0>, {'John': 90, 'Jennifer': 95, 'Jack': 'N/A'})
```

# 切片中的负步骤

切片是从序列数据类型(如列表或字符串)中检索多项的强大方法。当我们对序列进行切片时，我们通常指定起始和结束索引。此外，我们可以在切片中指定一个步骤，在指定的步骤之后获取项目。下面是一个简单的例子:

```
>>> integers = [0, 1, 2, 3, 4, 5, 6]
>>> integers[0:7:2]
[0, 2, 4, 6]
>>> integers[::3]
[0, 3, 6]
```

切片有几个显著的特征:

1.  可以省略开始和结束索引。Python 将为您应用正确的索引。
2.  如果您使用看似索引外的索引，Python 将为您使用适用的绑定索引。

一个额外的特性是你可以在切片中应用一个负的步骤。该功能的一个广为人知的应用是反转序列，如下所示:

```
>>> integers[::-1]
[6, 5, 4, 3, 2, 1, 0]
```

但是猜猜下面的操作会发生什么？

```
integers[0:6:-1]integers[-1:-7:-1]
```

如果您运行这两行代码，您将得到下面的结果:

```
>>> integers[0:6:-1]
[]
>>> integers[-1:-7:-1]
[6, 5, 4, 3, 2, 1]
```

你可能想知道发生了什么。当你有一个消极的步骤，切片将从右向左计数。本质上，开始索引指的是右边的项，结束索引指的是左边的项。从右边移到左边，切片将采取指定的步骤来检索相应的项目。

例如，切片`[0:6:-1]`意味着从索引`0`处的项目开始，向左移动直到索引`6`处的项目，并向前左移一步。因为起始索引在结束索引的左侧，所以会产生一个空列表。同样的操作发生在`[-1:-7:-1]`上，由于起始索引在结束索引的右侧，所以从右向左移动时可以产生一个项目列表。

这个解释可能会让你感到困惑。因此，通常不建议使用这个特性。当您需要反转一个序列时，您可以简单地使用 Python 中可用于排序数据类型的`reverse`方法。颠倒顺序后，你可以应用积极的一步，产生更直观的结果，而不会混淆他人。下面是一个简单的例子:

```
# instead of
integers[-1:-7:-1]# do this (or something similar)
integers.reverse()
integers[0:6:1]
```

# 结论

在本文中，我们回顾了三个可能会让其他 Python 开发人员感到困惑的特性——尤其是那些不熟悉这种语言的人。如前所述，代码最重要的目标是尽可能获得最好的可读性。因此，建议不要使用这些功能。

但是，您仍然想知道它们是什么，因为您可能会在其他人的代码中遇到它们。
# 你应该学习的 4 个不常见的 Python 技巧

> 原文：<https://betterprogramming.pub/4-uncommon-python-tricks-you-should-learn-2d3a156c10f2>

## 帮助您掌握 Python 的工具

![](img/50af6f1168d7f3478ca58ad2dde2cc5f.png)

由 [Max Nelson](https://unsplash.com/@maxcodes?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 1.多重赋值

当您想给几个变量赋予相同的值时，您经常会看到为每个变量重复赋值语句的代码。

它可能类似于下面的代码，对每个变量使用一个单独的赋值:

```
a = 1
b = 1
c = 1
```

在 Python 中，我们可以简化这一点，并一次给每个变量赋值。

```
a = b = c = 1
# a == 1
# b == 1
# c == 1
```

这样做之后，每个变量都被赋予了链中最右边的值。因为它只取最右边的值，所以我们也可以用一个变量代替`1`。

```
val = 1
a = b = c = val
# a == 1
# b == 1
# c == 1
```

# 2.序列解包

在 Python 中，如果我们有一个序列，比如一个列表，我们可以直接将该序列的元素分配给单个变量，而不必使用循环或单独索引该序列的每个元素。

```
our_list = [1, 2, 3]
a, b, c = our_list
# a == 1
# b == 2
# c == 3
```

这里我们看到`our_list`中的每个元素都被赋给了相应的变量。注意，只有当我们在左侧提供了正确数量的变量时，这才有效——对于`our_list`中的每个元素都必须有一个变量。

## 变量交换

这样做的一个好结果是，我们可以交换变量，而不需要使用临时变量。

```
a, b = (1, 2)
# a == 1
# b == 2a, b = b, a
# a == 2
# b == 1
```

由于`b, a`等同于元组`(b, a)`，我们可以解包这个序列，并像上面所做的那样将其重新分配给`a`和`b`。通过更改右边的元组，我们可以将这些变量重新分配给任何其他值集。

# 3.扩展切片

您可能已经知道如何用`our_list[begin:end]`符号获得序列的一部分，这将创建原始序列的一个子序列。但是使用[扩展切片](https://python-reference.readthedocs.io/en/latest/docs/brackets/slicing.html)可以更进一步，它提供了控制步长的第三个参数。

扩展切片语法是`our_list[begin:end:step]`。

`step`参数可以是正的，也可以是负的。正值将从开头开始，一次跳过`step`个元素，直到到达结尾。

相反，负值将从末尾开始，一次跳过`step`个元素，直到到达开头。

## 反转列表

我们可以使用扩展的 slice 语法来快速反转列表。

```
our_list = [1, 2, 3]
reversed_list = our_list[::-1]
# reversed_list == [3, 2, 1]
```

通过提供-1 的步长，它从`our_list`中的最后一个元素开始，一次后退一个元素，直到到达开头。这将创建一个与原始列表相反的列表。

# 4.所有和任何

Python 提供了两个独特的内置函数，允许我们检查 iterable 中的所有元素是否都是`True`，以及任何*元素是否都是`True`。这些功能被恰当地命名为 [all](https://docs.python.org/3/library/functions.html#all) 和 [any](https://docs.python.org/3/library/functions.html#any) 。*

```
all_true = [True, True, 1, 'hello']
any_true = [0, False, True, '', []]# all(all_true) == True
# any(all_true) == True# all(any_true) == False
# any(any_true) == True
```

注意，在 Python 中，一切都有真值，包括字符串、数字和列表。每种类型都以不同的方式实现这一点。

比如在整数中，任何非零的都是`True`，对于字符串和列表，任何非空的都是`True`。

我希望这有所帮助。感谢阅读！
# 为了在面试中脱颖而出，你应该知道的 6 个 Python 概念

> 原文：<https://betterprogramming.pub/6-python-concepts-you-should-to-stand-out-in-an-interview-e630c4e76b8d>

## 3.专用容器数据类型

![](img/8ae427526ba605580176c95c7f878677.png)

照片由 [Maranda Vandergriff](https://unsplash.com/@mkvandergriff?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/interview?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Python 广泛用于机器学习、数据科学、安全和云计算。
所以你觉得自己已经为 Python 面试做好了准备。但是你知道一些概念会让你在面试中脱颖而出吗？本教程将涵盖以下概念:

*   列举
*   分类的
*   专用容器数据类型
*   列出理解
*   字符串格式
*   Python 调试器

# 1.列举

当遍历一个元素列表时，大多数开发新手会选择使用传统的 for 循环函数；然而，如果你需要给你的潜在雇主留下深刻印象，可以考虑使用 enumerate。枚举执行迭代，而不跟踪每个元素的索引。

考虑如下所示的数字列表。你将如何遍历这个列表？您可能会尝试使用传统的 for 循环，它遍历每个元素。

```
brands = ["Apple", "Samsung", "LG","Huawei", "Xiaomi", "Oppo"]for brand in brands: print(brand)
```

然而，更有效的方法是使用 enumerate()函数。Enumerate 是一个内置的 Python 函数，根据 Python 文档，其定义如下:

> 返回一个枚举对象。 *iterable* 必须是一个序列，一个[迭代器](https://docs.python.org/3/glossary.html#term-iterator)，或者其他一些支持迭代的对象。 [enumerate()](https://docs.python.org/3/library/functions.html#enumerate) 返回的迭代器的 [__next__()](https://docs.python.org/3/library/stdtypes.html#iterator.__next__) 方法返回一个包含计数(从*开始*默认为 0)和迭代 *iterable* 得到的值的元组。

使用 Enumerate 来迭代我们的列表，如下所示。

```
brands = ["Apple", "Samsung", "LG","Huawei", "Xiaomi", "Oppo"]for index,brand  in enumerate(brands): print(index,":",brand)
```

结果将是:

```
0 : Apple
1 : Samsung
2 : LG
3 : Huawei
4 : Xiaomi
5 : Oppo
```

然而，使用 enumerate 要简单得多，也不那么复杂。

# 2.分类的

排序是您应该掌握的另一个 Python 概念。python sorted()函数接受列表、元组、字典或集合，并返回元素的排序列表，而不修改原始序列。举个例子，

```
my_list =[1,4,6,8,9,4,6]
print(sorted(my_list))
```

输出将是:

```
[1, 4, 4, 6, 6, 8, 9]
```

考虑下面的例子，

> 写一个函数，接受两个字符串作为参数，如果它们包含完全相同的字母，则返回 True，否则返回 false。

例如，`str1 = “silent”`和`str2= “listen”` 应该返回`True`，而 `str1 = book`和`str2 = cook` 将返回`False`

**你会如何使用 sorted 解决这个问题？**

你可以用 sorted 来证明你的理解。

```
def anagaram(str1,str2): if sorted(str1) ==sorted(str2): print("This string is an Anagram") else: print("This string is not an Anagram")
```

如果我们用单词`listen`和 silent 调用函数

```
print(anagaram('listen','silent'))
```

它将返回以下输出。

```
This string is an Anagram
```

# 3.专用容器数据类型

Python 引入了集合模块，该模块实现了专门的容器数据类型，为通用内置容器提供了替代方案，例如字典、列表、集合和元组。

专用容器数据类型包括:

*   命名元组()
*   双端队列
*   链式地图
*   计数器
*   有序直接
*   默认字典
*   用户词典
*   用户列表
*   用户字符串

让我们看一个如何使用 OrderedDict 的例子。

普通字典任意遍历键值对，而 OrderedDict 根据它们被插入的顺序进行遍历。如果一个键改变了它的值，顺序将保持不变。

输出将是:

```
name : Peter
age : 27
```

# 4.列出理解

Python 列表理解是一个特性，它允许开发人员编写一行代码来编写 python 功能。适当的列表理解知识会给你一个额外的优势，而不是使用 for 循环。
让我们来看看实现 for 循环与列表理解来找出数字的平方。

列表理解:

*   允许您编写更少的代码
*   会产生更容易理解的代码
*   很快

# 5.字符串格式

**如何在 Python 中格式化字符串？**。Python 3.6 中格式化字符串的一种改进方法是使用 f 字符串。f 弦不容易出错，速度也更快。

根据 Python 文档:

> *格式化字符串文字*或 *f-string* 是以`'f'`或`'F'`为前缀的字符串文字。这些字符串可能包含替换字段，替换字段是由花括号`{}`分隔的表达式。虽然其他字符串总是有一个常量值，但格式化字符串实际上是在运行时计算的表达式。

在 Python3.6 之前有两种格式化字符串的方法，即`% formatting`和`str.format()`。

这两种类型都将输出以下代码:

```
My puppy's name is spooky and he's 24 months old
My puppy's name is spooky and he's 24 months old
```

不推荐使用旧的字符串格式化方式，因为它容易出错，导致代码不可读。
使用 f 字符串的应用程序的一个例子是在定义 Django 模型时添加 Django 模型的字符串表示。

# 6.Python 调试器

调试代码在软件开发的每个阶段都是必须的。调试器允许您查看每段代码如何影响应用程序。幸运的是，Python 引入了一些很酷的调试器，这使得调试变得很容易。从 Python3.7 开始，Python 引入了内置断点()函数，用于暂时停止代码执行。

如果你认为一个程序可能会导致错误，就使用`breakpoint()`。考虑一个简单的将两个数相除的程序。

当您在代码中的任何地方引入断点()函数时，执行将会停止，pdb shell 将会出现，您可以在其中运行常规 Python 代码

输出将是:

```
> /home/earth/Desktop/debug.py(81)debug()
-> result = a / b
(Pdb) print(a/b)
*** ZeroDivisionError: division by zero
(Pdb) print(5/0)
*** ZeroDivisionError: division by zero
(Pdb)
```

[](/how-to-ace-your-first-python-technical-interview-e6e1d16b8347) [## 如何赢得第一次 Python 技术面试

### 在 Python 技术面试中期待什么

better 编程. pub](/how-to-ace-your-first-python-technical-interview-e6e1d16b8347) 

# 结论

这些概念将有助于加深对 Python 的理解，也将有益于您的 Python 工作和项目。如果你喜欢读这篇文章，考虑注册成为[媒体成员](https://essyking.medium.com/membership)。
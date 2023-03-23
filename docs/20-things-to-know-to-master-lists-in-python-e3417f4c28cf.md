# 掌握 Python 列表的 20 件事

> 原文：<https://betterprogramming.pub/20-things-to-know-to-master-lists-in-python-e3417f4c28cf>

## 对 Python 中最常见的集合数据类型有更深入的理解

![](img/9c93f819bbb4ad95404fb6fa75145321.png)

[asoggetti](https://unsplash.com/@asoggetti?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

所有现代编程语言都支持类似数组的数据类型，Python 也不例外。在 Python 中，内置的类似数组的数据类型是*列表*。可以说，列表对象是任何 Python 项目中最常用的集合数据类型。人们可能对此有不同的看法，但我喜欢使用它们，因为有以下三个原因:

*   列表对象是有序的集合，所以我们知道哪些项目在其他项目之前。
*   它们可以灵活地存储任何类型作为它们的元素，可以是整数、浮点数、字符串、自定义类实例或它们的混合！
*   它们是可变的，所以我们可以在适当的时候向列表中添加新的条目，修改条目，或者从列表中删除条目。

考虑到它的广泛应用，了解 list 对象支持的各种功能和操作是非常重要的。在本文中，我将向您展示关于 Python 中的列表您需要知道的 20 件事。

# 1.基本建设

要构造一个列表，可以用方括号将任何元素括起来。或者，您可以使用类构造方法`list()`来包含任何 iterable，这将返回相同项目的列表对象。请参见以下代码片段中的一些示例:

需要注意的一点是，当你在`list()`中包含一个字符串时，它将返回一个由单个字母组成的`list`对象。这是因为 `str`对象被认为是以单个字母作为元素的可迭代对象。

# 2.列表理解

除了基本的构造之外，另一种创建`list`对象的常用方法是使用列表理解。这是用下面的语法创建`list`对象的简洁方法:`[expression for x in iterable]`。让我们来看一些例子，看看它是如何工作的:

# 3.检查空虚

在我们应用特定的动作之前，通常需要检查一个`list`对象是否为空。虽然有多种方法可以检查列表对象的空性，但有些方法比其他方法更受欢迎。

```
>>> **# Create a list**
>>> not_empty_list = [2, 3, 4]
>>> 
>>> **# Check emptiness**
>>> if len(not_empty_list) > 0:
...     print("Method 1: The list is not empty")
... 
Method 1: The list is not empty
>>> if not_empty_list != []:
...     print("Method 2: The list is not empty")
... 
Method 2: The list is not empty
>>> if not_empty_list:
...     print("Method 3: The list is not empty")
... 
Method 3: The list is not empty
```

在这段代码中，我展示了三种检查空性的方法。其中，首选第三种方法。

# 4.索引和反向索引

当我们想要检索一个单独的条目时，我们可以暗示使用索引。当我们从左侧开始计数时，就像我们在大多数其他编程语言中所做的那样，第一个索引是 0，以此类推。此外，Python 中一个方便的特性是它还支持反向索引，最后一个元素的索引为-1，以此类推。有关示例，请参见以下代码:

# 5.限幅

有时，我们需要处理原始列表对象的子序列。一种常见的方法是使用切片来获得一些项目。关于如何对列表进行切片，有几种不同的方法，下面的代码片段列出了一些常见的方法:

# 6.追加/插入项目

列表对象的一个关键特性是它们的可变性。list 对象的一个可变操作是添加新项。有几种方法可以执行此操作。让我们看看下面的一些常见用法的代码:

上面的代码为您提供了三种向列表中添加新项目的方法。`insert()`方法在特定位置添加一个新项，`append()`方法在列表末尾添加一个新项，`extend()`方法在列表末尾添加每个 iterable。

# 7.循环

当我们使用列表对象时，一个常见的任务是迭代列表以执行操作。我们可以在一个`for`循环中直接使用列表，因为列表是可迭代的。我们也可以使用具有高级迭代功能的列表，比如`enumerate()`和`reversed()`。

```
>>> **# Create a list for iteration**
>>> pets = ["dogs", "cats", "hamsters"]
>>> 
>>> **# Regular iteration**
>>> for pet in pets:
...     # do something with each of the pets
...     pass
... 
>>> **# Iteration with enumerate**
>>> for count, pet in enumerate(pets, 1):
...     print(f"Pet # {count}: {pet}")
... 
Pet # 1: dogs
Pet # 2: cats
Pet # 3: hamsters
>>>
>>> **# Iteration with reversed**
>>> for pet in reversed(pets):
...     print(pet)
... 
hamsters
cats
dogs
```

# 8.整理

因为列表对象是一种集合数据类型，所以它们包含多个元素。这意味着我们需要对它们进行排序，将它们按照一定的顺序排列，以便于后续的执行。我们可以使用`sort()`方法或内置的`sorted()`函数方便地做到这一点，后者返回一个新的列表对象，而前者对列表进行排序并返回`None`。除此之外，它们都差不多，我们只关注`sort()`方法。

如您所见，`sort()`函数支持两个关键字参数。对于`key`参数，我们指定了排序所基于的函数。在我们的例子中，我们使用元组的第一个元素对列表进行了排序。对于`reverse`参数，如果我们设置了`True`，我们将指定一个布尔值来对列表进行逆序排序。

# 9.反向列表

在 Python 中反转列表对象也很方便。有几种方法可以做这件事。关于下面的代码，需要注意几点。

*   `reversed()`方法返回一个 iterable，而不是一个 list，所以您必须使用`list()`从 iterable 生成一个 list。
*   `reverse()`方法就地反转列表，因此它返回`None`。
*   除了列表对象之外，`[::-1]`方法还可以用来反转其他序列，比如字符串和元组。

# 10.检查元素是否存在

作为集合类型，某些操作仅在特定项目包含在列表中时才相关。为了检查特定项目的存在，我们可以利用`in`关键字操作。请看下面一些琐碎的例子。需要注意的一点是，我们可以结合`not`关键字来否定检查。

# 11.更新项目

正如我们多次提到的，列表是可变的，这意味着我们可以更新特定的项。这些项目可以就地更新，而不会影响其他项目。可以对单个项目或一系列项目进行更新。值得注意的是，对于后者，更新项的数量不必与原始子序列的数量相匹配。

# 12.删除项目

除了添加和更新项目，我们还可以从列表中删除项目。Python 为此操作提供了几种方法，如下面的代码片段所示。有几件事需要注意:

*   `remove()`方法删除第一个匹配的元素并保留剩余的元素。
*   默认情况下,`pop()`方法删除最后一个元素。您可以选择设置一个特定的索引，在该索引处元素将被移除。
*   `pop()`方法返回被移除的项目，因此在需要操作被移除的项目时非常有用。
*   `clear()`方法删除所有项目。

# 13.复制列表

我们可以制作现有列表的副本，这样我们就可以在保持原始列表不变的情况下操作新列表。下面的代码片段展示了在 Python 中复制列表的常用方法。有一点需要注意的是，浅抄和深抄是有区别的，这是超出目前讨论的话题。如果有兴趣，你可以在这里找到一些关于这个[的信息](https://medium.com/better-programming/use-id-to-understand-6-key-concepts-in-python-73e0bbd461ec)。

# 14.清点特定项目

我们有一个记录列表，我们想知道一个特定项目有多少个记录。为此，我们可以简单地使用`count()`方法。对于每个元素的更高级的计数，我们可以使用在`collections`模块中可用的`Counter()`方法。值得注意的是，我们可以将`Counter()`方法的输出转换成一个字典，用于任何额外的操作。

# 15.连接列表

当我们处理多个列表时，我们需要连接列表来创建一个新的列表。下面的代码片段向您展示了一些常见的方法，这些方法应该是不言自明的。

# 16.随机选择项目

对于一个项目列表(例如数字、对象)，我们需要根据特定目的的顺序对项目进行随机化，例如用相同的项目列表评估算法的排列。让我们看看如何用一个列表做到这一点。

# 17.获得随机物品

想象一下，你在一个聚会上，我们有一个门奖。我们想为获胜者随机抽取一张奖券。本质上，它就像是从一系列数字中获得一个随机项。让我们看看代码:

# 18.查找项目的索引

我们有一个条目列表，我们想知道列表中特定条目的索引。如果您使用相似数据结构的多个列表，此功能会很有用。知道一个列表中一个项目的索引将被用来获得另一个列表中相应的项目。

# 19.检查列表之间的关系

当您处理具有重叠元素的多个列表时，有时需要知道不同列表之间的关系。在这种情况下，我们可以利用 set 对象的唯一性特性。更相关的是，set 对象有`issuperset()`和`issubset()`方法，它们允许我们检查两个集合数据类型之间的嵌套关系。

## 20.哈希能力

Hashability 是一个稍微高级一点的话题。可散列性意味着 Python 对象在其生命周期中有一个散列值。您可能已经看到 Python 字典要求它们的键是可散列的，集合的元素也是如此。值得注意的是，列表是*而不是*可散列的，所以我们不应该试图将列表用作字典的键或集合的元素。

如果你真的想使用一个元素序列作为字典的键，那就使用元组，它在设计上是可散列的。

# 在你走之前

在本文中，我们回顾了 Python 中最常用的列表方法和操作。我希望您对列表对象有了更好的理解，现在可以在您的 Python 项目中正确地使用它们。以下是其他几篇相关文章。请随意看一看。

感谢阅读！

[](https://medium.com/better-programming/30-simple-tricks-to-level-up-your-python-coding-5b625c15b79a) [## 提升 Python 编码水平的 30 个简单技巧

### 更好的 Python

medium.com](https://medium.com/better-programming/30-simple-tricks-to-level-up-your-python-coding-5b625c15b79a) [](https://medium.com/better-programming/use-id-to-understand-6-key-concepts-in-python-73e0bbd461ec) [## 使用 id()理解 Python 中的 6 个关键概念

### 发现引擎盖下的机械

medium.com](https://medium.com/better-programming/use-id-to-understand-6-key-concepts-in-python-73e0bbd461ec)
# Python 中使用字典的 5 种高级操作

> 原文：<https://betterprogramming.pub/5-advanced-operations-using-dictionaries-in-python-5f8edb4719fa>

## 提升在项目中使用词典的技能

![](img/9e52866fc133f297c9158c18db4a7c4f.png)

Alberto Restifo 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Python 灵活性的一个表现是各种数据结构的可用性，包括存储单个值的数据结构(例如整数和字符串)和存储多个值的数据结构(例如列表和元组)。对于后者，我们可以称之为容器数据类型。其中，字典是我的最爱之一。

本质上，字典是键值对的集合。对于任何给定的字典，键必须是不同的和可散列的，并且这些键的可散列性允许搜索和更新具有 *O(1)* 时间复杂度。此外，对于哪些数据类型可以存储为字典值，没有特别的要求。字典的基本用法可以总结如下:

我们大多数人应该非常熟悉字典的这些基本操作。在这些基础上，我想在本文中介绍一些字典的中级或高级操作。

# 1.dict()构造函数

大多数时候，我们习惯于创建一个用花括号括起键值对的`dict`对象，就像上一节中显示的例子。使用花括号创建词典被称为文字法。但是，Python 提供了一个非常强大的构造器方法，用于在不同场景下创建字典。

*   从元组列表中创建一个`dict`对象，每个元组存储一对键和值，如下所示:

```
>>> **# Create a dict object from tuples**
>>> dict_tuples = dict([("a", 0), ("b", 1), ("c", 2)])
>>> dict_tuples
{'a': 0, 'b': 1, 'c': 2}
```

*   使用`zip()`函数压缩两个可重复项，创建一个`dict` 对象。一个 iterable 用于键，另一个用于值:

```
>>> **# Create a dict object from two iterables**
>>> dict_keys = ["a", "b", "c"]
>>> dict_values = [0, 1, 2]
>>> dict_zipped = dict(zip(dict_keys, dict_values))
>>> dict_zipped
{'a': 0, 'b': 1, 'c': 2}
```

*   使用赋值操作符(`=`)从显式键值对创建一个`dict`对象:

```
>>> **# Create a dict object from key-value assignments**
>>> dict_assigned = dict(a=0, b=1, c=2)
>>> dict_assigned
{'a': 0, 'b': 1, 'c': 2}
```

# 2.词典理解

除了使用`dict()`构造函数方法创建字典之外，另一种创建`dict`对象的简便方法是使用字典理解技术。

你们中的许多人可能听说过列表理解，但是你知道我们也可以使用理解技术来创建字典吗？很直白。让我们看一个例子:

```
>>> **# Use dictionary comprehension**
>>> squares = {x: x*x for x in range(5)}
>>> squares
{0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

如上所示，我们使用字典理解创建了一个`dict`对象，它具有以下基本语法:`{key_expr: value_expr for item in iterable}`。是不是又好看又轻松？

如果我们需要从 iterable 中过滤一些元素，通过实现一些条件，我们可以有一个稍微复杂一点的用例，如下例所示:

```
>>> **# Create a list of integers used in a dict comprehension**
>>> integers = [1, 2, 3, 4, 5]
>>> odds_squares = {x: x*x for x in integers if x%2 == 1}
>>> odds_squares
{1: 1, 3: 9, 5: 25}
```

# 3.检索值

看到这一节的标题你可能会笑。“你在跟我开玩笑吗？我知道如何检索值，就像你在开始时展示的那样。”关于使用方括号来检索特定键值，您肯定是对的。但是，您是否经历过可能导致您的程序崩溃的事情？

```
>>> **# Create a dictionary for value retrieving**
>>> student = {"name": "John", "student_id": 73802}
>>> student["gender"]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'gender'
```

我知道使用方括号来获取字典的值是许多编程语言中最传统的方式，但是在 Python 中我们也有选择。为了防止可能的`KeyError`，一个繁琐的方法是在我们决定是否检索它的值之前，先检查字典对象是否有键。大概是这样的:

```
>>> **# Use the one-line if-else**
>>> name = student["name"] if "name" in student else "Unknown"
>>> name
'John'
>>> gender = student["gender"] if "gender" in student else "Unknown"
>>> gender
'Unknown'
```

不使用这个三元表达式，我们还有其他选择。我们实际上可以使用`get()`方法，如果字典没有指定的键，该方法允许我们设置一个默认值，这样我们可以防止自己陷入`KeyError`问题。下面来看看它的用法:

```
>>> **# Use the get() method**
>>> student_id = student.get("student_id", -1)
>>> student_id
73802
>>> grade = student.get("grade", 999)
>>> grade
999
```

# 4.迭代次数

字典是可迭代的，这意味着我们可以遍历字典中的元素来执行特定的操作。然而，字典存储键值对，因此给了我们更多的迭代方法。让我们看看这些不同的方式。为了简单起见，下面的例子都使用相同的字典:

```
>>> **# Create a dictionary for iterations**
>>> currencies = {"America": "USD", "China": "CNY", "Britain": "GBP"}
```

*   为了迭代这些键，我们可以使用`keys()`函数。需要注意的一点是，如果你直接迭代字典，你基本上是在键上迭代。前者只是调用`keys()`函数的语法糖。如下所示，这两种方式生成相同的输出:

```
>>> **# Iteration over the keys**
>>> for item in currencies:
...     print(item)
... 
America
China
Britain
>>> for item in currencies.keys():
...     print(item)
... 
America
China
Britain
```

*   为了迭代这些值，我们可以使用`values()`函数。用法非常简单，下面是一个简单的例子:

```
>>> **# Iteration over the values**
>>> for item in currencies.values():
...     print(item)
... 
USD
CNY
GBP
```

*   为了迭代键值对，我们可以使用`items()`函数。这将生成一个元组列表，每个元组存储一个键及其对应的值。见下文:

```
>>> **# Iteration over the items**
>>> for key, value in currencies.items():
...     print(f"Key: {key}; Value: {value}")
... 
Key: America; Value: USD
Key: China; Value: CNY
Key: Britain; Value: GBP
```

在上面的迭代中需要注意的一点是，随着 Python 的发展，字典已经变得有序了。因此，当元素的顺序很重要时，您仍然可以使用字典。然而，问题是在你删除一个键-值对之后，如果你要把它添加回来，新的插入将会在末尾，并且顺序将会改变。

# 5.合并词典

有时候，我们从多个字典开始，想把它们合并成一个字典。当然，最繁琐但最直接的方法是创建一个新字典，遍历现有字典，并将这些键值对添加到新字典中。

然而，在 Python 中有更好的合并字典的方法。出于演示的目的，我将只使用两个字典，并将它们合并为一个字典。下面给出了例子。

*   第一种方法是使用字典的`update()`方法。基本上，这个方法将获取另一个字典并更新调用字典(即调用函数的字典)。当存在重叠键时，调用字典的值将被覆盖，如下所示:

```
>>> **# Merge dictionaries using update()**
>>> d0 = {"a": 0, "b": 1}
>>> d1 = {"b": 2, "c": 3}
>>> d0.update(d1)
>>> d0
{'a': 0, 'b': 2, 'c': 3}
```

*   第二种方法是使用双星号(`**`)来解包字典。与上面的例子类似，如果有重叠的键，后面解包的键将影响最终值。下面让我们看看在合并字典时解包是如何使用的:

```
>>> **# Merge dictionaries using unpacking ****
>>> dict0 = {0: "a", 1: "b"}
>>> dict1 = {1: "z", 2: "c"}
>>> dict2 = {**dict0, **dict1}
>>> dict2
{0: 'a', 1: 'z', 2: 'c'}
```

*   第三种方法是使用`dict()`构造函数，它可以接受一个字典和另一个字典的解包的键值对。用法非常简单。下面我们来看看它是如何工作的:

```
>>> **# Merge dictionaries using dict()**
>>> d0 = {"a": 0, "b": 1}
>>> d1 = {"b": 2, "c": 3}
>>> d2 = dict(d0, **d1)
>>> d2
{'a': 0, 'b': 2, 'c': 3}
```

# 结论

在本文中，我们回顾了五种使用字典的中级或高级操作。在涉及字典的编码中，这些技巧会非常方便。

感谢阅读这篇文章。以下是与当前讨论相关的其他几篇文章，供您参考。

[](https://medium.com/swlh/how-to-pack-and-unpack-data-in-python-tuples-and-dictionaries-55d218c65674) [## 如何在 Python 中打包和解包数据—元组和字典

### 了解神奇的星号(*)

medium.com](https://medium.com/swlh/how-to-pack-and-unpack-data-in-python-tuples-and-dictionaries-55d218c65674) [](https://medium.com/better-programming/dictionary-merging-and-updating-in-python-3-9-4ac67c667ce) [## Python 3.9 中的字典合并和更新

### 快速浏览 Python 3.9 的新 merge |和 update |=操作符

medium.com](https://medium.com/better-programming/dictionary-merging-and-updating-in-python-3-9-4ac67c667ce)
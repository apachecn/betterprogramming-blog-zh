# 访问 Python 字典值的 3 种方法

> 原文：<https://betterprogramming.pub/3-ways-to-access-python-dictionary-values-e353d0830931>

## 利用 Python 所提供的灵活性

![](img/ecbfe796faa07b43088a3f9338cf438c.png)

照片由 [Sigmund](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

字典是我最喜欢的 Python 数据类型之一。主要原因是它们存储键值对。本质上，它们可以用来保存两类信息，并以匹配的顺序保存相关的项目。

例如，假设我们有下面的字典来存储一些学生的成绩:

```
grades = {"Anne": 98, "John": 95, "Bella": 97}
```

这个字典包含以下两组信息:学生(姓名作为关键字)和成绩。此外，每个学生都与自己的年级相关联。

有多种方法可以检索存储在字典中的值。在这篇文章中，我们将谈论它们。更重要的是，我们将讨论最佳实践。

# 下标符号

获取学生成绩的第一种方法是使用下标表示法，如下所示:

```
>>> grades["Anne"]
98
>>> grades["John"]
95
```

这种方法的主要优点是非常简单。如果您在其他语言中使用过字典或类似字典的数据结构，比如 JavaScript、Kotlin 或 Swift，那么您可能对这种方法非常熟悉。因此，当您需要访问字典中的条目时，使用这种方法是非常自然的。

但是，有一个潜在的问题，如下所示:

```
>>> grades["Zoe"]
Traceback (most recent call last):
  File "<input>", line 1, in <module>
KeyError: 'Zoe'
```

当您试图访问字典中不存在的键时，您会遇到`KeyError`异常。当它没有被处理时，你的程序会崩溃。

最佳实践:对你创建的字典或者那些你对关键字有把握的字典使用下标符号。

# get 方法

考虑到下标符号的上述问题，有些人可能尝试了以下解决方案:

```
name_to_check = "some name"
if name_to_check in grades:
    grade = grades[name_to_check]
else:
    grade = "A default value"
```

这个解决方案当然有助于避免`KeyError`异常，但是有点麻烦。在这种情况下，更好的解决方案是使用字典的`get`方法。为了使用`get`方法，我们指定了键的名称和一个缺省值，如果这个键不在字典中的话。

下面的代码片段展示了一些使用`get`的例子:

```
>>> grades.get("Anne")
98
>>> grades.get("Zoe", 0)
0
>>> grades.get("Zoe")
None (None is automatically hidden in an interactive console)
```

如上所示，当指定的键不在字典中时，`get`方法确实提供了不引发`KeyError`异常的优势。更重要的是，它允许您在键不存在时设置一个合适的默认值。理论上，无论何时从字典中检索值，都可以使用`get`方法，但我更喜欢尽可能使用下标符号，因为我觉得它们可读性更强。

然而，在某些情况下,`get`方法会非常方便。其中一个场景是当我需要处理函数定义中的变量关键字参数时。假设您正在构建一个 Python 包，它具有以下 API 函数:

如上所示，这个函数非常灵活，除了两个位置参数之外，还支持许多不同的关键字参数。为了清楚起见，我不想在函数定义中列出所有这些关键字参数。毕竟，当 API 用户忽略这些关键字参数时，我知道我想要设置什么默认值。

*最佳实践:对您不能直接控制的字典使用* `*get*` *方法，比如在 API 函数调用中使用的字典。您将知道在这些情况下什么默认值是合适的。*

# setdefault 方法

当人们谈论`get`方法的替代方案时，有些人可能会提到`setdefault`方法。这个方法非常类似于`get`方法，因为它也接受两个参数:键(必需的)和默认值(可选的)作为后备。当关键字不在字典中时，将返回回退值，而当关键字在字典中时，将返回关键字的值。请注意以下特征:

```
>>> grades.setdefault("Anne")
98
>>> grades.setdefault("Danny", 0)
0
>>> grades.setdefault("Ashley")
None (None is automatically hidden in an interactive console)
```

上面的代码片段向您展示了`setdefault`方法的行为类似于`get`方法的行为。然而，`setdefault`与`get`的不同之处在于，当您调用`setdefault`时，当键不在字典中时，会发生一个额外的操作(`dict[key] = default_value`)。观察下面的效果:

```
>>> grades
{'Anne': 98, 'John': 95, 'Bella': 97, 'Danny': 0, 'Ashley': None}
```

我们之前用键`“Anne”`、`“Danny”`和`“Ashley”`调用了`setdefault`。因为`“Danny”`和`“Ashley”`在我们调用它们时不在字典中，所以下面两个操作在幕后发生:

```
grades["Danny"] = 0
grades["Ashley"] = None
```

通常，我不推荐使用`setdefault`方法。不仅名字容易混淆——我们通常不期望通过调用涉及设置值的方法来返回东西——而且还有一个许多人可能不知道的隐式操作(即，如果键不存在，则设置默认值)。

*最佳实践:避免使用* `*setdefault*` *方法。很少需要。*

# 默认选项

在前面的章节中，我们关注了适用于`dict`类的解决方案。然而，除了`dict`类，还有另一种叫做`defaultdict`的字典类型，它是`dict`的子类。它在`collections`模块中可用。一个例子可以帮助我们理解这个类:

```
>>> from collections import defaultdict
>>> grades_default = defaultdict(int, grades)
>>> grades_default
defaultdict(<class 'int'>, {'Anne': 98, 'John': 95, 'Bella': 97, 'Danny': 0, 'Ashley': None})
```

与`dict`类构造函数不同，`defaultdict`有一个名为`default_factory`的参数，这是一个零参数函数，当您访问一个不在字典中的键时会调用这个函数。在我们的示例中，当学生不在字典中时，当您访问学生的成绩时，调用`int`来创建零成绩(因为`int()`计算为零)。请注意以下特征:

```
>>> grades_default["Lily"]
0
```

需要注意的一点是，`defaultdict`的操作类似于`setdefault`方法，因为由`default_factory`创建的值将被设置为字典中没有的键。例如，在访问`“Lily”`的等级后，字典得到更新，如下所示:

```
>>> grades_default
defaultdict(<class 'int'>, {'Anne': 98, 'John': 95, 'Bella': 97, 'Danny': 0, 'Ashley': None, 'Lily': 0})
```

鉴于`setdefault`方法和`defaultdict`类之间的相似性，它们可以有以下相似的用法。假设我们想要创建一个 dictionary 对象，该对象使用学生姓名的首字母作为键，而具有相同首字母的姓名与每个键对应:

```
**# Use the setdefault with a dict object**
students_by_initial0 = {}
for name in grades.keys():
    group = students_by_initial0.setdefault(name[0], [])
    group.append(name)
```

上面的代码片段使用了带有`dict`对象的`setdefault`方法。让我们看看如何用`defaultdict`类实现同样的功能:

```
# Use the defaultdict class
students_by_initial1 = defaultdict(list)
for name in grades.keys():
    students_by_initial1[name[0]].append(name)
```

可以想象，如果相同的功能由一个常规的`dict`类实现，中间有一个检查键是否存在的步骤，那么它会更复杂，如下所示:

*最佳实践:当您必须迭代地访问 dictionary 对象的条目时，使用* `*defaultdict*` *类，例如本例中的分组。此外，当缺少一个键时，您应该知道正确的默认值。否则，可以使用* `*dict*` *类型。*

# 结论

在本文中，我们回顾了访问字典中的值的不同方法，特别关注处理字典中可能不存在键的情况。本质上，这些方法都有自己的使用场景。因此，选择最佳方案实际上取决于您的用例。
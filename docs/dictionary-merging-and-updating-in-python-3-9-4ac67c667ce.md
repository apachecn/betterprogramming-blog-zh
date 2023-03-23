# Python 3.9 中的字典合并和更新

> 原文：<https://betterprogramming.pub/dictionary-merging-and-updating-in-python-3-9-4ac67c667ce>

## 快速浏览 Python 3.9 的新 merge |和 update |=操作符

![](img/19db0af98301bed54e12008ec55074ce.png)

[皮斯特亨](https://unsplash.com/@pisitheng?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/dictionary?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

Python 3.9 正在积极开发中，计划于今年 10 月发布。2 月 26 日，开发团队发布了它的 alpha 4 版本。与几乎所有 Python 程序员相关的一个新特性是引入了新的 merge `(|)`和 update `(|=)`操作符。

让我们快速浏览一下这篇文章中的内容。

# 字典

字典，俗称`dict`，是 Python 中最重要的内置数据类型之一。这种数据类型是一个大小灵活的键-值对集合，由于它的散列实现，它最出名的是具有固定的数据查找时间。

以下是一些常见的用法:

```
# Declare a dict
student = {'name': 'John', 'age': 14}# Get a value
age = student['age']
# age is 14# Update a value
student['age'] = 15
# student becomes {'name': 'John', 'age': 15}# Insert a key-value pair
student['score'] = 'A'
# student becomes {'name': 'John', 'age': 15, 'score': 'A'}
```

# 合并字典——老方法

有时，我们需要合并两个字典进行进一步的处理。在 3.9 正式发布之前，我们有几种方法可以做到这一点。假设我们有两个`dict`:T4 和 T5。我们想要创建一个名为`d3`的新`dict`，由`d1`和`d2`组成。为了说明如果在合并的`dict`之间有一些重叠的键，我们应该做什么，我们有另一个`dict` `d2a`，它有一个与`d1`重叠的键。

```
# two dicts to start with
d1 = {'a': 1, 'b': 2}
d2 = {'c': 3, 'd': 4}
d2a = {'a': 10, 'c': 3, 'd': 4}# target dict
d3 = {'a': 1, 'b': 2, 'c': 3, 'd': 4}
```

## 使用 update()方法

第一种方法是使用`dict`的方法`update()`。下面的代码片段向您展示了如何做到这一点。注意，我们必须首先创建一个`d1`的副本，因为`update()`函数将修改原始的`dict`。

```
# create a copy of d1, as update() modifies the dict in-place
d3 = d1.copy()
# d3 is {'a': 1, 'b': 2}# update the d3 with d2
d3.update(d2)
# d3 now is {'a': 1, 'b': 2, 'c': 3, 'd': 4}
```

当存在重叠的键时，我们必须更加谨慎地考虑要保留哪些值。正如您在下面看到的，在`update()`方法中作为参数传递的`dict`将通过拥有重叠键(即`‘a’`)的值(即 10)来“赢得”游戏。

```
d3 = d1.copy()
d3.update(d2a)
# d3 now is {'a': 10, 'b': 2, 'c': 3, 'd': 4}
# This is not the way that we wantd3 = d2a.copy()
d3.update(d1)
# d3 now is {'a': 1, 'c': 3, 'd': 4, 'b': 2}
# This is the way that we want
```

## 打开字典包装

第二种方法是使用字典的解包。与上面的方法类似，当有重叠的键时，“最后看到的获胜”。

```
# unpacking
d3 = {**d1, **d2a}
# d3 is {'a': 10, 'b': 2, 'c': 3, 'd': 4}
# Not rightd3 = {**d2a, **d1}
# d3 is {'a': 1, 'c': 3, 'd': 4, 'b': 2}
# Good
```

## 使用**字典** ( *可重复*、** *夸格*)

用 Python 创建 dict 的一种方法是使用`dict(iterable, **kwarg)` 方法。与当前主题特别相关的是，当`iterable`是一个`dict`时，将用相同的键-值对创建一个新的`dict`。对于关键字参数，我们可以传递另一个`dict`，这样它会将键值对添加到将要创建的`dict`中。请注意，这个关键字参数`dict`将使用相同的键替换该值——类似于“上次看到的获胜”请看下面的例子。

```
d3 = dict(d1, **d2)
# d3 is {'a': 1, 'b': 2, 'c': 3, 'd': 4}
# Good, it's what we wantd3 = dict(d1, **d2a)
# d3 is {'a': 10, 'b': 2, 'c': 3, 'd': 4}
# Not right, 'a' value got replaced
```

需要注意的一点是，只有当关键字参数`dict`将字符串作为它们的键时，这个方法才有效。如下图所示，使用`int`作为键的`dict`无法工作。

```
>>> dict({'a': 1}, **{2: 3})
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: keywords must be strings
>>> dict({'a': 1}, **{'2': 3})
{'a': 1, '2': 3}
```

# 合并词典—新功能

在最新发布的 Python 3.9.0a4 中，我们可以非常方便地使用合并运算符|来合并两个`dict`。下面给出一个例子。您可能已经注意到，当这两个`dict`之间有重叠的键时，最后看到的键会胜出，这种行为与我们前面看到的一致，例如`update()`方法。

```
# use the merging operator |
d3 = d1 | d2
# d3 is now {'a': 1, 'b': 2, 'c': 3, 'd': 4}
# goodd3 = d1 | d2a
# d3 is now {'a': 10, 'b': 2, 'c': 3, 'd': 4}
# not good
```

与这个合并操作符相关的是扩充赋值版本，它就地操作(即更新左侧`dict`)。本质上，其功能与`update()`方法相同。下面的代码片段向您展示了它的用法:

```
# Create a copy for d1
d3 = d1.copy()# Use the augmented assignment of the merge operator
d3 |= d2
# d3 now is {'a': 1, 'b': 2, 'c': 3, 'd': 4}
# goodd3 |= d2a
# d3 now is {'a': 10, 'b': 2, 'c': 3, 'd': 4}
# not good
```

# 结束语

本文回顾了 Python 3.9 中合并和更新字典的新特性。在几个模块中还有其他几个新的更新/改进，例如`asyncio`、`math`和`os`模块。详见[官网](https://docs.python.org/3.9/whatsnew/3.9.html)。我迫不及待地想在 10 月份的正式发布中一探究竟！

你准备好迎接 Python 3.9 了吗？敬请期待它正式发布时对其新功能的评论！
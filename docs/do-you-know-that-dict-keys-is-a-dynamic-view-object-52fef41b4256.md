# 你知道 dict.keys()是一个动态视图对象吗？

> 原文：<https://betterprogramming.pub/do-you-know-that-dict-keys-is-a-dynamic-view-object-52fef41b4256>

## dict.values()和 dict.items()也是如此。了解它们是什么

![](img/33f56ae9daea99c01b11f2d9a6afa66e.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/learn?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在我们的 Python 项目中，几乎到处都使用字典，尤其是内置的`dict`数据类型。

作为一种基本的数据结构，它通过实现一个允许高效插入、删除和查找的哈希表，将其元素存储为键值对。一些常见的用法如下所示。

字典的基本用法

# keys()、values()和 items()方法

迭代是我们对一个`dict`和其他可迭代对象做的标准工作。从`dict`生成可迭代的三种最常见的方法是使用内置函数`keys()`、`values()`和`items()`。

*   `keys()`方法返回一个类似集合的`dict_keys`类型的视图对象，它由调用`dict`的所有键组成，这些键应该是惟一的并且是可散列的。

Dictionary keys()方法

*   `values()`方法返回一个`dict_values`类型的视图对象，它由调用`dict`的所有值组成。该视图对象可以有重复的值，因为`dict`的值不必是唯一的。

字典值()方法

*   `items()`方法返回一个`dict_items`类型的视图对象，它由`calling`字典的所有键值对组成。

Dictionary items()方法

所有这些方法都可以用来迭代一个`dict`。让我们在下面的例子中看看它们各自在迭代中的用法。

使用`keys()`、值()和项目()进行迭代

# 视图对象

具有一些数据库知识的人可能对视图的概念很熟悉，维基百科对视图的定义如下。

> “在数据库中，**视图**是对数据进行*存储*查询的结果集，数据库用户可以对其进行查询，就像在持久数据库集合对象中一样。
> …
> 作为一个结果集，它是一个虚拟表，当请求访问那个视图时，从数据库中的数据动态计算或整理而来。应用于相关*基础表*中数据的更改将反映在视图后续调用中显示的数据中。

使用`keys()`、`values()`和`items()`方法获得的字典视图对象类似于数据库中的视图。

在下面的例子中，你可以看到我们使用`keys()`方法创建了一个名为`letters_keys`的视图对象。

在通过插入另一个键-值对来更新 dict 之后，我们可以看到视图`letter_keys`同时得到更新。这个视图是相同的对象，并且始终具有相同的内存地址。

字典视图对象

除了在迭代中使用这些视图对象以及它们的动态更新特性之外，如上所示，还有三点需要注意。

1.  这些视图对象不能被索引。虽然它们看起来像列表或集合，但它们不支持索引。所以，当你这样做的时候，如果你看到相关的错误，不要感到惊讶。
2.  成员测试可用于所有这三种视图对象类型，即`dict_keys`、`dict_values`和`dict_items`。因此，我们可以方便地检查`dict`是否有特定的键、值或键值对。
3.  `dict_keys`类型支持一些类似集合的操作。这是因为`dict`中的键是唯一且可散列的，这类似于集合中的项目是唯一且可散列的。

字典视图对象的附加特征

# 外卖食品

在本文中，我们了解到从`dict`的`keys()`、`values()`和`items()`方法返回的值是字典视图对象。以下是一些关键要点。

*   它们是可迭代的，因此可用于迭代。
*   它们是动态的，会随着字典的更新而更新。
*   我们不能使用索引来检索视图对象的特定项目。
*   视图对象支持成员测试。

感谢阅读！
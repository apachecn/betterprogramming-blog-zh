# Python 中使用 Lambdas 和自定义函数的高级排序

> 原文：<https://betterprogramming.pub/advanced-sorting-in-python-using-lambdas-and-custom-functions-410b5780fb07>

## 对非基本数据类型(如 int、str)的元素列表进行排序

![](img/bf80c4bfbfc1d49d8d56e3617bde0c05.png)

马丁·桑切斯在 [Unsplash](https://unsplash.com/s/photos/small-to-big?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 基本排序

说到排序，使用最多的 Python 函数是`sorted()`和`sort()`。

尽管在用法、内存使用和操作速度上有一些不同，正如在以前的[媒体文章](https://blog.usejournal.com/list-sort-vs-sorted-list-aab92c00e17)中所讨论的，这两个函数都可以用来对`list`进行排序。下面是一个简单的例子。

基本排序示例

在上面的例子中，对于那些不太熟悉`sorted()`和`sort()`功能的人来说，有一些事情需要强调。

*   `sort()`函数实际上是`list`数据类型的实例方法，所以用法是`list.sort()`。
*   `sort()`的分类操作到位并返回`None`。因此，如果您通过运行`type(numbers.sort())`来检查类型，输出将是`NoneType`。
*   与`sort()`函数不同，`sorted()`函数具有更好的灵活性，因为它允许参数是一个`list`或任何其他`iterable`对象，就像示例中所示的`tuple`。
*   对于`sorted()`函数，返回值是一个`list`，即使我们传入一个像`tuple`这样的非列表数据。
*   默认的排序顺序是升序。如果我们指定`reverse=True`，排序顺序将是降序。

从本质上来说，`sorted()`和`sort()`函数在列表排序时是可以比较的，其中`sorted()`函数具有更好的灵活性。因此，在下面的演示中，我们将只使用`sorted()`函数。

# 按关键字排序

除了对`int`和`str`的基本排序操作之外，如果我们想要对字典的`list`进行排序，比如下面的`activities`，我们还能做什么呢？

词典列表

如果我们对变量`activities`进行排序，将会出现以下错误，因为解释器不知道如何比较`dict`的实例。那我们该怎么办？

```
TypeError: ‘<’ not supported between instances of ‘dict’ and ‘dict’
```

兰姆达斯来救援了！回想一下，Python 中的 lambda 只是一个匿名函数，它有一个或多个参数，但只有一个表达式。

在下面的例子中，我们创建了一个 lambda 并将其赋给变量`add_ten`。由于 lambda 是一个函数，赋值变量`add_ten`也是一个函数，因此，我们用括号将其称为常规函数。

我们对 lambda 如何工作有了一些基本的概念，现在我们可以看看如何使用它来对字典进行排序。

对词典列表进行排序

如上例所示，我们创建了一个 lambda，并将其作为`key`参数传递。现在，解释器不抱怨`dict` s 的`list`不能排序了。万岁！

实际上，如果我们想让`activities`按多个键排序，我们可以写一个稍微复杂一点的 lambda。

按日期和活动排序

如上图所示，列表现在按`day`和`activity`排序。使用如下所示的`itemgetter()`功能可以达到相同的效果。

实际上，`operator`模块有额外的操作，比如`attrgetter()`，在排序自定义对象时很方便。

```
from operator import itemgettersorted_activities = sorted(activities, key=itemgetter('day', 'activity'), reverse=True)
```

从上面的例子中，您可能已经注意到，对于`key`参数，我们可以简单地传入一个函数，这为我们定制排序提供了更多的灵活性。这里有一个例子。

按自定义函数排序

我们有一个名为`activity_sorting`的函数，它被用作`sorted()`函数的`key`。从输出中，您可以看出`activities`变量现在被`activity`排序了。

# 结论

本教程介绍了如何使用 lambdas 和自定义函数对 Python 中的字典列表进行排序。这些方法也可以应用于自定义对象列表。

如果排序`key`只涉及一个表达式，你可能想考虑使用 lambdas，而不用在`sorted()`或`sort()`函数之外编写一个完整的函数。
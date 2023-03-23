# JavaScript 中组合数组的 3 种不同方式

> 原文：<https://betterprogramming.pub/3-different-ways-to-combine-arrays-in-javascript-b273c9225e0d>

![](img/c4260466d8a326b33193fdc57d58b269.png)

约书亚·阿拉贡在 [Unsplash](https://unsplash.com/search/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在这篇文章中，我将讨论组合两个或多个数组的不同方法。

让我们开始吧。

我将使用 [Visual Studio 代码](https://code.visualstudio.com/)编辑器。

# 1.Concat()

最基本的方法是使用`concat()`方法并连接两个不同的数组。

很简单。您必须定义两个数组，然后像下面这样组合它们。

记住顺序很重要！

代码预览

使用`concat()`的另一种方法是在一个空数组中连接两个或更多的数组:

# 2.使用 ES6 中的…(展开运算符)作为快捷方式

第二种方法就像一条捷径。你必须使用`…`(三个点)，或者扩展操作符，将两个或多个数组的值存储在不同的数组中。

它仅在 ES6 版本中可用，是组合阵列的有效方式。这是我个人最喜欢的。

如你所见，顺序在这里也很重要。

# 3.使用推送合并数组

您还可以使用`push`方法来组合不同的数组，如下所示。

如你所见，首先声明一个空数组，然后使用`push`和`…`，数组的内容将被复制到所需的数组中。

记住，如果你不使用`…` (spread operator)，整个数组都会被推送，你会得到一个嵌套数组叫做一个*二维数组*。我们今天不会深入讨论这个问题。

仅此而已！感谢阅读。
# 如何在 JavaScript 中合并深度嵌套的对象

> 原文：<https://betterprogramming.pub/how-to-merge-deeply-nested-objects-in-javascript-27e12107480e>

## 正确合并嵌套对象的方法

![](img/c653b678051c10e500025724c226ce47.png)

照片由[émile Perron](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在很多情况下，我们需要合并两个嵌套很深的对象。

在本文中，我们将看到如何正确地处理这个问题。

比方说，我们有一个这样的`user`对象:

我们想用新值更新这个对象。例如，当更新他们的用户配置文件时，用户输入新的值，因此我们需要将以前的对象与新的更新对象合并。

如果用户只更新一些值，我们将得到这样一个对象:

如您所见，`user`对象有一个`location`属性，其中包含了`city`和`country`属性。但是在`updates`对象中，我们没有`country`属性，那么如何合并这些对象呢？

让我们尝试使用一些已知的方法。

# 使用 Object.assign

正如您所看到的，来自`update`对象的`location`属性已经完全替换了之前的`location`对象，因此`country`属性丢失了。

这是因为`Object.assign`做的是[浅合并](https://codepen.io/myogeshchavan97/pen/ZEbmZoq?editors=0011)而不是深合并。浅合并意味着它将只在第一级而不是嵌套级合并属性。

# 使用扩展运算符

如你所见，这也产生了相同的结果，因为`Object.assign`和`spread operator`只是浅层合并了对象。

为了解决这个问题并正确合并两个深度嵌套的对象，我们可以使用由 [Lodash](https://lodash.com/) 库提供的`merge`方法。

# 使用 Lodash 合并

现在，它像预期的那样工作，并且`country`属性是[没有在合并中丢失](https://codepen.io/myogeshchavan97/pen/xxwQNOZ?editors=0011)。

Lodash 是最好的实用程序库，它为现实生活中的应用程序提供了很多函数。要探索其他有用的`lodash`方法，请查看[我以前的文章](https://levelup.gitconnected.com/extremely-useful-lodash-methods-b38f121fea7e)。

看看我最近出版的[掌握 Redux](https://master-redux.yogeshchavan.dev/) 课程。

在本课程中，您将构建 3 个应用程序以及一个点餐应用程序，您将了解:

*   基本和高级冗余
*   如何管理数组和对象的复杂状态
*   如何使用多个减速器管理复杂的冗余状态
*   如何调试 Redux 应用程序
*   如何在 React 中使用 Redux 使用 react-redux 库让你的 app 反应性。
*   如何使用 redux-thunk 库处理异步 API 调用等等

最后，我们将从头开始构建一个完整的[订餐应用](https://www.youtube.com/watch?v=2zaPDfCKAvM)，集成 stripe 以接受支付，并将其部署到生产中。

**别忘了订阅我的每周简讯，里面有惊人的技巧、窍门和文章，直接在这里的收件箱** [**中。**](https://yogeshchavan.dev/)
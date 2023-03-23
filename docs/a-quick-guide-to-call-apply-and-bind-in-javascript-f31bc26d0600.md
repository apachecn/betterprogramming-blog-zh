# JavaScript 中调用、应用和绑定的快速指南

> 原文：<https://betterprogramming.pub/a-quick-guide-to-call-apply-and-bind-in-javascript-f31bc26d0600>

## JavaScript 基础知识

![](img/affd71e890e649e1f32b236efc18483f.png)

欧文·史密斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

早在二月份，我创建了一个测验来帮助自己和他人更好地理解 JavaScript 中的关键词`this`。当时，我没有深入研究明确确定`this`所指对象的方法(即使用`call`、`apply`和`bind`方法)。

快进到本周，当我在探索 [d3.js](https://d3js.org) (一个数据可视化库)的缩放效果时遇到了`call`方法:

```
svg.call(d3.zoom().on('zoom', () => {
  g.attr('transform', d3.event.transform)
}))
```

与任何工具一样，如果不经常遇到，很容易忘记如何使用它们。因此，这里有一个对所有三种方法的快速参考，以提醒它们的用法和区别。

# 调用()并应用()

## 它们是多么相似

`call`和`apply`方法都允许我们指定`this`关键字应该引用什么并立即调用函数。

例如:

我们不能使用`measures.covidAlert()`，因为`measures`对象没有`covidAlert`函数作为其执行上下文。

这就是`call`或`apply`方法帮助我们的地方:

```
covidAlert.call(measures)
// or:
covidAlert.apply(measures)// => To stop the spread, please wear a mask.
```

## 它们是多么的不同

如果您想将参数传递给函数，您可以通过使用`call`方法逐个传递参数来实现:

使用`apply`，您应该传入一个参数数组:

# 绑定()

当您不想立即调用函数时，`bind`方法很有用。相反，`bind`创建了一个函数的副本，您可以在以后调用它。

您也可以使用`bind`方法逐个传递参数，就像使用`call`一样:

# 结论

就这样结束了！要了解更多关于关键字`this`的细节及其在 JavaScript 中的执行上下文，我推荐阅读以下两篇文章。他们清晰的讲解和实际的用例确实固化了我对函数绑定的理解:

*   [了解了这一点，在 JavaScript](https://www.taniarascia.com/this-bind-call-apply-javascript/) 中绑定、调用、应用
*   [理解 JavaScript](https://blog.bitsrc.io/understanding-call-bind-and-apply-methods-in-javascript-33dbf3217be) 中的调用、绑定和应用方法
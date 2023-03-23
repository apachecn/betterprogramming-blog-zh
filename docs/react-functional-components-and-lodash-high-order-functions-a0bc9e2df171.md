# 反应功能成分和 Lodash 高阶功能

> 原文：<https://betterprogramming.pub/react-functional-components-and-lodash-high-order-functions-a0bc9e2df171>

## 如何在 React 功能组件中无缝使用 Lodash 高阶功能，如去抖和节流

![](img/cf16ce71e95a80d8ec86e6214ddd04af.png)

由[伯纳德·赫曼特](https://unsplash.com/@bernardhermant?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

尽管现代浏览器不断增加对实用功能的支持(因此使得 [Lodash 不如](https://github.com/you-dont-need/You-Dont-Need-Lodash-Underscore)有用)，但你可能仍然会发现自己处于一种出于某种原因需要/想要使用 [Lodash](https://lodash.com/) 的情况。

随着 [React](https://reactjs.org/) 成为最流行的前端库之一，很有可能你最终会需要结合使用两者。这似乎是一个微不足道的任务(确实如此)，但是当我们将[功能组件和钩子](https://reactjs.org/docs/hooks-state.html#hooks-and-function-components)放在一起时，可能会有一些陷阱。我希望这篇文章能帮助你避开它们。

本文适用于 Lodash 提供的所有高阶效用函数，但我们将重点关注`[debounce](https://lodash.com/docs/4.17.15#debounce)`和`[throttle](https://lodash.com/docs/4.17.15#throttle)`。

# 目录

```
· [TL;DR](#c95f)
  ∘ [Module/file scope function](#9521)
  ∘ [Component scope function](#e148)
· [Debounce](#bb67)
· [Throttle](#2375)
· [Conclusion](#4b50)
```

# TL；速度三角形定位法(dead reckoning)

最重要的是避免多次定义函数。

## 模块/文件范围函数

如果函数不需要访问组件的作用域，您可以简单地在组件外部定义它:

## 组件范围功能

我们可以使用`useCallback`来确保去抖功能只被创建一次，并且如果它需要访问组件的作用域，它将在组件的整个生命周期中被重用:

# 去抖

`debounce`最常见的用例是减少由用户输入触发的函数调用的数量。例如，如果您正在实现一个支持自动完成的组件，您可能不希望在每次击键时都调用后端服务，因为击键的速度很可能比应用程序处理请求的速度快。您需要一种方法来避免服务器不必要的负载，并节省用户宝贵的 CPU 周期。

最佳实践是`debounce`服务调用，这意味着函数调用将一直等待，直到从上次调用函数起已经过了预定的时间量。

您可以在下面的代码沙箱中看到它:

# 喉咙

一个`throttle`功能最多每预定时间调用一次。一个常见的用例是减少调用处理滚动事件的回调函数的次数。例如，如果您正在实现一个支持无限滚动的组件，您将希望在用户接近页面底部时加载下一页/下一批。您很快会注意到，当用户滚动时，scroll 事件将被触发多次。

为了避免这种情况，一个选择是`throttle`事件处理函数。您可以在下面的代码沙箱中看到它:

# 结论

我希望你今天学到了新东西。感谢阅读。

保重，下次再见。
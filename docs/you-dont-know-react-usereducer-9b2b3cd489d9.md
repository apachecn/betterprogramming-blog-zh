# 如何有效地使用 React useReducer

> 原文：<https://betterprogramming.pub/you-dont-know-react-usereducer-9b2b3cd489d9>

## 使用 useReducer 挂钩时，您可能错过了什么

![](img/c372a35efd535843c4a3c8f89e156b2f.png)

[费德里科·布尔加拉西](https://unsplash.com/@fede_burgalassi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当 React 团队推出 16.8 版本并将 hooks 范式引入我们的社区时，我们过去编写代码的方式开始迅速改变。

作为一个更干净的解决方案，来跟踪我们在所有推荐者中的价值。

`useContext`是另一个游戏规则改变者，使上下文使用比以前更简单。

特别是`useReducer`显示出它是实现状态缩减器模式的一个很好的接口…

但它能做的就这些吗？肯定不是。

痴迷于 state reducer 模式的 React 开发人员看到的是另一个 API 一次又一次地实现相同的模式，他们经常将自己的想象力局限于这一次使用，而没有看到利用这个钩子可以获得的所有好处。

不要误会，我也是用这个模式搭配`useReducer`而且功能极其强大。但是我们还可以做更多的事情。

## 一个例子来看看…

下面检查一下常用的`useToggle`自定义钩子的实现。

使用 useState 的 useToggle 实现

*很明显有效，没问题！*但是如果你仔细看看，我们是否可以重构这个定制钩子来使用…谁知道…更少的钩子调用**？**

它目前所做的是用`useState`钩子创建一个状态，并结合使用它的 setter 函数和`useCallback`创建 toggle 函数。**两声勾人的叫声**。

我们应该知道的是，正如`useState`所做的，由`useReducer`返回的调度函数也保证被记住。使用它，我们可以潜在地摆脱`useCallback`调用，仍然获得相同的结果。

使用 useReducer 优化 useToggle 实现

你看到了吗？ `useReducer`让我们控制更新器函数的实现细节，暴露出最强大的 API 来创建定制钩子。

从现在开始，只是想象。

我一直在为许多目的使用`useReducer`,甚至抽象出最简单的逻辑，以使我的代码对我自己和我的同事来说是可重用和可读的。

我会给你一些我如何使用它的其他例子，希望有一天会对你有所帮助！

## useFunctionalRef

useFunctionalRef 实现

## 使用输入

使用输入实现

# 结论

这篇文章旨在推动您扩展您的想象力，并充分利用 React API 所公开的内容来创建一个可组合和可读的应用程序。

希望你喜欢这篇文章。感谢阅读。
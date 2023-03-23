# 优化 React 装载机组件的鲜为人知的关键

> 原文：<https://betterprogramming.pub/implementing-a-loader-react-component-e5535dc66738>

## 了解一些有经验的开发人员忽略的细节

![](img/754193b4fb059bd822c078bf5c111631.png)

有人可能认为实现一个 [React](https://reactjs.org/) loader 组件非常简单——事实也确实如此。然而，有一个细节，即使是一些有经验的开发人员也会忽略，这个细节将使代码更有效，更不容易出错。

让我们从最简单的实现开始:

我们会这样使用它:

在我们查看提议的改进之前，让我们后退一步，确保我们理解了代码评估顺序。

假设我们有以下代码:

我希望大家都同意，上述功能的执行顺序是:`function3`、`function2`、`function1`。基本的东西。

现在，这与 React 组件有什么关系？

为了编写呈现组件的代码，我们大多数人依赖一种类似 XML 的语法，称为 [JSX](https://reactjs.org/docs/introducing-jsx.html) (在呈现`Loader`时使用)。

但是当这些代码通过编译器时(最常见的是 [Babel](https://babeljs.io/) ，它被转换成原始的 JS，来自:

收件人:

[试试看](https://babeljs.io/repl/#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=FAMwrgdgxgLglgewgAgLYE8DCDUAckCmEMAFAJTIDewyyUSAzjMgNoAmAhjBwDJxMAaZAwIwAIl178YAXWQBeZGBEBlbjALkA3MgD0u5J27J-SiGwIg4EAmxp1GzFvx4IObawHMhImAEkGV3cvOUVlAjUuTTItYHtwaHgkZAAbNzYJbnIqe1o9AygUuCgAay9kGAALAmQAIzAYGGTUDhKCBgrqw0lkDgA3DjgUjlqUglzhUUypJhIWAHIMAFojDgBGeaFF9BXJJYAmeZkYid8AoI8ITxIQDhSRE9oAXzjaACdRMDeUEgmAHg8fQAfBNaH96o1kkhMEVSvJKGl3NMnkCLshpn9dBCmhAQXk8n8LgQ3iZAukvPCXOSrijQXlKKs-EwAHQtXAkVYKIHIAAGABVKqYACQMyRPUloawEZk8sgvfFg3REt54gm6QGqk5PIA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=react&prettier=false&targets=&version=7.7.3&externalPlugins=)。

`Loader`的另一种写法用法是:

很酷吧。

通过使用我们在文章开始时建立的关于代码求值顺序的知识，我们应该理解不管`isLoading`的值或`dataList`的值如何，都会对`dataList.map(…)`求值。

我们假设只有当`isLoading`变为假时`dataList`才会被填充。但是现在我们知道它甚至会在那之前被使用。因此，当这种情况发生时，我们需要有防御性代码，以防止`dataList`未定义的情况。

编写防御性代码是解决这个问题的一种方法，但是效率呢？如果不需要的话，例如当`isLoading`为真时，难道我们不想完全避免执行`dataList.map(…)`吗？

我们可以使用的模式如下:

因此，我们不是发送已经计算好的内容来呈现，而是发送一个将返回该内容的函数。那个函数实际上是一个 [thunk](https://en.wikipedia.org/wiki/Thunk) 。您可能已经从 [redux-thunk](https://github.com/reduxjs/redux-thunk#whats-a-thunk) 中了解了 thunks。为此，我们需要如下修改`Loader`组件:

在这个场景中，`children`是返回内容的函数。

这种模式很有效，因为它延迟了计算代码来呈现`children`，直到真正需要它的时候。当可能不需要渲染子对象时，这很有用。

如果我们试图在获得渲染内容所依赖的数据之前计算渲染内容，我们可以避免出现错误。

这使我们能够编写更短的代码，不需要太多的防御(因为我们只有在拥有所有需要的数据时才进行计算)。

这种模式也可以用于其他组件(想想任何有条件地呈现其子组件的组件；例如，基于用户的许可)。

感谢阅读！我希望这有助于您理解在 render 上如何计算代码。
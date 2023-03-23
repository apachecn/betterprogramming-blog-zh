# 用钩子反应全局组件

> 原文：<https://betterprogramming.pub/react-global-component-with-hooks-d1471488cf73>

## 如果您需要创建一个全局组件，useContext 是正确的选择

![](img/0eed4f04edb656e7c15cbcd15bcf58f5.png)

# 什么时候应该使用 useContext？

`Context`或`useContext`主要用于某些数据需要被不同嵌套层次的*多个*组件访问时。例如，如果你想管理一个在你的层次结构中使用的状态，而不需要显式地传递 prop，`useContext`将会很棒。

例如，您的在线或离线状态的状态管理器；许多组件需要访问这些信息来显示后备 UI 或管理集中式通知或消息系统。

# 如何使用 useContext？

你必须使用两种不同的钩子。第一个是`createContext`在变量内部构建上下文，在我们的例子中是`AppStateContext`。

之后，您将能够使用与内部功能相关的功能和状态来声明您的`AppStateProvider`。

在渲染中，您将包装`AppStateContext.Provider`，将在`AppStateProvider`中创建的变量传递给它。

# 如何将上下文添加到应用程序中？

你只需要将组件包装在`AppStateProvider`中，就可以在`AppStateProvider`的所有子节点上使用`Context`。

这与 [Apollo](https://www.apollographql.com/docs/react/api/react-apollo/) 客户端提供者的行为相同，你可以在[这篇文章](https://medium.com/better-programming/use-graphql-query-hooks-with-react-d440c6dcb57d)中尝试一下。

# 如何访问上下文？

在所有需要使用`Context`的组件内部，您必须使用另一个最重要的钩子`useContext`！将它传递给`AppStateContext`。

之后，您将能够访问传递给上下文的值。在这种情况下，我们有函数`SetMessage`和`SetError`来集中管理我们应用程序的所有通知。

您也可以从`Context`中仅导入您需要的值。

# 完成本教程的 CodeSandbox

[https://codesandbox.io/s/react-simple-usecontext-s4mdf](https://codesandbox.io/s/react-simple-usecontext-s4mdf)

## 如果你想了解更多关于钩子的知识

当我开始使用钩子时，我亲自阅读了《学会反应钩子》**,它帮助我理解了它们如何使用工具，如**使用上下文**:[https://amzn.to/2Y8hoX9](https://amzn.to/2Y8hoX9)**

# **参考资料和资源**

*   **[https://reactjs.org/docs/context.html](https://reactjs.org/docs/context.html)**
*   **[https://codesandbox.io/s/react-simple-usecontext-s4mdf](https://codesandbox.io/s/react-simple-usecontext-s4mdf)**
*   **[https://amzn.to/2Y8hoX9](https://amzn.to/2Y8hoX9)**
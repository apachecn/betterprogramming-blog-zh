# 在 React 中更好地处理状态的 3 种方法

> 原文：<https://betterprogramming.pub/3-ways-to-handle-state-better-in-react-9a44c3e9162>

## 使用 useReducer、自定义挂钩等等

![](img/fc0c723ac8408d50e29c082e1e4c5397.png)

照片由[费伦茨·阿尔马西](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 让开发人员完全自由地决定如何在组件中管理状态。React 有两种类型的组件:类组件和功能组件，这是在 React v16 中引入的。

类组件使用方法来管理状态，比如用`this.state`和`this.setState`来管理状态，用`componentDidMount()`来管理组件挂载后的副作用。如果有兴趣，可以[多了解一下这些方法](https://reactjs.org/docs/state-and-lifecycle.html)。

然而，由于大多数 React 开发人员现在使用 React 功能组件，我们将关注功能组件及其状态，这是使用 [React 钩子](https://reactjs.org/docs/hooks-intro.html)管理的。

在本文中，我们将了解一些我发现的在功能组件中管理状态的最佳实践，并真正利用 React hooks API。

# 对复杂状态使用 useReducer

有时候`useState`钩子不能解决问题，尤其是在处理可能涉及大型对象的复杂状态行为时。`useReducer`钩子是一个强大的 React 钩子，用于处理不需要第三方依赖的复杂状态管理。此外，它减少了每次渲染重新创建的数据量。

当与上下文和类型脚本结合使用时，`useReducer`会变得非常强大。

让我们来看看如何在现实生活中使用它:

看看这是多么模块化和更容易阅读。

# 定制挂钩 FTW(即，用于 win)

当使用 React 钩子时，有时会在一个组件中使用非常复杂的状态逻辑，这个组件利用多种类型的钩子来实现一个目的。

幸运的是，我们可以定制 React 钩子，将复杂的逻辑包含到一个可访问的钩子中。这对于表单、切换、异步行为以及组件中以一堆钩子结束的任何事情都很方便。

我们将看一个定制钩子的例子，用来切换条目列表中的条目。代码如下:

通过将所有这些逻辑抽象成一个自定义的 React 钩子，它不仅更容易阅读，而且可以在我们的应用程序中重用！

# 全局状态管理

在大多数情况下，您不需要状态管理库。只有在处理复杂状态的大型应用程序中，才应该引入外部库来管理它。

即便如此，如果您不能使用提供的[上下文 API](https://reactjs.org/docs/context.html) 在组件之间共享数据，请重新考虑。

如果你决定需要一个外部状态管理工具，我强烈推荐看看[反冲](https://recoiljs.org/)，这是一个超级轻量级和简单的管理全局状态的工具。另一个受欢迎的库是 [Redux](https://redux.js.org/) 。关于如何有效地使用这些工具，有大量的文章和讨论，所以我不会在本指南中涉及它们。

# 使用数据提取库

从外部 API 提取数据似乎是一个简单的问题，但是一旦您开始需要在内存中缓存数据(以减少 API 调用的数量)、更新数据以及在多个位置访问数据，问题就会变得非常复杂。

令人欣慰的是，有像 [React Query](https://react-query.tanstack.com/) 这样的现代数据获取库，可以有效地从外部来源获取、缓存、失效和刷新数据。它们还可以用于向一些外部客户端发送数据，真正包含与服务器交互的整个工作流。

更好的是，其中一些库，如 [Apollo Client](https://www.apollographql.com/docs/react/) 包含状态管理，以可预测、声明性和可读的方式处理获取数据、缓存数据和自动更新 UI 的整个过程。

# 结论

管理 React 状态可能很复杂，但是通过这些技巧，我希望您能够以一种高效且可读的方式来完成。
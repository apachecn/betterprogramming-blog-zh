# 对类似 Redux 的模式使用带有 useContext 和 useReducer 的高阶组件

> 原文：<https://betterprogramming.pub/using-higher-order-component-with-usecontext-and-usereducer-for-a-redux-like-pattern-8fb6ba0e5aba>

## 在 React 组件中维护干净且可重用的代码

![](img/dae2a845069ffd68d496a67513e9d054.png)

克里斯多夫·高尔在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

这不是`useContext`或者`useReducer`或者更高阶的元件教程，我就不详细解释了。

一个同事告诉我`useContext`和`useReducer`有多好。他们带来了拥有一个状态管理系统而不需要外部包的所有好处。

我花了一些时间来尝试它们，并对无法保持无国籍状态感到不快。

直接使用`useContext`的缺点包括:

1.  改变我们编写组件的方式。
2.  可以访问比组件所用更多的信息。
3.  无法在上下文之外重用表示组件。

在尝试了`useContext`和 HOC 之后，我设法用它们创建了无状态组件。

为了提高本文的可读性，我将所有代码放在一个文件中。我强烈建议您为每个组件创建单独的文件。

# 不使用上下文的计数器组件

让我们看看一种不用`useContext`编写计数器组件的方法。

假设我们有一个存储状态的容器`Counter`，我们可以创建一堆其他的无状态组件，比如`Display`、`IncrementButton`、`DecrementButton`来帮助我们显示`Counter`的计数值。

当我们有一个非常嵌套的组件，需要多层次的适当传递时，挑战就来了。

例如，在`Counter`和`IncrementButton`之间，如果有五个其他嵌套组件，我们将不得不把状态值从`Counter`一直传递到`IncrementButton`。

当您有许多组件需要跨多个级别传递状态时，这就变得很烦人。通过使用`useContext`，我们将能够在不传递任何道具的情况下访问状态。

下面的代码使用`useContext`代替。

我们不再需要将状态传递给单独的组件。`Display`、`IncrementButton`、`DecrementButton`分别不从母公司取`count`、`increment`、`decrement`。

但是，它们不再是独立的组件。它们需要在它们的祖先组件链中有一个提供者。

# useState 和 useReducer

一种常见的模式是将`useState`与`useReducer`一起使用。当我们需要管理许多状态时，这很方便。我们目前只有一个状态`count`，但是一旦我们有了几个状态，事情就会变得混乱。

以下代码利用了`useContext`和`useReducer`

`useReducer`取一个减速器及其初始状态。我们创建一个接受当前状态和动作的缩减器。更新后的`state`和`dispatch`功能由减速器返回给我们。

动作创建者`incrementAction`和`decrementAction`返回一个动作(一个带有类型和可选负载的对象)。然后，`dispatch`函数通过调度我们创建的动作来更新状态。

如果您熟悉 Redux，那么这个模式对您来说会很熟悉。

添加动作和缩减器有助于组织预期的状态。这些操作帮助我们跟踪我们拥有的功能。

# 独立的无状态组件

应用`useState`和`useReducer`后，我们创建了一个状态管理系统。现在我们将尝试把`Display`、`IncrementButton`和`DecrementButton`组件改回独立的无状态组件。

我们将使用一个高阶组件(HOC)来注入状态和调度函数作为道具。

让我们单独看一下特设委员会。

有两种方法可以检索上下文的值。

第一种方式是使用`useContext`如前所见:`const {state, dispatch} = useContext(CounterContext)`。

第二种方式是利用上下文的消费者，比如`CounterContext.Consumer`。`Consumer`需要返回组件的函数。

我们把值`state`和`dispatch`析构出来，和所有的道具一起传入组件。

使用特设，我们不再需要使用`Display`中的`useContext`。`Display`组件现在接受一个`state`作为道具。

然后我们需要用`withCounter`包装我们的无状态组件，这个 HOC 为无状态组件提供了`state`和`dispatch`。

下面是这一部分的代码

# 从显示中删除状态

在上一节中，我们传入了作为道具的`state`和`dispatch`。然而，`Display`组件只需要`count`而不是整个`state`对象。

在本节中，我们将从`Display`中移除`state`，仅传入`count`。我们可以通过扩展我们的 HOC 来加入一个`mapStateToProps`和`mapDispatchToProps`来做到这一点。

更新`withCounter` HOC 以接收`mapStateToProps`和`mapDispatchToProps`。

我们修改`withCounter`以包含`mapStateToProps`和`mapDispatchToProps`。

`mapStateToProps`应提取出`Display`所需的状态。组件必须明确地告诉它所需要的状态是什么，这些状态将作为道具传递给组件。

在`IncrementButton`组件中，为了映射分派函数，我们可以创建一个`mapDispatchToProps`函数来将分派函数映射到道具。在`DecrementButton`组件中，代码是类似的。

我们现在已经成功地使用`useContext`、`useReducer`和 HOC 创建了一个类似 Redux 的模式，它不需要我们修改如何编写我们的无状态组件。

特别感谢[梅布尔·李](https://medium.com/u/7c7e484eedea?source=post_page-----8fb6ba0e5aba--------------------------------)作为一名出色的编辑。

感谢阅读！

# 密码

下面是放在一个文件中的完整代码。强烈建议将组件、操作和上下文分离到不同的文件中。
# 用钩子代替 Redux

> 原文：<https://betterprogramming.pub/using-hooks-to-replace-redux-392f98ca61b7>

## 通过功能组件中的生命周期挂钩和状态管理，让您的生活更加轻松

![](img/c586ab93a4a064d0074c047b000ecd6e.png)

作为一名来自 Vue 的 React 开发初学者，我发现自己在与 Redux 和以正确的方式进行简单状态管理所需的所有样板文件作斗争:动作类型、动作生成器、正确的动作和 reducers。如果你想简单地连接 React，你必须安装另一个包 react-redux，并学习如何使用它。之后，如果你想使用异步操作，你必须编写自己的 redux 中间件或者学习如何使用另一个包——redux-thunk…

![](img/9ffed375fd5b687fc73e7f85dc06183a.png)

照片由[内森·杜姆劳](https://unsplash.com/photos/aZ9X3L1Va2Y?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/frustration?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

但是随后反应伴随着钩子！钩子是神奇的小东西，它让你可以更容易地使用基于类的组件特性，比如生命周期钩子和功能组件中的状态管理。

![](img/373be479e21e5cdd48c33779316c04e1.png)

照片由 [Nghia Le](https://unsplash.com/photos/V3DokM1NQcs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/success?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

其中一个钩子是`useReducer`。正如官方文档所述，`useReducer`，

> *接受一个* `*(state, action) => newState*` *类型的缩减器，并返回与* `*dispatch*` *方法配对的当前状态。(如果你熟悉 Redux，你已经知道这是如何工作的。)*

我想知道——也许 Redux 被提及是有原因的，我可以用它来让*像 Redux 一样进行集中的*状态管理。为什么不用`useState`？它做几乎同样的事情，但方式不同。React 文档指出，当被调用时`useState`返回一个有状态值和一个更新它的函数。

我认为有一个基本的区别使得`useReducer`和`useState`更适合谈论*集中式*状态管理。使用`useState`钩子，负责计算新状态的业务逻辑必须在所有更新状态的组件中。有了`useReducer`,我们可以将所有的业务逻辑放在一个地方(reducer 本身),这使得维护变得容易，并使组件专注于它们最初被设计来做的事情。另一个好处是，`useReducer`钩子保持了 Flux、Redux、Vuex 和其他状态管理解决方案所使用的“分派动作来更新状态”模式。

所以，我们有一个赢家。但是交易中的中央集权部分呢？因为 React 有一个长期的解决方案，与所有组件共享一个状态:上下文。根据官方文件:

> *上下文提供了一种通过组件树传递数据的方法，而不必在每一层手动向下传递属性。*

这是一种单向的、共享的、集中的状态，组件在发生变化时会对其做出反应。现在有了`useContext`挂钩。看来我们已经为这项工作选择了最好的工具——让我们让它们一起工作吧。

为此，我们将使用*“又老又无聊”*递增递减计数器应用程序。我们有 3 个组件由一个高阶组件(`App`本身)连接。其中一个用于显示计数器的值，另外两个用于更新计数器。下面是工作应用程序的代码:

为了简单起见，我在一个文件中写了这个例子——在一个真正的应用程序中，这些组件应该在不同的文件中。

我们的工作是替换从`App`组件传递到另一个组件的道具，并保持应用程序的功能。

首先，我们可以利用`useContext`替换`Display`组件上的道具:

但是，正如您可以通过执行代码来检查的那样，我们失去了递增/递减功能，因为上下文不能被其使用者更新。

这里是我们使用`useReducer`的地方。正如我之前所说的，`useReducer`函数返回状态和 dispatch 函数，可以用来调度将更新状态的动作。

受返回具有两个值的数组的钩子的启发，我们将使用`Context`来共享实际状态和分派函数，这两个值可以被析构并归属于两个单独的变量。

我们必须编写一个 reducer 来替换`App`组件中的`addToCounter`函数。

必须添加的最后一个部分是一个高阶组件，以利用`useReducer`钩子并将状态和调度设置为上下文值，因为`React`钩子只能在组件内部调用:

好了，我们的应用程序又开始工作了。

但是异步函数呢？Reducers 应该是纯函数，所以我们不能使用 async await 并等待承诺来解析，然后根据解析结果更新状态。

为此，我们可以使用类似 redux-thunk 这样更简单的工具。我们为`useReducer`分派方法构建了一个包装函数，它检查有效负载是否是一个承诺，解析它，然后分派期望的动作。为此，我们必须致力于`CounterContextProvider`并实现我们的“中间件”。我们将实现一个异步，它将在按钮被点击一秒后递增计数器。

终于来了！一种集中式状态管理代码，可用于共享状态，并调度更新状态的操作，无论是同步还是异步。

你可能认为这个项目代码太多，props 的解决方案更精简——但这只是一个例子，向你展示它是如何完成的。当然，在这样的应用中，使用它没有多大意义。然而，在有些情况下，您的应用程序可能更复杂，您不想安装和学习如何使用其他 3 个包(redux、react-redux 和 redux-thunk)来管理应用程序的状态。

最后一点:这个例子并不意味着 Redux 就是发电站，也不是 Redux 在所有项目上的替代品。存在非常真实和故意的限制。对于需要强大功能的大型项目，应该使用 Redux。如果您有一个较小的项目或一个初级开发人员团队，这个例子可能适合。
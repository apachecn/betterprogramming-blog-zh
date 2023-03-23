# React 中基于角色的授权

> 原文：<https://betterprogramming.pub/role-based-authorization-in-react-c70bb7641db4>

![](img/d0583278930e30969f6e950599910658.png)

[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/laptop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

身份验证和授权都是编写应用程序时常见的问题。认证回答“你是谁？”，而授权则回答了这个问题，“你被允许看那个吗？”

最近有人问如何使用 [React](https://reactjs.org/) 和 React Router 来完成基于角色的授权，并链接到[一个帖子](http://frontendinsights.com/role-based-authorization-using-react-router/)描述了一种实现方法。本质上，这篇文章建议简单地传递一个允许查看给定路由的角色列表，并检查当前登录的用户是否是路由处理程序中的角色之一。

那会有用，但是我看到它有一些问题。

*   违反单一责任原则。
*   不必要地通过 React 路由器传递数据。
*   没有可重用的代码。

那么，我们如何应用授权，让每个组件只做一项工作，是自包含的，并且是可重用的呢？

我们可以改进上述博客代码的一个方法是将授权逻辑封装在它自己的组件中——用一个[子功能](https://discuss.reactjs.org/t/children-as-a-function-render-callbacks/626)来防止组件在用户未被授权的情况下被挂载——并从您的路由处理程序中呈现出来。

这为我们提供了可重用的授权逻辑，这是朝着正确方向迈出的一步。我们甚至可以让授权组件加载用户本身，因此它所需要的只是允许的角色。但是每个路由仍然“拥有”授权正在发生的事实，这感觉不太对。

但是还有另一个选择。一个[高阶组件](https://facebook.github.io/react/docs/higher-order-components.html)——通常缩写为 HOC——可以将授权逻辑完全移出路由处理器。假设授权特设自行加载当前登录的用户，它可能如下所示:

我几乎没有包括 authorization HOC 的实现，因为它取决于您使用什么来存储您的数据，您是否希望显示错误消息或在失败时重定向到其他地方，您使用的是哪个路由器，等等。有很多未知，我想把重点放在模式而不是实现细节上。重要的是，它检查当前登录的用户，并呈现包装的组件，因为他们有足够的角色。

因为*何时应用 HOC 并不重要，所以我们可以将该函数调用移到路由器配置中。*

更新:已经有人指出，在 render 中将一个组件封装在一个 HOC 中会导致很多问题，作为一条规则，不应该这样做。在任何情况下，替换`component={}`内的内联函数调用都应该替换为将其分配给 render 方法之外的变量。

```
const RouteWithAuthorization = Authorization(YourRoute, ['roles'])
```

要进一步了解为什么这是个坏主意，请查看 [React 文档](https://reactjs.org/docs/higher-order-components.html#dont-use-hocs-inside-the-render-method)。回到原帖。

太好了！现在，在我们的路由处理程序中有一个授权逻辑的提示，没有任何东西通过 React Router 传递，我们所有允许的角色都定义在一个文件中。但是你知道吗，如果我们有很多路由，那将意味着很多重复的角色定义。

让我们做两个改变——颠倒我们给 HOC 的参数的顺序，并“ [curry](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch4.html) ”这个函数。我们的特设成为一个函数，返回特设。如果您使用 Redux，这种类型的函数调用应该看起来很熟悉，因为它与`connect`使用的机制相同。

现在，我们可以用不同级别的允许角色“播种”我们的授权特设，消除了一次又一次定义允许角色的需要。

好了，这样好多了。现在，我们可以预先定义一定数量的角色，我们可以有这样的路由器配置:

用户可以看到所有人，经理可以编辑用户，管理员可以添加新用户。通过查看用法可以清楚地看出意图是什么，并且代码的每个部分都有一个单独的职责。

编辑:重申上面的话，在 render [中创建高阶组件会导致 React 如何计算差异](https://reactjs.org/docs/higher-order-components.html#dont-use-hocs-inside-the-render-method)的问题。在上面的最后一个例子中，`User(User)`、`Manager(EditUser)`和`Admin(CreateUser)`部分应该从 render 方法中提取出来并分配给变量。

当然，客户端授权只是其中的一部分。后端也应该总是执行用户角色，因为客户机上的所有数据都可以从 devtools 中更改。

这不是最佳实践，这是一个我认为可以解决人们在现实世界中面临的问题的想法。我把这个作为博文而不是库来发表，因为每个人的用例都不一样。

也许在您的应用程序中，authorization HOC 将当前角色作为道具传递给它所包装的组件。也许其中一个约定俗成的参数是授权失败时要呈现的组件。可能不是一个更高级的组件，而是一个具有 function 子组件的组件，它获取角色和授权是否成功作为参数。也许您传递的不是一组允许的角色，而是一个带有签名`user => bool`的函数。

另外，React 有许多习惯用法，一旦你熟悉了它们，你就能写出真正有表现力的、简洁的代码。高阶组件、子函数和(基本)currying 是代码非常简单的概念:它们中的每一个都只需要几行代码，但是一旦你弄清楚它们解决的是什么类型的问题，你就可以将它们组合起来，使它们变得非常强大。

感谢阅读！我在 Twitter 上的名字是 [@vcarl_](https://mobile.twitter.com/vcarl_) (但在其他大多数地方我都是 vcarl)。我主持了 React 开发者聊天室[React flux](http://join.reactiflux.com/)和节点聊天室 [Nodeiflux](https://discordapp.com/invite/vUsrbjd) 。JS 开发者。如果您有任何问题或建议，请联系我们！
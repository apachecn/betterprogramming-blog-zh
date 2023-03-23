# 尝试这些，而不是使用 Redux 不可变的. js

> 原文：<https://betterprogramming.pub/try-these-instead-of-using-immutable-js-with-redux-f5bc3bd30190>

## 试试 Immer，Redux Toolkit，无缝的，或者根本没有库

![](img/4e0dd9c0e59c248a5c5345d466a81b72.png)

由[在](https://unsplash.com/@helloquence?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/s/photos/laptop-and-paper?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的

在[之前的一篇文章](https://medium.com/better-programming/3-reasons-to-avoid-using-immutablejs-with-redux-b0109d0123e8)中，我说过 [Immutable.js](https://immutable-js.github.io/immutable-js/) 不应该作为 Redux 的“不变性助手”库，但没有推荐任何其他选项。

以下是一些我比 Immutable.js 更喜欢使用的替代方法(排名不分先后):

1.  [无缝-不可变](https://github.com/rtfeldman/seamless-immutable)
2.  [沉浸](https://github.com/immerjs/immer)
3.  [Redux 工具包](https://github.com/reduxjs/redux-toolkit)
4.  根本没有图书馆

# 重述:为什么不是 Immutable.js？

将 Immutable.js 与 Redux 一起使用的主要问题是，它的数据结构不能向后兼容普通的 JavaScript 语法——它们只能使用库的 API 来处理，API 本质上是另一种语言。

这导致库扩展到整个代码库，甚至扩展到不应该关心不变性的地方，比如哑 React 组件。

这给开发人员带来了不必要的困难，他们必须不断地询问一些数据是普通的 JavaScript 还是由 Immutable.js 包装的。

试图析构一个不可变的. js 对象是回答这个问题的一种方式。

一个更好的选择是，库的使用仅限于处理不可变数据的地方:在 reducers 中。

其他地方，如组件、实用函数和选择器，应该都不知道任何不变性助手库。

# 替代方案 1:无缝-不可变

Seamless 最初给人的感觉非常类似于 Immutable.js:所有对象都必须由一个不可变的工厂函数包装，并且必须使用库的 API 来对它们进行更改。关键的区别在于*无缝对象可以使用普通 JavaScript 读取。*

考虑一个 reducer，它看起来与无缝和不可变的几乎相同。

但是在如何通过 Redux 的`mapStateToProps`将`todos`传递给一个组件，以及该组件如何使用这个 prop 方面有所不同——无缝地，这些都可以使用普通的 JavaScript 来完成。

使用 Seamless 和 Immutable.js 的`mapStateToProps`函数如下所示:

```
(state) => { todos: state.todos }  // Seamless(state) => { todos: state.get('todos') }  // ImmutableJS
```

在组件中使用 prop 看起来像这样:

```
props.todos.a.isComplete  // Seamlessprops.todos.getIn(['a', 'isComplete']) // ImmutableJS
```

因此，与 Immutable.js 相比，Seamless 的使用更局限于代码库中，因为无缝对象可以用普通的 JavaScript 读取。

像 dumb React 组件和基本选择器(以及它们的所有测试)这样的东西不会显示任何库的迹象。然而，改变数据只能使用无缝 API 来完成，因此在实用函数和更复杂的选择器中，这个库仍然是显而易见的。

# 备选方案 2: immer

Immer 的工作方式与 Immutable.js 和 Seamless 非常不同，正因为如此，它完全解决了向后兼容的问题— *它完全是普通的 JavaScript。*

主要由于这个原因，Immer 已经成为 Redux 中管理不变性的一个非常流行的解决方案。甚至连 [Redux 风格指南](https://redux.js.org/style-guide/style-guide/#use-immer-for-writing-immutable-updates)都把它列为明显的最爱:

> “使用 Immer 编写不可变的更新— **强烈推荐”**

有点令人惊讶的是，Immutable.js 仍然在 npm 下载排行榜上名列前茅，但我怀疑 Immer 很快就会领先。

![](img/bf76a6a92ebd602cc389660ababc7309.png)

[https://www . NPM trends . com/immutable-vs-immer-vs-seamless-immutable](https://www.npmtrends.com/immutable-vs-immer-vs-seamless-immutable)

Immer 允许开发人员编写变异操作，但确保这些操作只发生在临时的“草案”状态，该状态稍后会最终确定为新状态:

> 使用 Immer 就像有了一个私人助理；他拿了一封信(当前状态)并给了你一份副本(草稿),让你在上面草草修改。一旦你完成了，助手会拿走你的草稿，为你产生真正的不可变的，最终的信(下一个状态)。— Immer

这是通过 Immer 的`produce`功能实现的:

```
produce(currentState, producer: (draftState) => void): nextState
```

传递当前状态，期望的更新表示为一个突变(生产者函数)，Immer 将返回结果作为一个新的状态，保持当前状态不变。

需要注意的一点是，因为 Immer 本身负责返回最终的草稿，所以`producer`函数参数不应该改变草稿，也不应该试图返回某些东西——它应该只做其中一个。

![](img/49b07065892df81a725faf9072e70194.png)

有了这个`produce`功能的帮助，用 Redux 来使用 Immer 变得极其简单。考虑到与前面相同的 reducer，使用 Immer 比使用 Seamless 和 Immutable.js 更好，因为:

*   状态是普通的 JavaScript。
*   reducer 中对这种状态的更新是使用普通 JavaScript 完成的。
*   从这个状态派生出来的所有道具都是普通的 JavaScript。

Immer 实现了我们所希望的:这个库仅限于 reducers，不会扩展到代码库的其他部分，比如组件、实用函数和选择器。

# 替代方案 3: Redux 工具包

Redux Toolkit 提供了一组实用程序和函数，帮助开发人员编写包含最佳实践的一致 Redux 逻辑。

一个这样的实用程序是`createReducer()`，它在幕后使用 Immer，允许不可变的状态更新被写成突变。

该实用程序还允许简化 reducer 的结构(再见，switch 语句),使 reducer 作为一个整体看起来有些不同，但实际的状态更新看起来与 Immer 相同:

因此，使用 Redux 工具包带来了与使用 Immer 相同的好处，只有`produce`函数是隐藏的。

# 选择 4:根本没有图书馆

最有趣的选择，但也最容易出错。出于这个原因，这是获得对状态管理中的不变性的欣赏的一个很好的方法。

独立完成任务可能不会有很好的伸缩性，但是对于小型应用程序来说，它们仍然很小(小型应用程序需要 Redux 吗？)这是一个和其他解决方案一样好的解决方案。

简单来说；只要记住`{ ...spreadEverything }`中的减速器即可；

记住扩展操作符只进行浅层复制，因此有递归。

更进一步，您可以使用`Object.freeze()`来确保数据的不变性——试图改变一个冻结的对象将会抛出一个错误。

然而，冻结是一种肤浅的方法(像 spread 操作符一样),这意味着当状态变成嵌套状态时，冻结和解冻它变得更加复杂，必须递归地完成。

当你发现自己在编写定制的`deepFreeze`和`deepUnfreeze`函数时，可能是时候开始使用上面的库解决方案之一了，它能更好地解决这个问题。

# 摘要

在 Redux 中，有许多库可以帮助处理不可变数据，但在我看来，那些向后兼容普通 JavaScript 的库是赢家，这很快就让 Immutable.js 打折扣了。

现在，Immer + Redux 组合似乎是最受欢迎的，并且有很好的理由——Immer 没有引入任何不能用普通 JavaScript 处理的东西。

这意味着它只限于 reducers，不像 Immutable.js 那样扩展到代码库的 Redux 特定部分之外。所有这些都使得 Immer 易于理解和使用，易于采用，必要时甚至易于删除(但我怀疑会是这样)！

请记住，对于小型应用程序，您可能根本不需要库。至少在导入之前尝试一下。
# 你应该知道的 React 应用的 8 个有用技巧

> 原文：<https://betterprogramming.pub/8-useful-tricks-for-react-apps-you-should-know-a15c2678c846>

## 把戏不只是给孩子玩的

![](img/951418b14d275c3cbdf193a628e3b8c2.png)

*丹尼尔·科尔派拍摄的照片*

React 已经经历了许多阶段的转变，从来没有让它的粉丝感到惊讶。

起初，我们用 [mixins](https://www.reddit.com/r/reactjs/comments/alc005/what_exactly_is_a_mixin/) 来创建和管理我们的界面，然后出现了[类组件](https://reactjs.org/docs/react-component.html)的概念，现在 [React 挂钩](https://reactjs.org/docs/hooks-intro.html)改变了我们在 React 中构建应用的方式。

你知道还有什么很棒吗？了解一些你可以在 React 中使用的巧妙技巧，这将有助于你更好地构建你的应用程序(当然，如果你遇到一些你不知道自己能做的事情)。

本文将介绍 React 中每个 React 开发人员都应该知道的八个巧妙技巧。

现在，我并不期望这个列表中的每一项对你来说都是新的，但是我希望你能在这个列表中找到至少一项对你有用的，你直到现在才知道你可以做的事情。

以下是你应该知道的 React 的八个技巧:

# 1.用字符串创建 React 元素

这个列表中的第一项是创建一个常规的 react DOM 元素，用简单的字符串表示 HTML DOM 元素标签。更准确地说，是表示 DOM 元素的字符串。

例如，您可以通过将字符串`'div'`赋给变量来创建 React 组件，如下所示:

React 将只调用`React.createElement`并使用该字符串在内部创建元素。这不是很棒吗？

在 [Material UI](/material-ui.com) 这样的组件库中常用，可以声明一个`component`道具，调用者可以为其决定组件的根节点成为`props.component`的值，比如:

你可以这样使用它:

您还可以传入将用作根节点的自定义组件:

# 2.使用误差边界

在 JavaScript 中，我们习惯于用`try/catch`处理代码执行中的大多数错误，这个代码块可以“捕捉”发生的错误。当这些错误在 catch 块中被捕获时，您可以避免您的应用程序在代码边界内崩溃。

这方面的一个例子如下所示:

React 最终只是 JavaScript，所以我们可以假设我们可以使用相同的策略来捕捉和处理错误。然而，由于 React 的性质，组件内部的 JavaScript 错误会破坏 React 的内部状态，并导致它在未来的渲染中发出神秘的错误。

出于这个原因，React 团队引入了[错误边界](https://reactjs.org/docs/error-boundaries.html)，每个 React 开发者都应该知道它们，这样他们就可以在他们的 React 应用中使用它们。

在错误边界之前发生的错误的问题是，当这些隐含的错误在以前的渲染中发生后，在将来的渲染中发出时，React 没有提供一种方法来处理它们，也没有从组件中恢复它们。这就是为什么我们都需要误差边界！

错误边界是 React 组件，它捕捉组件树中任何地方的错误，记录它们，并可以显示一个后备 UI，而不是崩溃的组件树。

它们在渲染期间、在生命周期方法内部以及在它们下面的整个树的构造函数内部捕捉错误(这就是为什么我们在应用程序的顶部某处声明和渲染它们)。

这里有一个来自 [React 文档](https://reactjs.org/docs/error-boundaries.html#introducing-error-boundaries)的例子:

然后，您可以将它作为常规组件使用:

```
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

# 3.保留以前的值

更新道具或状态时，只需使用`React.useRef`就可以保留它们以前的值。

例如，要跟踪一个项目数组的当前和以前的更改，您可以创建一个被赋予以前值的`React.useRef`和一个当前值的`React.useState`:

这是因为`React.useEffect`是在组件完成渲染后运行*的。*

当`setNames`被调用时，组件重新渲染，`prefNamesRef`将保留之前的名称，因为`React.useEffect`是从*之前的渲染*执行的最后一个代码。

而且，由于我们在`useEffect`中重新分配了`prevNamesRef.current`，它在下一个渲染阶段成为之前的名称，因为它最后被分配了之前渲染阶段的名称。

# 4.使用 React.useRef 进行灵活的非过时值检查

在 React 中引入 React 挂钩之前，如果我们想确保操作(比如获取数据)发生在组件挂载到 DOM 上的之后的*，我们就有了类组件的`componentDidMount`静态方法。*

当 React Hooks 出现时，它很快成为编写我们的组件的最流行的方式，而不是使用类组件。当我们想要跟踪一个组件是否已经安装，以防止在组件卸载后设置状态时，我们应该这样做:

钩子在迁移到 React 钩子之后没有了`componentDidMount`,卸载之后状态更新导致的内存泄漏的概念仍然适用于钩子。

然而，类似于`componentDidMount`使用 React 钩子的方法是使用`React.useEffect`，因为它是在组件完成渲染之后*执行的。如果您在这里使用`React.useRef`来分配挂载值的值，您可以达到与类组件示例相同的效果:*

另一个跟踪最新变化而不导致重新渲染的好用例的例子是像这样与`React.useMemo`结合使用( [source](https://github.com/mui-org/material-ui/blob/master/packages/material-ui/src/utils/useForkRef.js) ):

如果 ref 属性改变并被定义，这将创建一个新的函数。这意味着 React 将用`null`调用旧的分叉 ref，用当前 ref 调用新的分叉 ref。

由于使用了`React.useMemo`，ref 将被记忆，直到`refA`或`refB`的 ref props 发生变化，在此行为中会发生自然清除。

# 5.使用 React.useRef 自定义依赖于其他元素的元素

`React.useRef`有几个有用的用例，包括将其自身分配给 ref prop 以反应节点:

如果我们想要获取`div`元素的坐标位置，这个例子就足够了。

然而，如果应用程序中某处的另一个元素想要在`position`相应地改变或应用一些条件逻辑的同时更新它们自己的位置，最好的方法是使用`ref callback function pattern`。

当使用回调函数模式时，您将接收 React 组件实例或 HTML DOM 元素作为第一个参数。

下面的例子只是显示了一个简单的例子，其中`setRef`是应用于`ref`属性的回调函数。您可以看到，在`setRef`中，您可以做任何您需要的事情，而不是直接将`React.useRef`版本应用到 DOM 元素:

# 6.高阶组件

普通 JavaScript 中创建强大的可重用函数的常见模式是[高阶函数](https://eloquentjavascript.net/05_higher_order.html)。因为 React 最终是 JavaScript，所以也可以在 React 内部使用高阶函数。

对于可重复使用的*组件*，诀窍是使用*高阶组件*。

一个*高阶组件*是当你有一个以组件作为参数并返回一个组件的函数时。

就像高阶函数可以用来抽象出逻辑并在应用程序的其他函数中共享一样，高阶组件使我们能够从组件中抽象出逻辑并在其他组件中共享它们。

这意味着您可以使用一堆可重用的组件在您的应用程序中重用。

这是一个高阶元件的例子。在这个代码片段中，一个高阶组件`withBorder`接受一个定制组件并返回一个隐藏的*“中间层”*组件。

然后，当父组件决定呈现这个返回的高阶组件时，它作为一个组件被调用，并接收从“中间层组件”传入的属性:

# 7.渲染道具

我最喜欢在 React 库中使用的技巧之一是*渲染道具模式*。它在某种程度上类似于高阶组件，解决了一个类似的问题:在多个组件之间共享代码。

渲染道具公开了一个函数，它的目的是传回外部世界渲染其子对象所需的一切。

在 React 中渲染组件的最基本方式是这样渲染它们:

使用渲染道具时，渲染其子对象的道具按照惯例称为`render`，如下所示:

在这个例子中，`MyComponent`是一个组件的例子，我们称之为*渲染道具组件*，因为它期望`render`作为道具，并调用它来渲染其子组件。

这在 React 中是一个强大的模式，因为我们可以通过 render 回调将共享状态和数据作为参数传递，从而允许组件在多个组件中被渲染和重用:

# 8.记忆

作为 React 开发人员，最重要的事情之一是优化像`React.memo`这样的组件的性能。这有助于防止令人讨厌的错误，如在应用程序运行时导致灾难性崩溃的无限循环 T2。

阅读下面的 React 应用程序中应用记忆的几种方法:

*   [React.memo](https://reactjs.org/docs/react-api.html#reactmemo)
*   [React.useMemo](https://flaviocopes.com/react-hook-usememo/)
*   [react . use 回调](https://reactjs.org/docs/hooks-reference.html#usecallback)
*   [做出反应。纯组件](https://reactjs.org/docs/react-api.html#reactpurecomponent)
*   [优化性能](https://reactjs.org/docs/optimizing-performance.html)

# 结论

这篇文章到此结束！我希望你发现这是有价值的，并期待在未来更多。
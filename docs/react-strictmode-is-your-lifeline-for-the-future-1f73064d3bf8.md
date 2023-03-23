# React 中的 StrictMode 是什么

> 原文：<https://betterprogramming.pub/react-strictmode-is-your-lifeline-for-the-future-1f73064d3bf8>

## 关于 React 你需要知道的一切。严格模式

![](img/edad6dff4584e8803c93f11a13da72ee.png)

[Toa Heftiba](https://unsplash.com/@heftiba?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 funPhoto。

通常当我回头看我很久以前写的一段代码时，我认为我可以写得更好 100 倍。我确信我不是这条船上唯一的人。随着我们获得更多的经验，我们不断发展我们的编码实践和设计模式。React 也是这种情况。

React 也经历了许多转变，随着它的发展，过去被认为是好的某些实践不再适合未来的路线图。随着 v16 的发布，发生了一个重大变化，它对 React Fiber 的架构进行了重写。主要关注的是时间安排(即，决定何时应该执行一项工作，同时记住不同任务(如动画、UI 更新等)的优先级)。

几乎与此同时，React 中添加了一个新的上下文 API。

此外，为了在未来版本中提供并发模式，渲染阶段被分成多个部分，已经发生了很多变化。它的开始见证了 React 钩子的引入，某些生命周期方法的废弃，等等。

在本文中，我们将看看 React 中的`StrictMode`帮助我们识别的所有不推荐使用的模式。

# 什么是反应。StrictMode 以及如何使用它？

`React.StrictMode`是一个突出应用程序中潜在问题的工具。它的工作方式是将应用程序呈现为一个组件，封装应用程序的一部分或整个应用程序。`StrictMode`不在 DOM 中呈现任何可见的元素，但是在开发模式下启用某些检查并提供警告。

*注意:* `*StrictMode*` *在生产模式下不运行任何检查或显示警告。*

您可以像这样为整个应用程序启用`React.StrictMode`:

通过用`<React.StrictMode>`包装它，您可以在应用程序的一部分中类似地启用它。

从 React 的 v17 开始，`StrictMode`支持以下功能:

*   识别传统字符串引用。
*   检测已弃用的`findDOMNode`方法。
*   检测旧上下文 API 的使用。
*   检测已被 React 否决的不安全的生命周期方法。
*   检测 React 组件中的意外副作用。

## 1.识别传统字符串引用

React 最初版本中的 Refs 是使用字符串进行赋值的。然而，正如 Dan Abramov 在[本期 Github](https://github.com/facebook/react/pull/8333#issuecomment-271648615)中指出的那样，它也存在许多问题:

> “它要求 React 跟踪当前呈现的组件(因为它不能猜测`this`)。这使得反应有点慢。
> 
> 它并不像大多数人期望的那样使用“渲染回调”模式(例如`<List renderRow={this.renderRow} />`)，因为由于上述原因，ref 将被放置在`List`上。
> 
> 它是不可组合的，也就是说，如果一个库在传递的子对象上放置了一个引用，用户就不能在其上放置另一个引用。回调引用是完全可组合的。"

由于这些原因以及许多其他原因，例如在需要强制转换引用的 TypeScript 中键入引用的问题，为类组件引入了更好的替代方法:

*   回调参考
*   `React.createRef`

## 2.检测不推荐使用的`findDOMNode`方法

在给定类实例的情况下，之前使用了`ReactDOM.findDOMNode`方法来获取 DOM 节点。通过直接向 DOM 元素而不是类实例添加一个 ref，可以避免使用`findDOMNode`。

`findDOMNode` API 有两个主要问题:

*   这将只返回类组件实例中的第一个子级。但是，随着 v16 中片段的引入，您可能会从一个组件实例中返回多个元素，这可能会导致一个问题，因为您可能希望将所有元素的包装器或返回的元素列表中的特定元素作为目标。
*   `findDOMNode` API 是一个只接受请求的 API(即当它被调用时，它将评估并返回结果)。例如，如果所呈现的元素在子元素中被有条件地改变，父元素可能不知道。

`findDOMNode`的替代方法是使用`React.forwardRef`并将 ref 传递给子组件中所需的元素，或者通过单独的名称(如`innerRef`)传递 ref，并从子组件的 props 中使用它来设置所需元素的 ref。

## 3.传统上下文 API

React 版本引入了新的上下文 API。在此之前，使用了旧的易错 API，如果父层次结构中的某个组件通过实现`shouldComponentUpdate`停止了子元素的重新呈现，将导致消费者无法更新。

即使 React 继续支持 v16.x 中的旧 API，`StrictMode`也会通过显示警告来指出旧的上下文 API 的用法，以便可以将它们移动到最新版本。

## 4.检测不安全的生命周期方法

在 React 的 v16.3.0 中，对 React APIs 进行了一些突破性的更改。其中一个变化是生命周期方法如`componentWillMount`、`componentWillReceiveProps`和`componentWillUpdate`的废弃。新的生命周期也被加入，比如`getDerivedStateFromProps`和`getSnapShotBeforeUpdate`。

尽管这些生命周期方法在 React 的后续版本中仍然可用，并且在重命名时添加了前缀`UNSAFE_`, React 可能会在未来的版本中完全删除它们。

为什么这些生命周期方法被否决了？

为了理解这一点，我们必须首先知道 React 通常分两个阶段工作:

*   呈现阶段:在这个阶段，React 检查需要对 DOM 进行哪些更改。React 在这个阶段调用一个`render`函数，并将结果与之前的渲染进行比较。渲染阶段生命周期包括`componentWillMount`、`componentWillReceiveProps`、`componentWillUpdate`和`render`。
*   提交阶段:这是 React 实际提交对 DOM 的更改并调用提交阶段生命周期(如`componentDidMount`和`componentDidUpdate`)的阶段。

提交阶段很快，但是呈现阶段可能很慢。为了用并发模式的愿景来优化它，React 决定将渲染分成几部分，暂停并恢复工作，以避免阻塞浏览器。

因此，当他们这样做时，渲染阶段的生命周期可能会被调用多次，如果这些包含副作用或不正确的做法，它们可能会导致应用程序的行为不一致。此外，这些生命周期中的一些会鼓励糟糕的开发实践。其中包括:

*   `componentWillMount`
*   `componentWillReceiveProps`
*   `componentWillUpdate`

让我们看看其中的一些做法。

## 在 componentWillMount 中调用 setState

初始化状态的正确和不正确方式

正如您在上面的代码片段中看到的，`componentWillMount`用于在初始渲染之前设置状态，但是通过在构造函数中设置初始状态或者使用`state`作为类属性，可以很容易地重构它。

## 组件 WillMount 中的异步请求

在`componentWillMount`中有一个异步获取请求对于服务器端渲染和即将到来的并发模式都是有问题的。使用服务器端渲染，将不会使用在`componentWillMount`中获取的数据。对于异步渲染，提取请求可能会进行多次。

在 React 类组件中获取数据的不正确和正确方法

有一种普遍的误解，认为在初始渲染之前，在`componentWillMount`中获取的任何数据都是可用的。这是不正确的，您应该使用加载状态来避免使用初始渲染中的数据，并进行 API 调用来获取`componentDidMount`中的数据。

## 在组件 WillMount 中添加订阅或侦听器

在`componentWillMount`中添加订阅/监听器有两个问题:

*   使用服务器端渲染，服务器上不会调用`componentWillUnmount`函数，因此不会进行清理，并可能导致内存泄漏。
*   使用异步呈现，可以附加多个订阅，因为呈现阶段生命周期可能被调用多次。

添加和删除监听器的正确方法是将`componentDidMount`和`componentWillUnmount`生命周期方法配对。

## 更新状态或调用属性更改的副作用

以前，`componentWillReceiveProps`生命周期用于在父道具改变时更新状态或调用子道具中的副作用。虽然没有太大的问题，但是开发人员中有一个误解，认为这个生命周期只有在道具更新时才会被调用。

但是，每当重新渲染父属性时，它就会被调用。

因此，在比较了以前和当前的属性后，如果没有正确执行，任何函数调用或状态更新都可能会有不一致的行为。

## 更新前读取 DOM 属性

有时，您可能希望保存某些 DOM 属性，例如更新前的滚动位置，以便在应用更新时将其还原，从而防止在添加或删除新项目时用户当前查看的项目从视图中消失。

以前，您会在`componentWillUpdate`生命周期方法中这样做。然而，使用异步呈现，调用`componentWillUpdate`和调用`componentDidUpdate`的时间之间可能会有间隙，如果用户以实际改变滚动位置的方式与 DOM 交互，例如调整窗口大小或实际滚动更多内容，这可能会导致不一致。由于这个原因，`getSnapshotBeforeUpdate`被建议作为`componentWillUpdate`的替代，因为它正好在 DOM 发生突变之前被调用。

既然我们已经讨论了一些为什么要删除这些用法的原因，让我们回到正题上来。

我们可能会想，“为什么我们甚至需要一些实用程序来指出不安全的函数呢？我们可以简单地搜索并使用推荐的实践更新它们。”

虽然您是正确的，并且可以在您自己的代码库中这样做，但是您将无法容易地识别出在您的代码库中用作依赖项的*不安全的*生命周期。`StrictMode`也将帮助您指出它们，以便您可以更新它们(或者如果最新版本不兼容，用替代版本替换它们)。

## 5.检测意外的副作用

正如我们在上一节中确定的，React 希望在即将到来的并发模式中优化呈现阶段，它决定分解呈现阶段。因此，渲染阶段生命周期可以被多次调用，如果在其中使用了副作用，会导致意外的行为。

在 React 的最新版本中，这些功能包括:

*   `constructor`
*   `getDerivedStateFromProps`
*   `shouldComponentUpdate`
*   `render`
*   `setState`类和功能组件中的更新器功能
*   传递给`useMemo`、`useState`、`useReducer`的功能

虽然副作用是不确定的，但是`StrictMode`通过两次调用上面的函数，使副作用对开发人员来说更加确定。这样，如果在渲染阶段函数中错误地编写了任何副作用，由于它所呈现的明显的不一致性，它本身就可能处于开发模式中。

例如，如果在一个`constructor`函数中建立一个 WebSocket 连接，那么在开发模式中两次调用`constructor`会有助于更容易发现，因为将会建立两个连接。

# 关键要点

*   `React.StrictMode`可以为部分或整个应用程序启用。
*   它只在开发模式下运行，为遗留 ref 用法、废弃的`findDOMNode`方法、遗留上下文 API、不安全的生命周期和意外的副作用提供警告。
*   `StrictMode`导致渲染阶段生命周期和函数的有意双重调用，以便更容易发现这些函数中实现的意外副作用。
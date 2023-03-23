# React 组件生命周期

> 原文：<https://betterprogramming.pub/the-react-component-lifecycle-c9302202a69f>

## 在创建、更新和销毁组件时，了解组件正在经历什么

![](img/5952c5a88e7c4e9f9977719820863479.png)

K. Mitch Hodge 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在我使用 React 的经验中，`componentDidMount`一直是我使用的生命周期方法。我基本上忽略了其他人。然而，存在许多其他的生命周期方法。虽然很少使用，但了解它们的功能以及它们在组件生命周期中扮演的角色是很重要的。

下面概述了组件生命周期的各个阶段，以及每种生命周期方法在此过程中的位置和方式:

![](img/75396d3980bdfecebf169b88963bbecd.png)

照片来自 [Wojciech Maj](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/) 。

# 组件生命周期

React 的一个特点是能够创建由可重用组件组成的高响应性 UI。由于用户交互和应用程序中的输入或更新，组件可以很容易地改变。为了适应这种不断变化的状态，React 组件参与了创建(或安装)、更新和删除(或卸载)的生命周期。这个生命周期包括内置的方法，称为生命周期方法，可以在生命周期的不同时间调用这些方法来控制组件如何对应用程序中的更改做出反应。

记住`render()`是 React 组件中唯一需要的方法是很重要的。如果您想改变组件的默认行为，以下方法都是可选的。

# 增加

React 组件本质上是 JavaScript 类，因此它们包含一个`constructor`函数来定义组件的初始状态，甚至在挂载发生之前:

注意:`super`在上面被调用是为了确保我们可以将自己的功能添加到从`React.Component`继承的`constructor`函数中。

一旦确定了初始状态，组件的 JSX 就会呈现在页面上，这样组件就被安装到 DOM 上了。此时，`static getDerivedStateFromProps`和`componentDidMount`生命周期方法是可用的。

每次渲染一个组件时都会调用`static getDerivedStateFromProps`函数，这是唯一一个在安装过程中在`render`之前调用的生命周期方法。这个函数返回一个对象来更新状态，如果没有要更新的内容，则返回`null`。`static getDerivedStateFromProps`很少使用，除非组件的状态依赖于其属性随时间的变化。在每次渲染之前触发该方法(有时是不必要的)会导致应用程序出现性能问题。

`componentDidMount`只被调用一次，紧跟在组件的第一个`render`之后。这是迄今为止最常用的生命周期方法。通常，任何 DOM 操作或从端点获取长期运行的数据都在这个方法中进行。这也是设置任何订阅或间隔的好地方。但是，它们必须在`componentWillUnmount()`中终止(稍后将详细介绍)。

举例来说，假设我们有一个每 10 秒获取实时股票数据的应用程序。在`componentDidMount`中，我们将通过回调获取数据的函数来初始化时间间隔:

```
componentDidMount() {
    this.interval = setInterval(this.fetchStockData, 10000);
} 
```

# 更新

在组件生命周期的更新阶段，有四种生命周期方法(除了`render`)可用。这些是`static getDerivedStateFromProps`(再次)、`shouldComponentUpdate`、`getSnapshotBeforeUpdate`和`componentDidUpdate`。这些方法总是按照简介中的图表中列出的顺序调用:

1.  `static getDerivedStateFromProps(props, state)`
2.  `shouldComponentUpdate(nextProps, nextState)`
3.  `render()` —渲染通过`this.props`和`this.state`访问道具和状态。
4.  `getSnapshotBeforeUpdate(prevProps, prevState)`
5.  `componentDidUpdate(prevProps, prevState, snapshot)` —可以通过`this.props`和`this.state`访问道具和状态。这是最后一次以前的道具和以前的状态可用。

我们在安装部分介绍了`static getDerivedStateFromProps`。但是，由于它在每次渲染之前被调用，所以它也存在于更新阶段。每当组件接收新的 props 或状态发生变化时，就会调用该方法。重要的是要注意，虽然这个方法已经被调用，但道具可能实际上并没有改变。也许一个父组件被更新，重新渲染，并把同样的道具传递给它的子组件。

同样，这种方法很少使用，因为它会导致性能问题，并且通常可以通过其他更好的解决方案实现相同的行为。

`shouldComponentUpdate`的独特之处在于它返回一个布尔值。它查看道具和状态的当前变化，并确定组件是否应该更新。这不同于默认行为，默认行为在每次状态改变时重新呈现。`shouldComponentUpdate`只作为性能优化而存在。不要使用这种方法来防止可能导致棘手错误的连续渲染，建议使用`PureComponent`来执行属性和状态的浅层比较，以确定是否存在差异以及是否有必要重新渲染。

`getSnapshotBeforeUpdate`方法主要用于从 DOM 中捕获信息，这些信息在下一个生命周期方法被调用之前可能会改变。鼠标或滚动位置是可能需要捕捉的快速变化数据的一个很好的例子。这个方法在`render`之后、React 将内容从虚拟 DOM 粘贴到真实 DOM 之前被调用。该方法返回`null`或信息的快照，这些信息将作为参数传递给`componentDidUpdate`。

`componentDidUpdate`仅在更新后调用。该方法可以访问当前属性和状态，以及先前属性、先前状态和`getSnapshotBeforeUpdate`中捕获的数据快照。`componentDidUpdate`是进行网络请求(前提是道具有变化)或者更新第三方库的绝佳场所。`setState()`可以在`componentDidUpdate`内调用，但必须用条件括起来，以防无限循环。

# 卸载

在卸载或删除阶段，我们可以使用一种生命周期方法:`componentWillUnmount`。这个方法在组件从 DOM 中移除之前被调用，并且它是任何时间间隔、网络请求或者在`componentDidMount`中发起的订阅被清除的地方。将其映射回我们的 stocks 应用程序，我们将结束该时间间隔，以便我们的应用程序不会在组件被卸载后继续获取不必要的数据:

```
componentWillUnmount() {
    clearInterval(this.interval);
}
```

# 结束语

虽然在大多数情况下没有必要用这些生命周期方法来覆盖组件的正常行为，但是当需要像订阅、数据获取和控制组件何时更新这样的操作时，选择几个是相当常用的。尽管如此，了解这些方法以及它们在组件生命周期中的位置对于更好地理解组件、DOM 和我们在浏览器中看到的内容之间的关系是很重要的。

如果你好奇的话，还有一些其他很少使用的方法没有在这里介绍，在 [React 文档](https://reactjs.org/docs/react-component.html)中有详细介绍。

# 参考

[](https://reactjs.org/docs/state-and-lifecycle.html) [## 状态和生命周期-反应

### 本页介绍了 React 组件中状态和生命周期的概念。你可以找到详细的组件 API…

reactjs.org](https://reactjs.org/docs/state-and-lifecycle.html) [](https://reactjs.org/docs/react-component.html) [## 做出反应。成分-反应

### 该页面包含 React 组件类定义的详细 API 参考。它假设您熟悉…

reactjs.org](https://reactjs.org/docs/react-component.html) [](https://www.tutorialspoint.com/reactjs/reactjs_component_life_cycle.htm) [## ReactJS -组件生命周期

### 在这一章中，我们将讨论组件生命周期方法。componentWillMount 在渲染之前执行，在两个…

www.tutorialspoint.com](https://www.tutorialspoint.com/reactjs/reactjs_component_life_cycle.htm)
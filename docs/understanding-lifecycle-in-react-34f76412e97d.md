# 了解反应生命周期

> 原文：<https://betterprogramming.pub/understanding-lifecycle-in-react-34f76412e97d>

## 了解 React 中的生命周期及其方法

![](img/ae8fd78c72ed20a76726c0586c15045f.png)

詹姆斯·哈里森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

简单直接地说，生命周期指的是 React 组件在其存在期间所经历的 3 个阶段。

React 中的每个组件都要经历三个阶段，即:

1.  增加
2.  更新
3.  卸载

> *作为 React 开发人员，了解这三个阶段以及与每个阶段相关的方法是很重要的。这些方法可以在类组件中被覆盖，以执行副作用和管理组件的状态。*

让我们来看看每个阶段的这些方法:

# 增加

这是任何组件的初始阶段。它对应于组件将被插入 DOM 的时刻。也就是说，当它开始出现在你的浏览器屏幕上的时候。

在此阶段，有 4 种方法按以下顺序执行:

1.  `contructor()`
2.  `getDerivedStateFromProps()`
3.  `render()`
4.  `componentDidMount()`

在这些方法中，只有第三种(render)在创建类组件时是必需的。其他的由开发人员决定是否在他们的组件中实现。

第一个是`constructor`，在 React 组件中用来设置初始状态值。

方法只用于一个目的:它允许一个组件通过改变属性来更新它的状态。这里的见其用法[的两个例子。在下面的例子中，我用 prop 中传递的值设置了计数器的状态。](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#updating-state-based-on-props) and [here](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#fetching-external-data-when-props-change)

第三个也是唯一一个强制的是`render`。被调用时，它必须检查 this.props 和 this.state，并返回以下类型之一:React 元素、门户、字符串、数字、布尔值或 null 值。render 方法*应该保持纯*，也就是不要在其中产生副作用(比如 API 调用)。

今天，您不再需要调用构造函数来初始化状态，这就消除了对它的需求。

`getDerivedStateFromProps`方法给我们的组件增加了一些复杂性，总的来说你不需要使用它。

[React 博客上的这篇文章指出了为什么不使用派生状态](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)

# 更新

当组件覆盖了一些属性更改(即其父组件传递了新的属性)或当内部状态覆盖了一个更改(例如通过`this.setState({})`)时，组件进入更新阶段。

就像在组装阶段一样，将调用一个定义好的方法序列。它们是:

1.  `getDerivedStateFromProps()`
2.  `shouldComponentUpdate()`
3.  `render()`
4.  `getSnapshotBeforeUpdate()`
5.  `componentDidUpdate()`

第一种方法，`getDerivedStateFromProps`我们已经知道并在组装阶段介绍过。在更新阶段，它的行为是相同的。

第二个方法`shouldComponentUpdate`将决定序列中的方法是否被执行。也就是说，它将决定组件是否应该再次呈现。这种方法只是为了避免不必要的更新，从而优化应用程序的性能。

如果已经调用了`this.forceUpdate()`方法，更新将不会调用 shouldComponentUpdate，组件将会如其名被强制更新。

然后在提交最近呈现的输出之前调用`**getSnapshotBeforeUpdate**`。它允许您的组件在 DOM 发生潜在变化之前从 DOM 中获取一些信息(例如，滚动位置)。

这个生命周期返回的任何值都将作为第三个参数传递给`componentDidUpdate`方法，这个参数称为 snapshot。

随着 DOM 的更新，最终调用了`**componentDidUpdate**`方法。

# 卸载

当组件要通过改变状态或道具从 DOM 中移除时，我们就处于反汇编阶段。

这里我们只有一种生命周期方法，那就是:

## 1.componentWillMount()

该方法在组件被移除之前执行。它用于删除条目和侦听器。一个例子是使用 setInterval，即使组件被删除，它将继续运行，而不管调用它的组件是否存在。

# 遗留方法

为了制造混乱和一些难以解决的错误，一些 React 生命周期方法正在被废弃。在版本 17 中，它们仍然可以在名字前面加上前缀`UNSAFE\_` 。要了解更多关于它们为什么被弃用的信息，请看 React 博客上的[这篇文章](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html)

1.  `UNSAFE_componentWillMount()`
2.  `UNSAFE_componentWillReceiveProps()`
3.  `UNSAFE_componentWillUpdate()`

# 结论

我希望现在您对什么是生命周期有了更好的理解！这绝对是一个可以帮助你在未来构建更好、更可预测的 UI 的概念。

```
**Want to Connect?**If you want to read more React stuff, do visit my website: [https://fernandobelotto.dev](https://fernandobelotto.dev)
```
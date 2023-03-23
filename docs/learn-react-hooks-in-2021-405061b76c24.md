# 2021 年学 React 钩子

> 原文：<https://betterprogramming.pub/learn-react-hooks-in-2021-405061b76c24>

## 过渡到反应钩的实践指南

![](img/534a5d3b9515b616fa6aff495eeccc66.png)

照片由 [Kari Shea](https://unsplash.com/@karishea?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你还在用基于类的 React 组件吗？你没机会用 React 钩子吗？你并不孤单——尽管 React 挂钩已经存在了一段时间，但一些开发人员仍然没有时间使用它们。

在本文中，我们将向您展示如何将基于类的组件转换成 React 钩子。我们还将详细介绍每个挂钩，并使用代码示例和练习来解释它的功能。到本文结束时，您应该已经掌握了基本的 React 钩子。

# 反应组分

确保遵循示例，并在演示 React 应用程序中创建它们。你获得的实践经验越多，你就会越习惯语法和概念。

## 反应基于类的组件

首先，让我们来看看一个简单的基于类的组件，看看如何使用钩子将它转换成:

这是一个典型的基于类的组件。让我们来看看功能组件的构建模块。我们将有一个处理所有事情的函数，而不是一个类。这个函数不是单独有一个`render`方法，而是你新的`render`方法。

## 功能组件模板

就是这么简单！这类似于接收道具作为参数的渲染方法。让你的道具更具可读性的一个常用方法是使用 spread 操作符来析构它们:

这样我们就能清楚地看到我们期望的道具。如果我们与 TypeScript 结合使用，这可能会使 React `PropTypes`变得多余。

# 反应状态挂钩

因为我们正在使用函数，所以我们实际上并不像过去在基于类的组件中那样有`this`可用。为了解决这个问题，React 想出了`hooks`。这些代码可以在您的功能组件中用来访问以前可用的概念。

对于状态的情况，我们可以使用 React 的`useState`钩子。如果你感兴趣，这里有关于使用状态的官方文档。首先，让我们按照文档中的定义来定义`useState`变量。

你会立即注意到`useState`语法看起来很奇怪。那是因为我们立即破坏了它。`useState`钩子返回一个包含两个条目的数组。第一项是状态变量的`value`。第二项是状态变量的`setter method`。假设我们想要更新州中的`headingText`值。为此，我们可以使用第二项中返回的变量。这里有一个例子:

有多个状态变量时会是什么样子？对于每个状态变量，我们需要使用`useState`钩子:

同样重要的是要注意，我们总是将一个字符串传递给`useState`方法。`UseState`将使用它作为状态变量的默认值，因此它不必是一个字符串——它可以是一个布尔值、一个对象或任何你喜欢的东西。

# 练习:useState —用钩子实现一个点击计数器

如果我们回头看前面的例子，我们可以看到如何使用状态，但它在这个例子中不是特别有用。让我们一起完成`useState`上的一个练习。

让我们用功能组件中的`useState`钩子创建一个点击计数器。该组件应该呈现一个按钮，每次按钮被按下时，一个段落显示按钮的点击次数。你必须使用`useState`钩子来保持点击的数量。

此外，实现一个将初始点击量设置为指定值的 prop 如果给了组件`20`,那么组件应该从一开始就显示`20 clicks`。

当你准备好了，向下滚动图片找到解决方案。

![](img/2e456364a1f86062d17d3a4fe6b9399c.png)

普通技术人员在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

解决方案如下:

你遇到什么困难了吗？请在评论中告诉我们！

# 反应生命周期和使用效果

我们现在知道如何创建功能组件并使用`useState`钩子。但是生命周期事件呢？

## React componentDidUpdate

使用基于类的`componentDidUpdate`函数，我们可以监听组件属性的变化。我们如何用钩子做到这一点？

这就是`useEffect`钩子的用处。每当依赖项的值发生变化时，它就会触发一个给定的函数。这个钩子不返回一个数组，相反，它允许你在执行过程中当特定的变量改变时运行特定的代码。

`useEffect`需要两个参数:要运行的函数和依赖列表。依赖列表是变量列表。每当这些变量中的任何一个发生变化，第一个参数中的给定函数就会运行。

## 反应组件安装

还记得在基于类的 React 中，我们有函数`componentDidMount`吗？每当组件被装载到页面上时，函数就会运行。对于`useEffect`，我们需要提供一个依赖列表，所以它不适合替换`componentDidMount`，对吗？不完全是——它有锦囊妙计。

如果我们传递一个空列表作为依赖列表，那么作为第一个参数给出的函数将以类似于`componentDidMount`的方式运行。让我们尝试一下，并扩展我们以前的点击计数器的例子！请注意，空列表对于此行为是强制性的。开发人员的一个常见错误是忘记定义列表。如果给出的是`undefined`而不是`[]`，那么`useEffect`的行为将不会像`componentDidMount`一样。

# 反应上下文挂钩

React 团队归类为`basic`的第三个也是最后一个钩子是`context`钩子。这允许您将数据从应用程序的顶层传递给所有递归子级。这通常对创建提供者很有用，比如主题提供者，所以每个组件都可以访问应用程序的主题风格。

请注意，虽然我们可以使用`useContext`钩子传递上下文，但是我们不能更新它！如果您的应用程序需要这样做，您可能需要查看状态管理器，比如 Redux。

我们不会对这个钩子进行过多的描述，但是这里有一个 React 文档中的使用示例，以及注释:

# React useMemo Hook:性能优化

React 给了我们`useMemo`钩子来提高应用程序的性能。与`useEffect`类似，`useMemo`钩子接受两个参数:一个要执行的函数和一个依赖列表。与`useEffect`的一个区别是这个钩子将返回函数的值，所以它的值可以在函数组件中使用。

`UseMemo`将缓存方法的值，只要依赖列表不改变。这意味着计算值的方法将不会运行，除非依赖列表中的某个值发生变化。

这在通过引用处理变量时特别有用。如果在你的函数组件中定义了一个数组，那么在重新渲染时引用将会不断变化。如果这个数组被传递给子组件，这些子组件将继续重新渲染，而实际上并不需要。

当使用`useMemo`时，我们可以通过缓存数组及其引用来避免这种行为，直到用于计算其值的一个依赖项发生变化。让我们看一个例子。

对于传递引用变量，`useMemo`可以避免在每次渲染主组件时重新渲染子组件。这是因为我们缓存了变量引用，而不是在每次渲染时重新定义它。这是使用`useMemo`的唯一理由吗？不完全是。这对于大型处理非常有用。

看看我的文章[你应该在 React 中使用备忘录吗？一个基准分析](https://medium.com/swlh/should-you-use-usememo-in-react-a-benchmarked-analysis-159faf6609b7)来找出你什么时候应该在你的 React 应用中使用`useMemo`。

在官方文档页面查看更多关于`[useMemo](https://reactjs.org/docs/hooks-reference.html#usememo)` [钩子的信息。](https://reactjs.org/docs/hooks-reference.html#usememo)

# 反应使用回调挂钩

`useCallback`钩子类似于`useMemo`钩子，但是它不是记忆变量值，而是记忆函数。它基于相同的概念工作，为了避免函数的引用在每次渲染时改变，缓存函数直到它的一个依赖项改变。

这避免了从接收此函数作为道具的子组件进行不必要的重新渲染。

# React 挂钩列表

上面解释的钩子是 React 中使用的一些比较重要的钩子。当然，React 还有其他挂钩——这里是它们每种类型的列表，如 React 在[官方文档](https://reactjs.org/docs/hooks-reference.html)中定义的。

## 基本挂钩

*   `useState`用于管理组件的状态
*   `useEffect`针对组件的生命周期事件，如果特定的依赖关系发生变化，则触发特定的功能
*   `useContext`为了将上下文传递给所有子组件

## 高级挂钩

*   `useMemo`用于记忆变量值。
*   `useCallback`用于记忆功能。
*   `useReducer`用于创建和使用定制的减速器。
*   `useRef`用于定义对 DOM 中组件的引用。
*   `useImperativeHandle`可用于自定义`useRef`返回的值。
*   `useLayoutEffect`与`useEffect`相似，但只在所有 DOM 更新后触发。
*   `useDebugValue`用于开发定制钩子时显示调试数据。

## 第三方挂钩

任何人都可以为 React 创建额外的挂钩。Redux 是一个流行的 React 状态管理器，它创建了`useSelector`钩子来从功能组件中检索 Redux 状态数据。如果您已经设置了 Redux，那么使用这个钩子检索 Redux 数据是小菜一碟。

# 钩子小抄

*   `useState` : `const [value, setter] = setState(defaultValue);`
*   `useEffect` : `useEffect(myFunction, dependencyArray);`
*   `useEffect`:did mount 的用法`useEffect(myFunction, []);`
*   `useContext` : `React.createContext(object)`和`useContext(MyContext);`
*   `useMemo` : `const myValue = useMemo(calculateFunction, dependencyList);`
*   `useCallback` : `const myFunction = useMemo(callbackFunction, dependencyList);`
*   `useSelector` : `const result = useSelector(state => state.item);`

# 摘要

React 开发的当前趋势是转向使用钩子。钩子和基于类的组件是互操作和兼容的！您可以将较新的组件作为功能组件，同时保持较旧的组件基于类。

但是不用担心，React 不会很快移除基于类的组件。

> 没有从 React 中移除类的计划——我们都需要继续发布产品，并且负担不起重写。
> 
> —官方 [React 文档](https://reactjs.org/docs/hooks-faq.html)

# 下一步是什么？

练习，练习，练习！你用钩子获得的实践经验越多，你就会越快适应它们。使用 React 挂钩创建一些宠物项目。这很有趣，你会得到一个很好的项目！

请继续关注关于 React 挂钩的下一篇文章，在这篇文章中，我们将创建自己的自定义 React 挂钩。

# 额外学习资源

*   你真的应该在 React 中使用 UseMemo 吗？基准分析。
*   [React Hooks 官方文档页面](https://reactjs.org/docs/hooks-reference.html)
*   [React Redux 第三方挂钩](https://react-redux.js.org/api/hooks)

[订阅我的媒介](https://kevinvr.medium.com/membership)到**解锁** **所有** **文章**。通过使用我的链接订阅，你是支持我的工作，没有额外的费用。你会得到我永远的感激。
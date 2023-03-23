# 探索 React Hooks 性能实践

> 原文：<https://betterprogramming.pub/adventuring-into-react-hooks-performance-practices-77a23825a946>

## 性能陷阱以及如何避免它们

![](img/df03138430afd92d235716fc1aa213ec.png)

照片由 [Kolleen Gladden](https://unsplash.com/@rockthechaos?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

从 16.8.0 版本开始，React 向我们介绍了一种不用写类就能使用状态和其他 React 特性的方法— [React Hooks](https://reactjs.org/docs/hooks-intro.html) 。

这是对经典类范式的惊人改进，它允许我们在组件之间重用有状态逻辑。毫不奇怪，它有一个学习曲线，可能会导致性能陷阱。

让我们深入探讨最常见的问题，并尝试找出如何避免它们。

# 重新渲染物质

好的，我们发现在使用钩子时可能会遇到一些性能问题，但是这些问题来自哪里呢？

本质上，钩子的大部分问题来自于组件不必要的渲染。看看下面的例子:

这是一个组件，它有两个状态，A 和 B，以及四个增量动作。我添加了`console.log`方法来查看每次渲染的消息。前两个动作是基本的增量，只是将 A 或 B 值增加 1。

让我们点击 *a++，b++* 按钮，看看控制台:每次点击，应该只有一个渲染。这真的很好，因为这正是我们想要的。

现在 1s 后按下 *a++，b++*按钮:每次点击，你会看到两次渲染。如果你想知道下面发生了什么，答案很简单。

React 将同步状态更新批处理为一个。

另一方面，对于异步函数，每个`setState`函数触发一个 render 方法。

但是如果你想有一致的行为呢？钩子的第一条规则来了。

# 规则 1:对于连接的数据模型，不要在几个 useState 方法之间分割状态

想象你有两个独立的国家。然后，需求改变了，因此一个状态的更新导致另一个状态的更新。

在这种情况下，你必须把它们加入一个对象:`const { A, B } = useState({ A: 0, B: 0})`。或者，利用`[useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer)`功能。

这个规则的另一个很好的例子是数据加载。通常，你需要三个变量来处理它:`isLoading`、`data`和`error`。不要试图将它们分开，而是更喜欢`useReducer`。

它允许您将状态逻辑从组件中分离出来，并帮助您避免错误。拥有一个具有这三种属性的对象也是一种解决方案，但不会那么显式和容易出错。

相信我，我见过很多人忘记将`isLoading: false`设定为错误。

# 定制挂钩

既然我们已经知道了如何在单个组件中管理`useState`，让我们将增量功能移到不同的地方使用。

我们将增量逻辑重构到它自己的钩子上，然后使用`[useEffect](https://reactjs.org/docs/hooks-reference.html#useeffect)`函数运行一次。

注意，我们必须在依赖数组中提供`incrementA` setter，因为我们在内部使用它，并且它是由 [Hook 的 ESLint 规则](https://www.npmjs.com/package/eslint-plugin-react-hooks)强制执行的。(如果您之前没有启用它们，请启用它们！).

如果你尝试渲染这个组件，你的页面将会因为无限的重新渲染而被冻结。为了解决这个问题，我们需要定义钩子的第二个规则。

# 规则二。确保只有当新对象发生变化时，才从自定义钩子返回新对象

上面的组件总是被重新渲染，因为`increment`钩子每次都返回一个新函数。为了避免每次都创建一个新函数，将它包装在`[useCallback](https://reactjs.org/docs/hooks-reference.html#usecallback)`函数中。

现在使用这个钩子是安全的。

有时，您需要从自定义钩子返回一个普通对象，确保您只在它的内容改变时使用`[useMemo](https://reactjs.org/docs/hooks-reference.html#usememo)`更新它。

# 如何在为时已晚之前找到这些重新渲染？

通常，在导致性能问题之前发现这些问题是很麻烦的，所以您必须使用特定的工具来预先检测它们。

其中之一是`[why-did-you-render](https://github.com/welldone-software/why-did-you-render)`库，它告诉你可避免的重渲染。将您的组件标记为`MyComponent.whyDidYouRender = true`，开始与它交互，并在控制台中寻找消息。

我保证你会在接下来的五分钟内发现新的东西。

另一个选择是使用 [React Dev 工具扩展](https://github.com/facebook/react/tree/master/packages/react-devtools)中的*分析器*选项卡。尽管您必须考虑您期望组件进行多少次重新渲染，但是该选项卡只显示了重新渲染的次数。

让我知道你还遇到过哪些关于钩子的挑战，让我们一起解决它们。

# 参考资料和更多资源

*   [React 钩子 API](https://reactjs.org/docs/hooks-reference.html) 。
*   [如何使用开发工具评测器评测 React 应用](https://kentcdodds.com/blog/profile-a-react-app-for-performance)。
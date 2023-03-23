# 是否应该停止使用 React 中的 isMounted？

> 原文：<https://betterprogramming.pub/should-you-stop-using-ismounted-in-react-a4feab96ee02>

## 克服 React 中最古老的反模式之一

![](img/ce357d61c52f908c462fb4e087820ae2.png)

图片由 [@lautaroandreani](https://unsplash.com/@lautaroandreani) 在 [Unsplash](https://unsplash.com/) 上拍摄

如果您熟悉 React 组件的生命周期，您就会知道 React 组件会经历一个安装阶段和一个卸载阶段。如果您已经使用了类组件，那么您已经使用了生命周期挂钩，如`componentDidMount`和`componentWillUnmount`来设置和清理组件。

随着几年前 React 转向功能组件和挂钩，许多开发人员感到困惑，因为没有类似于`useIsMounted`、`useDidMount`和`useWillUnmount`的挂钩。一些开发人员和库也为此创建了自定义挂钩。

在 ES6 类支持之前，React 有一个`isMounted`方法，许多开发人员将它用作反模式[。让我们看看有多少开发人员，包括我自己，分别在 ES6 类和功能组件中重复了这个模式。](https://reactjs.org/blog/2015/12/16/ismounted-antipattern.html)

在 ES6 类中，我们通过执行以下操作回到了反模式:

人们在许多文章中建议上述方法，并堆积溢出的答案，最终由一个同事传给另一个。React 不再有`isMounted`方法是有原因的！

关于`isMounted`反模式的链接文章解释了开发人员使用它来防止已经卸载的组件上的状态更新，因为这将引发警告。

例如，在上面的代码中，`loadData`方法执行一个异步操作。然而，当数据加载时，这个组件可能不再存在于 DOM / Screen 中。因此，作为一种变通方法，我们只在当前挂载的情况下在`loadData`方法中调用`setState`。如果我们要删除`if (this.isMounted) return;` guard 子句，如果在`setState`运行时组件没有安装，我们将从 React 得到一个警告。

文章还解释说，最好在`componentWillUnmount`上编写清理逻辑，以确保未安装的组件根本不运行任何代码。在上面的例子中，我们将使用如下的`[AbortController](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)`进行清理:

我认为，与通过检查`isMounted`的值来阻止状态更新相比，上述代码的意图更加明确。

现在，让我们看看功能组件。在功能组件中重复这种模式似乎没有意义，因为它们根本没有生命周期方法！然而，我们中的许多人，包括我自己，通过做以下事情重复了这种模式:

这里，因为我们不能使用`this`关键字，所以我们使用`useRef`作为替换(这是 React 文档中提到的有效模式[)。我们在空依赖效果期间设置`isMounted`的值。然后，我们使用从`useEffect`返回的清理函数作为`componentWillUnmount`的替代。](https://reactjs.org/docs/hooks-reference.html#useref)

在我看来，这种模式的出现是因为没有任何依赖关系的`useEffect`钩子经常被比作基于类的组件中的`componentDidMount`和`componentDidUpdate`生命周期方法。虽然他们可以比较，但他们的行为并不相同！

`useEffect`的目的是当组件内部的一些值改变时运行副作用。在`useEffect`中声明的副作用将总是至少运行一次，即使它没有依赖关系。我们利用这种行为来做我们在类组件中的`componentDidMount`内部会做的事情。围绕`React Suspense`有一个完整的数据获取策略叫做[随取随渲染](https://17.reactjs.org/docs/concurrent-mode-suspense.html#approach-3-render-as-you-fetch-using-suspense)，使得不依赖效果加载数据成为可能。

现在，让我们看看如何使用清理函数来解决这个问题，而不是使用`isMounted`方法。如果您曾经将`cleanup function`与`useEffect`一起使用过，解决方案应该非常明显。毕竟，它被称为清理功能！

如果我们只在`useEffect`中加载数据，代码会更简单。

# 结论

作为软件开发人员，我们的工作首先是找到让事情按预期运行的解决方案。不可能知道做每件事的正确和预期的方法。毫无疑问，`isMounted`解决方案是可行的！我很确定许多代码库不处理未安装组件的状态更新，更不用说使用`isMounted`解决方案了。

所以，我想说这是一个好的开始。然而，如果我们知道如何做，放弃反模式并更优雅地解决问题并不是坏事。
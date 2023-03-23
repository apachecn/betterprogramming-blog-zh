# 生命周期方法 101:添加和卸载倒计时

> 原文：<https://betterprogramming.pub/lifecycle-methods-101-adding-and-unmounting-a-countdown-a6922a8ead8>

## 取消 componentWillUnmount 方法中的所有订阅和异步任务

![](img/0ea749ecb3ae311ec7dad7bc2fd76bef.png)

来源:[pexels.com](http://pexels.com)

在本文中，我将介绍以下 React 生命周期方法:`ComponentDidMount`、`ComponentDidUpdate`和`ComponentWillUnmount`。如果你需要复习它们是什么，读读这个故事。

# 我们将建造什么

我们将构建一个简单的应用程序，一个倒计时，如下所示:

![](img/d3c9241e788f37085365279ab831392a.png)

# 第一步。设置

在我创建了一个 React 应用程序之后，我创建了一个`Timer`类组件。它被初始化为一个状态`timeLeft: 15`:

这是我的`index.css`:

# 第二步。componentDidMount 中的 setInterval

现在，当我们的页面加载时，我们希望倒计时立即开始。因此，我们将在`componentDidMount`中放置一个`setInterval`:

现在，每秒钟状态都会更新，随后页面上呈现的数字也会更新。

# 第三步。条件渲染

现在让我们添加条件渲染:如果`timeLeft`是 0，我们希望看到“KABOOM！”让我们添加一个`console.log`以便跟踪状态。

现在你可以看到，一旦状态变为`0`，就会弹出一个漂亮的动画。

然而，这并不是我们旅程的终点，因为尽管它看起来没问题，但我们现在正在经历内存泄漏——在后台，状态仍在进行不必要的更新:

![](img/4e025be5fffdea4b2345d9a605590536.png)

# 第四步。componentDidUpdate 中的 clearInterval

让我们保护自己免受这种丑陋的内存泄漏。为此，我们将在`componentDidUpdate`中设置一个跟踪器，如果`timeLeft`为 0，它将清除间隔。

提醒一下，`componentDidUpdate`是在每次`state`或`props`更新后触发的方法。

```
componentDidUpdate(prevProps, prevState){
  if (prevState.timeLeft == 1){
   clearInterval(this.interval)
  }
}
```

**注意** : `componentDidUpdate`以这个特定的顺序接受两个参数，所以即使我们没有使用`prevProps`，我们仍然需要把它们放在那里。

后台不再发生内存泄漏。然而，想象一下，当计时器运行时，在计时器变为 0 之前，我们的用户点击了页面上的某个东西。

这将导致一个难看的错误和长期内存泄漏:

```
Warning: Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in the componentWillUnmount method.
```

我们也需要针对这种情况的安全措施！

# 第五步。组件中的 clearInterval 将卸载

现在，我们将使用`componentWillUnmount`，这是一个在组件从页面中卸载(消失)之前触发的方法:

```
componentWillUnmount(){
  clearInterval(this.interval)
}
```

现在我们不会有内存泄漏了。

您的代码现在应该如下所示:

如果你想进一步练习 React 生命周期方法，试试[给你的页面添加一个标题](https://medium.com/better-programming/react-lifecycle-methods-101-adding-a-title-to-your-page-8781eba042cd)！
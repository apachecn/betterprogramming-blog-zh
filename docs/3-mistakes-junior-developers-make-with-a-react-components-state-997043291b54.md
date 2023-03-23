# 初级开发人员对 React 组件状态犯的 3 个错误

> 原文：<https://betterprogramming.pub/3-mistakes-junior-developers-make-with-a-react-components-state-997043291b54>

## 以及如何停止制造它们

![](img/42ded0db0b1f549cfde2de503008c846.png)

照片由[杰米街](https://unsplash.com/@jamie452?utm_source=medium&utm_medium=referral)上[的 Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄。

关于 web 开发，我最喜欢的一点是总有新的东西要学。你可能花了一生的时间来掌握各种编程语言、库和框架，但仍然不知道所有的东西。

因为我们都在学习，这也意味着我们都容易犯错误。这是可以的。目标是变得更好，变得更好。如果你犯了错误并从中吸取教训，你就做得很好！但是如果你没有学到任何新的东西，并且不断重复犯同样的错误，那么听起来你的职业生涯可能会停滞不前。

本着这种精神，下面是我在代码评审中经常看到的三个初级开发人员在处理 React 组件状态时会犯的常见错误。我们将看看每个错误，然后讨论如何修复它。

# 1.直接修改状态

当改变一个组件的状态时，重要的是返回一个修改过的状态的新副本，而不是直接修改当前状态。如果您错误地修改了组件的状态，React 的区分算法将不会捕捉到更改，并且您的组件将不会正确更新。

让我们看一个例子。假设你有这样一个状态:

```
this.state = {
  colors: ['red', 'green', 'blue']
}
```

现在你想给这个数组添加颜色`yellow`。这样做可能很诱人:

```
this.state.colors.push('yellow')
```

甚至是这个:

```
this.state.colors = [...this.state.colors, 'yellow']
```

但是这两种方法都是不正确的！当更新类组件中的状态时，您总是需要使用`setState`方法，并且您应该总是小心不要改变对象。

将元素添加到数组的正确方法如下:

```
this.setState(prevState => ({ colors: [...prevState.colors, 'yellow'] }))
```

这让我们犯了第二个错误。

# 2.设置依赖于前一状态而不使用函数的状态

有两种方法可以使用`setState`方法。第一种方法是提供一个对象作为参数。第二种方法是提供一个函数作为参数。那么，你什么时候想用一个而不是另一个呢？

例如，如果您有一个可以启用或禁用的按钮，您可能会有一个名为`isDisabled`的状态，其中包含一个布尔值。如果您想将按钮从启用状态切换到禁用状态，那么编写类似这样的代码，使用一个对象作为参数是很有诱惑力的:

```
this.setState({ isDisabled: !this.state.isDisabled })
```

那么，这有什么错呢？问题在于 React 状态更新可以是批处理的，这意味着多个状态更新可以在一个更新周期中发生。如果您的更新是批处理的，并且您有多个更新处于启用/禁用状态，那么最终结果可能不是您所期望的。

更新此处状态的更好方法是提供前一状态的函数作为参数:

```
this.setState(prevState => ({ isDisabled: !prevState.isDisabled }))
```

现在，即使您的状态更新是批处理的，并且对启用/禁用状态的多次更新是一起进行的，每次更新都将依赖于正确的先前状态，以便您总是以您期望的结果结束。

对于像递增计数器这样的事情也是如此。

不要这样做:

```
this.setState({ counterValue: this.state.counterValue + 1 })
```

执行以下操作:

```
this.setState(prevState => ({ counterValue: prevState.counterValue + 1 }))
```

这里的关键是，如果你的新状态依赖于旧状态的值，你应该总是使用一个函数作为参数。如果设置的值不依赖于旧状态的值，则可以使用对象作为参数。

# 3.忘记了 setState 是异步的

最后，重要的是要记住`setState`是一个异步方法。例如，假设我们有一个状态如下的组件:

```
this.state = { name: 'John' }
```

然后我们有一个方法来更新状态，然后将状态记录到控制台:

```
this.setState({ name: 'Matt' })
console.log(this.state.name)
```

您可能认为这会将`'Matt'`记录到控制台，但事实并非如此！它记录了`'John'`！

原因是，`setState`是异步的。这意味着当它到达调用`setState`的代码行时，它将开始状态更新，但是它下面的代码将继续执行，因为异步代码是非阻塞的。

如果您有需要在状态更新后运行*的代码，React 允许您提供一个回调函数，在更新完成后运行。*

更新后记录当前状态的正确方法是:

```
this.setState({ name: 'Matt' }, () => console.log(this.state.name))
```

好多了！现在，它可以像预期的那样正确记录`'Matt'`。

# 结论

你有它！三个常见错误以及如何改正。记住，犯错是可以的。你在学习。我正在学习。我们都在学习。让我们继续学习，一起变得更好。

*更新:想学习这些同样的概念，但是用函数组件和钩子？在这里看我的后续文章！*

[](https://medium.com/better-programming/3-mistakes-junior-developers-make-with-react-function-component-state-8a744ab99a0d) [## 初级开发人员在使用 React 函数组件状态时犯的 3 个错误

### “初级开发人员对 React 组件状态犯的 3 个错误”的后续

medium.com](https://medium.com/better-programming/3-mistakes-junior-developers-make-with-react-function-component-state-8a744ab99a0d)
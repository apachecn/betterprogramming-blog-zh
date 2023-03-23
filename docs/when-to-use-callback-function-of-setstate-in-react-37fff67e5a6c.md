# React 中何时使用 setState 的回调函数

> 原文：<https://betterprogramming.pub/when-to-use-callback-function-of-setstate-in-react-37fff67e5a6c>

当我第一次在 React 文档中读到 setState 时，我对自己说，“哦，这很简单，它只做一件事，改变状态，对吗？这就像改变一个变量的值。”

然后，在看到有人使用 setState 的回调后，我简单地忽略了它，因为我认为这只是一种奇特的做事方式。我想坚持我对 setState 的最初印象。

我甚至看到有人争论说这样写:

```
this.setState((prevState) => 
  ({ checked: !prevState.checked })
)
```

带来了比这更好的性能:

```
this.setState({ checked: !this.state.checked })
```

在我看来，他们两个没有太大的区别。第一个使用回调，setState 接受回调作为第二个参数，然后返回对象来更改状态。

# 使用 setState 的回调函数

当我在子组件中调用父组件的方法时，我遇到了一个问题。通过查看代码，您会有更好的想法:

我想在这里做两件事:

1.  更改当前子组件的状态
2.  传递一个参数，然后调用位于父组件中的 getCalendarData 方法。

上面的代码失败了，但是为什么呢？这是因为 setState 是异步的。getCalendarData 函数在当前月份更改之前触发。

## 破解它让它工作

这是可行的，但是上面的代码并不理想。我们不能确保所有设备上的时间总是 100 毫秒的延迟。

## 更好的实践

当我在回调中添加 getCalendarData 时，问题解决了。setState 有一个可选的第二个参数，它是一个回调函数！

另一种方法是使用 componentDidUpdate，如下所示:

我决定不使用这种方法，因为我的组件是以一种允许重用的[纯功能](https://hackernoon.com/react-stateless-functional-components-nine-wins-you-might-have-overlooked-997b0d933dbc)方式编写的。
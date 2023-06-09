# 避免 React 中不必要的渲染

> 原文：<https://betterprogramming.pub/avoid-unnecessary-renders-in-react-14e9dfeaf090>

## 优化您的 React 前端

![](img/59071fa7293865248854187664981b99.png)

React 的承诺是，当一个组件的道具或状态改变时，它将呈现这个组件。

虽然这很好，但我们可能不希望所有的更改都触发重新渲染。因此，我们需要处理好这些。我们将讨论一些我们能够并且应该防止重新渲染发生的情况。

# **只传递需要的道具**

不要把不必要的道具传递给组件，只传递需要的。
取两个兄弟组件，一个圆形组件，它在屏幕上显示动画，一个文本组件，它只输出圆形的半径，

问题是，即使`Info`不依赖于`xCenter`和`yCenter`，当圆圈在屏幕上移动时，对它的改变将再次被重新渲染，从而导致它被重新渲染。

为了解决这个问题，我们应该显式地只将需要的组件传递给子组件。`Screen`组件应修改如下。

这样，我们可以确保只将需要的道具传递给组件。这就是我喜欢 TypeScript 的原因，它会毫不犹豫地标记初始代码。

# **使用 React。避免重新渲染的备忘录**

无论如何，即使有了这些改进，我们仍然没有完成。

问题是，如果父组件由于属性或状态的改变而被重新渲染，子组件也可能被重新渲染，即使它不依赖于属性。

我们可以用记忆化来解决这个问题，这样可以确保组件只有在属性和状态改变时才被重新渲染。遵循[这个](https://dmitripavlutin.com/use-react-memo-wisely/)模式来明智地使用`React.memo`。

记忆的组件将如下所示:

现在要使用它，我们导入记忆版本。我们甚至应该记住父对象，以避免任何重新绘制，这样，如果父对象的父对象改变了，那么`Screen`将不会被重新绘制。尽管这取决于您的用例，但可能不会发生。

# **使用回调防止多次传递函数**

让我们介绍另一个组件，屏幕上的一个按钮，它只是用来处理一些点击来做一些非常酷的事情。我们也不要忘记记住它，这样我们就可以得到我们刚刚讨论过的很酷的特性。

现在，这里有一个陷阱。用于组件的变化检测是浅层的，它通常只比较参考变化。

例如，考虑:

```
const object1 = {a:1}
const object2 = {a:1}
object1 == object2
```

这会给你假的。这是因为这两个对象指向不同的引用，因此即使作为道具传递，它们也是不同的。

```
<MemoizedButton handleClick={() => {superClick(radius)}}/>
```

这里发生的事情是，无论何时呈现`Screen`组件，按钮仍将被重新呈现，只是因为新的`handleClick`将被重新分配。

为了解决这个问题，我们需要将函数封装在一个`useCallback`钩子中:

> 官方的 React 文档很好地解释了这一点:
> 
> useCallback 将返回回调的记忆化版本，该版本仅在其中一个依赖关系发生变化时才会发生变化。这在向依赖引用相等的优化子组件传递回调以防止不必要的渲染时很有用
> 【https://reactjs.org/docs/hooks-reference.html#usecallback 

# **现在让我们继续看列表**

总是使用唯一的键来避免重新呈现大的列表。没有一个键是不好的，但是使用一个有可能在渲染周期之间改变的键也是不好的，如果不是“更坏”的话。这里的目标是使用一个即使组件被重新呈现也不会改变的键。

例如，呈现文章卡片的列表，这可以是文章的 slug、文章的 URL、文章的数据库 ID(如果可用的话)。如果它是一个字符串列表，如果它们是唯一的，字符串本身可以被认为是键。

一旦键是惟一的，React 将知道只呈现列表中发生变化的项目，而不是在列表中发生变化时呈现整个列表。现在我知道这是很多，我将在另一篇文章中进一步解释。

用户资源来之不易，不使用是没有必要的。这是我关于优化 React 前端的系列文章的第一部分。感谢阅读。
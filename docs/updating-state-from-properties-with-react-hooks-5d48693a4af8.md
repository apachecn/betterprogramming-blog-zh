# 用 React 挂钩从属性更新状态

> 原文：<https://betterprogramming.pub/updating-state-from-properties-with-react-hooks-5d48693a4af8>

## 以及为什么不应该从 useEffect 依赖关系中改变状态

![](img/b3dc5913ce97d2fb5ac1bc257a7bbff4.png)

照片由 [Marko Aliaksandr](https://www.shutterstock.com/image-photo/white-radio-telescope-large-satellite-dish-1686580984) 为 [Shutterstock](https://www.shutterstock.com/) 拍摄。

我们有多少次想用组件属性中的数据来更新组件的内部状态？有各种各样的方法可以做到这一点，但有些方法也有陷阱。

# 问题是:道具并不总是管用

React 坚持认为[不更新状态以反映适当变化](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)，且有充分的理由。跟踪状态应该反映什么变得更加具有挑战性和复杂。这是受控组件背后的思想，其中受控组件更新父组件的状态，而不是试图同步两个组件。

完全受控组件的问题在于，有时候，组件内部的状态会变得非常复杂。比如说，父母可能只关心列出服务器数据库搜索的最终结果。理想情况下，我们会让搜索结果出现在一个全局状态存储中，比如 Redux 或 context，状态会简单地渗透到应用程序中需要的地方。但是有时候——特别是对于一个可重用的组件——如果能够封装搜索组件，读取`value`进行搜索，并通过调用`onSearch`属性返回结果就更好了。

这就是为什么我们中的许多人最终会在需要跟踪接收到的属性变化的组件中使用状态。

让我们看看我们可以从功能组件中的属性更新状态的一些方法，它们的优缺点，然后是我的首选方法。

# 使用 useEffect:请不要

我们多久见过这样的事情？

这看起来是一种简单而优雅的方法。每当`externalState`改变时，效果就会触发，更新`internalState`。三行，搞定。

除了它非常昂贵。

每当`externalState`发生变化，我们都不会听到，直到*整个应用程序的 DOM 生成之后。*然后我们调用`setInternalState`，这导致应用程序重新开始渲染阶段，并重新创建一个新的 DOM 结构。

为了你的用户，请不要这样做。我特意写下这篇文章，因为我已经看过太多遍了，不想因为把头发从头上扯下来而变得秃顶。

# 跟踪以前的状态值:有效，不太贵

React 为[提供了在新值到来时更新状态](https://reactjs.org/docs/hooks-faq.html#how-do-i-implement-getderivedstatefromprops)的替代方法。这是他们对`getDerivedStateFromProps`的半官方替代:

这种方法在 DOM 协调之前的渲染阶段完成所有工作。这使得它比最初的`useEffect`例子更有效。

# 最便宜的

[第三种选择](https://reactjs.org/docs/hooks-faq.html#how-to-get-the-previous-props-or-state)也来自 React 文档。他们把它描述为一个潜在的未来`usePrevious`钩子，它看起来很有希望。如果我们想用它来更新状态，它应该是这样的:

在这种情况下，我们没有使用`useEffect`来触发变化。因为我们正在更新一个引用，而不是调用一个`setState`函数，所以当`previousValueRef`被更新时，没有重新渲染发生。当组件下一次被调用时，提供的`value`与存储的`previousValueRef.current`进行比较，如果它们现在不同，我们更新`internalState`。

这种方法奏效的原因有两个:

1.  `useEffect`钩子没有依赖数组。这意味着它将在每次渲染后运行，从而使`usePreviousRef`始终保持最新。
2.  我们只在需要时才设置内部状态，因为以前的值和内部值都与提供的值不匹配。如果我们只检查先前的值，我们会因为每次更新内部状态而陷入无限的渲染循环。

*注:老实说，这让我很困惑。尽管文档上说* `*setState*` *函数如果接收到与它们包含的值相同的值，就不应该强制重新渲染，但我还是陷入了一个无限的渲染循环，即使是在调用像* `*setState(false)*` *这样简单的函数时，也没有先检查* `*false*` *。如果有人知道为什么会发生这种情况，我很乐意在回复中听到。*

就性能而言，第二种和第三种方案实际上是相同的。主要区别在于，第二种选择在呈现时完成所有工作，而第三种选择在组件呈现在页面上后完成部分工作。只有用新值重新呈现组件，而不是一次更新两个单独的状态元素，才会发生更新。这是次要的，但感觉像一个很好的微优化。

是的，过早的优化是代码维护的死亡。在这种情况下，我在 React dev 团队的成员提出的两个建议中进行选择，因此我不会为此失眠。

# 我个人的偏好:useUpdatableState

我更喜欢第三种选择，因为从状态改变的角度来看，它对我来说更便宜。但是就代码可读性而言，它有点杂乱，而且没有解决我的一个主要抱怨:我不能选择什么构成了状态的必要改变。当我想更新我的状态时，它基于严格的等式更新，只有当满足某些标准时。

输入`[useUpdatableState](https://www.npmjs.com/package/@landisdesign/use-updatable-state)`:

```
import useUpdatableState from '@landisdesign/use-updatable-state';const [state, setState, changed] = useUpdatableState(value);
```

它本质上是对`useState`的替代，除了它基于新的输入值进行更新。只要传入的值不变，它的返回状态就基于传递给`setState`的任何内容。当一个新值出现时，`state`被更新。`changed`参数告诉我`state`何时反映输入值，而不是我一直在修改的值，以防我需要对其他状态进行更改或触发副作用。

更重要的是，它允许我在第二个参数中提供一个谓词，比如:

在这个例子中，参数`(a, b) => a.id === b.id`告诉`useUpdatableState`如果 id 相同，我不应该基于那个外部值更新我的内部状态。这与`react-redux`的`useSelector`钩子所做的类似，让我们挑选什么值得强制重置我们的状态。

# TL；DR:不要用 useEffect 依赖关系改变状态

这才是这篇文章真正要讲的。这里至少有两种选择，它们不会强迫你的用户为编写漂亮但低效的代码付出代价。你还有其他的吗？请在下面的评论中留下它们。

感谢阅读！
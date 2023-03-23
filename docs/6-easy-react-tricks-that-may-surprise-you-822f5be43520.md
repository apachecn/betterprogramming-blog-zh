# 6 个让你吃惊的简单反应技巧

> 原文：<https://betterprogramming.pub/6-easy-react-tricks-that-may-surprise-you-822f5be43520>

## 带有片段的键、作为 HTML 元素的字符串等等

![](img/cca8b915e1d9d77f5d6b2c6981dfd1d8.png)

由 [Baher Khairy](https://unsplash.com/@baher366?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

大部分时间我和 React 一起工作。随着时间的推移，我遇到了一些我以前从未想过的简单事情，这让我真正“啊哈！”时刻。我也注意到许多和我一起工作的人并没有意识到这些小技巧——可以让他们开心的简单事情！

下面所有的例子都是围绕功能组件和钩子构建的。

# 用碎片反应键

有时你需要在列表中呈现多个组件。如果不需要容器，使用 React Fragment。这里有一个例子:

上面的代码是完全合法的，但 React 将开始抱怨键:

```
Warning: Each child in a list should have a unique "key" prop.
```

你看到了错误，然后说“是啊，管它呢。”然后你用一个`div`替换这个片段。

事实上，你的思路是正确的——你只是错过了一个你可能没有想到的棘手问题。你*可以*使用带有 React Fragment 的键，但是不能使用上面看到的语法。您必须用`<React.Fragment>`改变语法，然后就可以开始了:

# HTML 元素形式的字符串值

有时你想创建一个组件，它可以是一个灵活的 HTML 元素。或者你可能已经看过来自 CSS-in-JS 库中的`as` prop，比如`emotion`。

假设您想要创建一个可以呈现为`button`或`a`的`<Button>`组件。有哪些选择？你可以抽象样式，用它创建两个不同的组件，或者你可以只创建一个组件，用`React.createElement`和`as`属性:

这对于一个简单的组件来说很好，但是如果有另一种看起来感觉更好的方式呢？这是 JSX 的诀窍:

是的，你可以在 JSX 使用一个字符串作为组件——只要确保字符串组件名称以大写字母开头。

# 将函数传递给 setState

`useState`和`useEffect`大概是使用频率最高的钩子了。您需要将您的依赖项传递给`useEffect`，或者预期一些错误或不想要的效果，但是，如果您的依赖项只是一个与相对`setState`一起使用的状态，也许您不需要传递它。先来看不太好的版本:

有一个简单的方法可以摆脱这种情况:使用功能更新形式的`setState`，就像这样:

```
const [count, setCount] = useState(0)setCount(c => c + 1)
```

下面是更新后的示例:

# 用 useReducer 实现 useState

这是我在推特上听说的一个有趣的小把戏。虽然没有真正的好处，但了解一下`useReducer`的能力还是有好处的。

如果你直接从`useReducer`返回动作，那么它的行为几乎和`useState`一样。所以你可以争辩说`useState`没有必要。下面是推文:

下面是您可以复制粘贴并试用的代码:

```
const [name, setName] = useReducer((_, value) => value, 'James');<input value={name} onChange={e => setName(e.target.value)} />
```

# 手动重新渲染组件

您是否曾经需要手动重新渲染组件？例如，你需要重新渲染一个组件，但是没有状态或者任何你可以触摸的东西。比方说，出于某种奇怪的原因，你想在一个按钮被点击时这样做。

你可以这样做:

```
const [, rerender] = useState()rerender({})
```

就是这样！你使用`useState`但是你实际上并不需要状态本身。你只需要`setState`函数或者`rerender`函数来重新渲染。重要的是，每次它像一个空对象或数组一样运行时，你都需要传递一个新值。

# 如果没有直接的属性/状态依赖，将对象或函数移出组件

我经常看到这个错误。为了描述，首先，让我们看一下代码。

这个想法很好——比`if/else`或`switch`案例好得多。但是有一个问题。每次这个组件被重新渲染，一个新的`cardProps`对象被创建。即使没有任何变化，它也会导致所有依赖组件的重新渲染。

为了解决这个问题，我看到使用了`useMemo`:

`useMemo`解决了问题，但一切都有代价。如果您仔细查看代码，您会发现没有理由将`cardProps`对象放入组件中。自然，也不需要`useMemo`开销，因为该对象不直接依赖于任何道具或状态。即使它来自外部，您仍然可以使用`...cardProps[types]`。

如你所见——不需要挂钩。如果不需要把东西放在里面，就把它们搬到外面去，功能也是一样。

# 结论

每当你认为你已经学得足够多的时候，你会发现令你惊讶的事情——学习是没有止境的。

所以，继续学习，惊喜吧！

# 推荐故事

[](https://medium.com/better-programming/should-you-use-recoil-as-a-react-state-management-library-38ef14f3de10) [## 你应该使用反冲作为一个反应状态管理库吗？

### 看一看脸书开发的新的反应状态管理库

medium.com](https://medium.com/better-programming/should-you-use-recoil-as-a-react-state-management-library-38ef14f3de10) [](https://medium.com/better-programming/using-css-modules-with-react-in-2020-73c2223f495c) [## 将 CSS 模块与 React 一起使用

### 有时候纯 CSS 是更好的选择

medium.com](https://medium.com/better-programming/using-css-modules-with-react-in-2020-73c2223f495c) [](https://medium.com/better-programming/master-usestate-in-imperative-and-declarative-ways-e298f6d9c352) [## 以命令和声明的方式掌握使用状态

### 了解如何以多种方式使用 useState 来获得最佳结果

medium.com](https://medium.com/better-programming/master-usestate-in-imperative-and-declarative-ways-e298f6d9c352)
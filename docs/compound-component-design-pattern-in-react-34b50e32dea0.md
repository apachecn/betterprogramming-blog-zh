# React 中的复合组件设计模式

> 原文：<https://betterprogramming.pub/compound-component-design-pattern-in-react-34b50e32dea0>

## 避免正确训练，并编写具有隐式状态共享的表达性组件

![](img/625f0a22a7592cf01bc026f12ae055b4.png)

照片由 [Danist Soh](https://unsplash.com/@danist07?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 什么是复合组件？

复合组件是一种 React 模式，它为父组件与其子组件通信提供了一种富有表现力和灵活性的方式，同时以富有表现力的方式分离了逻辑和 UI。

在复合组件中，我们不是通过 props 传递状态，而是将元素作为子元素传递给父元素。

那么我们实际上在谈论什么:

```
<select name="places" id="places-select">
    <option value="paris">Paris</option>
    <option value="boston">Boston</option>
    <option value="london">London</option>
</select>
```

在 HTML 中，我们可以将选项作为子元素传递给`<select>`元素。select 元素管理状态，我们清楚地看到哪些元素作为选项传递给 select。

在 React 中，我们可以做非常类似的事情，将子元素作为元素传递，而父元素管理隐式状态。

# 在 React 中实现复合组件模式

让我们从一个简单的卡组件开始。我想在卡片上呈现链接，如果有很多链接，限制显示的数量，并有一个`show more` `show less`切换来扩展或折叠卡片以显示链接。

下面是我的简单组件的样子:

我的组件接受内容作为道具，并为我的内容对象中的每一项呈现一个`<CardItem />`。

组件以折叠状态开始，这导致它只呈现三个链接(我设置了三个`LIMIT`)。但是我也有一个切换功能，如果点击`show more`，折叠状态会改变，所有的链接都会呈现出来——`show more`变成`show less`。

很简单，对吧？但是如果这个组件有多个用例呢？如果我想重用这种切换显示更多/更少的功能，但每次在不同的卡上使用不同的 UI，该怎么办？

以我目前的组件，我不具备这样做所需的灵活性。我的功能与 UI 相结合。

如果我需要一个与我的`<CardItem />`不同的组件用于链接，我将不得不传递一个道具来控制不同的情况，或者如果道具的数量变得太复杂，甚至可能创建一个新的组件。

这就是我们的复合组件的用武之地:

这里我们从一个 Card 组件开始，管理一个`isCollapsed`状态，就像我们之前的组件一样，但是现在我们使用上下文 API 来存储值。

我们还使用两个函数来展开和折叠，并把它们放在卡片上下文中。我们的组件接受的唯一道具是`children`，我们在一个卡片 div 中呈现我们的上下文提供者中的子元素。

接下来，我想为我的卡片内容创建一个组件，以及一种方法来控制我用于展开和折叠的元素。

`CardContent`映射到其子节点，并控制有多少子节点(在本例中是链接)被渲染。

`Expand`和`Collapse`组件使用来自`Card`组件内部的`expand`和`collapse`函数，以及从`CardContext`中提取的`isCollapsed`布尔值。

然后，两个函数都使用 React 的`cloneElement()`方法返回一个元素，该方法将克隆作为子元素传递给组件的任何元素，并使用给定的配置返回它。在这种情况下，一个具有指定功能的`onClick`。(点击阅读更多关于 React 的克隆元素[。)](https://reactjs.org/docs/react-api.html#cloneelement)

最后，我们导出我们的`Card`组件及其所有组件。

这种技术非常明确，让组件的用户清楚地看到每个方法在做什么。

现在我们终于可以使用我们的组件了！

在`Card.CardContent`、`Card.Expand`和`Card.Collapse`中，我们可以将任何元素作为子元素，它们将始终拥有组件的功能。

这提供了使用此卡的折叠和扩展功能的灵活性，几乎任何组件，它完全解耦！

# 结论

可重用性是 React 的关键，了解您手头有哪些工具可以让开发变得更加有趣和容易。

我希望这篇教程能让你对如何使用复合组件以及它们能给项目带来的价值有所了解:)

请务必在此查看该项目的 code sandbox[。](https://codesandbox.io/s/compound-component-card-example-ht8xg)
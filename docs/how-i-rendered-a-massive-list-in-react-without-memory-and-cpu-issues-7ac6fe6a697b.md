# 我如何在没有内存和 CPU 问题的情况下在 React 中渲染一个巨大的列表

> 原文：<https://betterprogramming.pub/how-i-rendered-a-massive-list-in-react-without-memory-and-cpu-issues-7ac6fe6a697b>

## 没有延迟，没有滞后——以下是我是如何做到的

![](img/c56860f25020a4ab20f799e4a26a5fb2.png)

# 庞大的名单

我曾经遇到过这样一种情况，我不得不在一个列表中呈现大量的条目。每当我想呈现一个巨大的列表或表格时，React 中所面临的问题让我感到绝望。

根据我计划呈现的列表项和列表的长度，我面临以下问题:

1.  呈现列表花费太多时间(CPU 问题)
2.  滚动时有很大的延迟(内存问题)
3.  整个应用程序变得缓慢

# 我尝试过但没用的东西

我意识到造成延迟的不是大量的元素，而是呈现的 DOM 元素。

所以我想我应该把数组分成多个块，先渲染一个块。每当用户点击列表的底部，我会呈现下一个。

但这并不奏效。用户体验不好。当你到达底部时，我还在渲染下一个块，中间有一个延迟。尽管它正在杀死 UX，我用一些黑客修复了它。但实际问题是这样的:每当我添加一个块时，呈现列表的大小都会增加，用户在滚动时会面临同样的延迟问题。

我的第二个尝试是:每当我添加一个数据块时，我会删除前一个。是这样吗？是的，它扩展并解决了内存问题，但它杀死了 UX。每当我滚动时，滚动条上下移动，因为当我移除前一个块时，项目的位置发生了变化，我觉得应该出现在列表中的项目正在消失。

# 我最终是如何解决的

从以前的尝试中得到的教训是，当用户滚动时，元素的位置永远不应该改变，只呈现视图上可见的内容可以解决 CPU 和内存问题。

嗯，我唯一的选择是将数组分成不同的块，并为每个块放置一个容器，而不是直接呈现项目。当我移除容器中的物品时，如果它不在视图中，就有必要保持每个容器的高度。

这个问题基于数组的分割和每个块的大小。大小很重要，因为如果数组中有 10，000 个元素，如果我的块大小是 10，那么我将呈现 1，000 个容器 div。

这时我想，“为什么我不能在这里实现二叉树？”我递归地将数组一分为二，直到每个块都有最少数量的元素。这样，如果父容器在视图中不可见，那么它的所有子容器将被自动移除，同时保持每个元素的位置。

# React 中的实现

一旦我明白了逻辑，实现它就相当简单了。我不打算在这里解释所有的代码，因为那样太冗长了。取而代之的是，我将简单地引导你通过这个流程。

让我们把你的清单当作一个组件。当数据列表被提供给列表时，数据将经历这些过程。

1.  我们将获取列表数据并递归地分割它，直到一个块达到最少的条目，然后我们将存储它。
2.  可见性检查:在处理过的数据中，我们将基于列表组件的`scrollTop`通过递归循环来更新每个容器的可见性。我本可以只在第一步中完成，但是我之所以要把它分开，是因为每当有滚动事件触发时，这个函数就会被调用。
3.  我们将把可见性检查函数绑定到列表组件的`onScroll`。

仅此而已。现在，我们有了 list，它能够在没有内存和 CPU 问题的情况下呈现大量的列表项。

# 工作演示和 NPM 图书馆

我认为让别人写我已经写好的代码是没有意义的，所以我创建了一个 NPM 库并主持了一个工作演示。

[工作演示](http://react-eternal-list.rinas.in)

NPM 图书馆:[React-永恒列表](https://www.npmjs.com/package/react-eternal-list)

# 结论

二叉树方法非常有效。我尝试了多达 100，000 个元素，它仍然工作顺利。即使我花了一些时间来实现这一点，这是值得的努力。即使它只是一个列表，学习曲线也很好，现在我可以在未来的 UI 开发中使用这种方法。

感谢您的关注。你有不同的意见吗？我总是很高兴得到反馈。
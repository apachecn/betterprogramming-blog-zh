# 在 React 中构建高性能无限列表

> 原文：<https://betterprogramming.pub/build-high-performance-infinite-lists-in-react-a53c0899a211>

## 理解和解决常见问题

![](img/43e8b92e22332bf6300c4c345836d791.png)

在 [Unsplash](https://unsplash.com/photos/Mkh2La9fEDY) 上由 [Rohit Tandon](https://unsplash.com/@rohittandon) 拍摄的照片

互联网上充斥着使用无限滚动的产品。无限卷轴让信息的消费变得非常容易，并且极易上瘾。带有时间线或订阅源的产品，如 Twitter、Instagram 等。最适合无限卷轴。

在 JavaScript 中实现无限滚动可能很有挑战性。当你的提要有数千个条目时，问题就变得更加复杂了。让我们来看看其中的一些问题。

# 问题

*   渲染速度慢，因为在调整大小时，浏览器必须重新绘制所有元素
*   滞后滚动
*   页面上成千上万的 DOM 元素会导致浏览器崩溃

大多数设备每秒刷新屏幕 **60 次**。这些帧中的每一帧的预算都刚刚超过 16 毫秒(1 秒/60 = 16.66 毫秒)。当你不能满足这个预算，帧速率下降，内容在屏幕上抖动。你可以使用 Chrome 中的 [FPS 计](https://developer.chrome.com/devtools/docs/rendering-settings)来检查网页的刷新率。当你在有这么多 DOM 元素的页面上滚动时，FPS 肯定会低于 60。

# 我们能做什么

我们必须减少元素并处理滚动问题。以下是解决这些问题的一些基本思路:

*   **DOM 回收**:想法是只渲染可见的元素。我们可以重用它们来呈现新的项目，而不是创建新的项目。
*   **滚动锚定**:由于 DOM 中只有 10 个元素，我们需要伪造滚动来给人无限滚动的错觉。

这些需要大量的计算和拐角条件来有效地实现它。在阅读这些问题时，我遇到了一个 [react-virtualized](https://github.com/bvaughn/react-virtualized) 包，它为所有这些问题构建了解决方案，并由 [Dan Abramov](https://twitter.com/dan_abramov/status/716369503291105283) 推荐。

# 虚拟化的工作原理

React-virtualized 通过一组组件实现虚拟渲染，这些组件完成相同的任务:

*   它们计算在列表显示区域内哪些项目是可见的。
*   他们使用一个具有相对位置和绝对位置的容器，通过改变容器的宽度、高度、顶部和左侧属性来确定内部子元素的位置。

我们将使用以下组件来呈现具有动态宽度的列表以及动态宽度和高度的项目:

*   **列表:**该组件呈现一个元素列表。它负责虚拟化列表并只呈现可见的项目。
*   **CellMeasurer** :它通过以用户不可见的方式临时呈现单元格来自动测量单元格的内容。指定一个固定宽度来测量动态高度(反之亦然)。
*   **CellMeasurerCache** :存储`CellMeasurer`个测量值，并与父节点(`List`)共享。
*   **AutoSizer** :自动调整单个子的宽度和高度的高阶组件。
*   **InfiniteLoader** :当用户在列表中上下滚动时，它管理数据的即时获取。它还缓存列表数据，以避免在用户滚动时再次获取它。

让我们用它们来建立一个真正的列表。

您可以通过 [**文档**](https://github.com/bvaughn/react-virtualized/tree/master/docs#documentation) 来了解这些组件的工作原理以及不同可用道具的含义。

# 缓存问题

如果您基于一些状态变化重新呈现列表，您可能会因为`CellMeasurerCache`和`InfiniteLoader`所做的缓存而面临问题。可以使用内置方法清除缓存。

```
// Reset cached measurements for all cells.
this.cellMeasurerCache.clearAll();// Reset any cached data about already-loaded rows
this.infiniteLoaderRef.resetLoadMoreRowsCache();// Recompute row heights and offsets
this.listRef.recomputeRowHeights();
```

# 结论

我希望这有助于你理解实现大列表的问题以及如何处理它们。它提供了对 react 虚拟化包的基本理解。该软件包提供了许多其他组件来解决大型动态表格、网格等问题。布莱恩·沃恩也推荐[反应窗](https://github.com/bvaughn/react-window)作为一个可能的轻量级替代方案。

在评论中分享你的使用案例和你所面临的无限列表的问题。谢谢！
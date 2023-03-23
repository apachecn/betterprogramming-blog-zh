# WebGL 增强的 Drag Slider Tutorial With curtains . js—第 3 部分

> 原文：<https://betterprogramming.pub/webgl-enhanced-drag-slider-tutorial-with-curtains-js-part-3-4ea0791b4a54>

## 通过移除任何布局重画调用来提高滑块的整体性能

![](img/4be96d471e3ede5017613e07afbfe95d.png)

这篇文章是我们教程的第三部分，也是最后一部分。在第一部分中，我们创建了一个 JavaScript 拖动滑块。在[第二部](https://medium.com/better-programming/webgl-enhanced-drag-slider-tutorial-with-curtains-js-part-2-bf32aa5a15c0)中，我们添加了 [WebGL](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API) 和 [curtains.js](https://www.curtainsjs.com/) 来改善动画效果。如果你还没有这样做，请检查它们。

在这最后一部分，我们将看到如何通过移除任何布局重画调用来提高滑块的整体性能。

这是最后一支笔:

作为提醒，我们使用了 curtains.js 来添加与 WebGL 相关的所有内容。

curtains.js 是一个开源的普通 JavaScript 库。它在这里特别有用，因为它的主要目的是用 WebGL 效果增强 DOM 元素。

通过几行 JavaScript，您能够创建 WebGL 纹理平面，绑定到我们的滑块项目的 HTML 元素，然后对整个场景进行后期处理。

这样我们就可以看到使用窗帘的好处了。

*   干净和搜索引擎友好的 HTML 代码。
*   你不必担心你的 WebGL 对象的大小和位置，因为大多数东西(比如调整大小)将由库在幕后处理。
*   在任何情况下，如果在 WebGL 初始化期间或在您的着色器中出现错误，滑块仍将工作！

# 布局重画调用/回流

## 总体概念

首先，你需要理解什么是布局重绘和回流，以及它们的原因。GitHub 上有一篇 Paul Irish 写的简洁的文章解释了这一点。

基本上，每次你使用 JavaScript 方法获取 HTML 元素的样式或位置(比如`element.getComputedStyle()`或`element.getBoundingClientRect()`，或者每次你试图通过`window.scrollY`访问窗口滚动值，浏览器都需要重新绘制整个网页来计算这些值。

这可能导致性能瓶颈，并可能导致 jank 问题。

## 使用窗帘. js

事实上，每次你添加一个平面，或者每次你调整窗口的大小来复制平面的 HTML 大小和位置，并将其应用到相关联的 WebGL 平面对象时，currents . js 都会在内部使用`getBoundingClientRect`。

这是无法避免的，但是，幸运的是，这并不经常发生，所以这并不是什么大不了的事情。

但是，我们在滑块的`onTranslation`钩子中使用了`updatePosition`方法，这个函数也调用了`getBoundingClientRect`，因此触发了一个回流调用。

我们现在将看到如何改进我们的滑块，以摆脱那些布局重画计算。

# 另一种方法来翻译我们的飞机

## 使用 setRelativePosition 方法

本库提供了另一种翻译平面的方法:`[setRelativePosition](https://www.curtainsjs.com/plane-class.html#set-relative-position)`[函数](https://www.curtainsjs.com/plane-class.html#set-relative-position)。

它以像素为单位获取 X 和 Y 值作为参数，并通过更新其模型视图矩阵将其应用到您的平面。

# 用例

通常，当您使用 currents . js 时，您将使用滚动事件监听器中的`updatePosition`来根据窗口滚动值更新您的平面的位置。

因为没有办法在不触发 reflow 调用的情况下获取滚动或 HTML 元素的位置，所以这仍然是在使用原生滚动时处理平面位置的最佳方式。

但是，如果你正在使用一个虚拟的滚动库，例如，你将能够访问滚动值的计算，而不会引起回流调用，你将能够相应地更新你的飞机的位置，用我们的`setRelativePosition`方法！

现在，回到我们的滑块。在任何给定的时间，我们都可以访问它的当前翻译(它是在不触发回流的情况下计算的)，那么为什么不用它来更新我们的位置呢？这正是我们要做的。

# 这个想法

总体概念相当简单:我们将使用`setRelativePosition`函数将我们的翻译应用于平面。

因为每次我们调整浏览器窗口大小时，我们的飞机位置会自动更新，所以我们必须在那里重置它们的相对位置。

通过这样做，我们将打破滑块的当前平移和我们的相对位置值之间的同步。然后，我们必须跟踪滑块先前的平移，并将这些值减去我们的相对位置。

滑块和 WebGL 现在再次同步！

# 更新代码

首先，我们将向 WebGL slider 类添加一个`previousTranslation`属性。

让我们更新我们的`WebGLSlider`类构造函数:

接下来，我们必须更新我们的`onTranslation`钩子来使用`setRelativePosition`函数，而不是之前的`updatePosition`函数:

我们需要做的最后一件事是重写我们的`onSliderResized`钩子来更新我们的`previousTranslation`属性值，重置我们的平面的相对位置，我们就完成了！

这就是所有的人！

现在，您已经准备好在您的网站上使用 curtains.js，并且对性能的影响最小。

您可以尝试更改着色器过程片段着色器中的一些值，以稍微调整效果。你也可以尝试使用不同的位移图像(记住，你必须使用一种模式来获得无缝效果)。

下面是一个额外的示例，其中包含不同的置换图像和着色器中的一些变化:

# 结论

我希望你喜欢这篇文章，并发现它很有用。我还希望您看到了使用 currents . js 通过整洁的 WebGL 效果来增强您的 UI 是多么容易。

看看[库的文档](https://www.curtainsjs.com/documentation.html)和[例子](https://www.curtainsjs.com/#examples)或者查看它的 [GitHub repo](https://github.com/martinlaxenaire/curtainsjs) 如果你想知道更多。

要有创意！
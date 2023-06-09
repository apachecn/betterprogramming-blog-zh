# 如何用 JavaScript 重启 CSS 动画

> 原文：<https://betterprogramming.pub/how-to-restart-a-css-animation-with-javascript-and-what-is-the-dom-reflow-a86e8b6df00f>

## 通过使用 DOM 重排/布局事件

![](img/a3e18dc7963ab5abf22ae7ce9b0d3f8e.png)

不幸的是，通过添加和删除类来重启动画并不像我想象的那么容易

复杂的 CSS 动画可能是一件痛苦的事情，因为没有真正的方法来调试它们，而且，如果你曾经将它们与一些 JavaScript 特殊的酱混合在一起，那么你肯定处于我的位置。

是的，那个令人沮丧的地方，没有一个开发者愿意去。经过长时间的研究和调试，尝试了各种方法，却完全不知道如何修复它。

如果你正在读这篇文章，你是一个网页开发者，你知道我的意思。但是别担心，我的朋友！在这篇文章中，我将向你展示一个简单的修复方法来使用和正确重启 CSS 动画，这样你的酷动画*就能保持*酷。

但首先，让我问你几个问题，以确保你正在阅读正确的文章，而不是浪费你的时间。

# 你是:

*   尝试同步各种元素上的 CSS 动画？
*   在用户事件上重启动画？(鼠标悬停时重启按钮动画。)

如果你至少对其中一个问题回答“是”，那么这篇文章就适合你，希望你会发现它很有用。

因此，在经历了多次这种可怕的情况后，我的好奇心和结束那些永无止境盯着屏幕的时间的决心，我收集了一些正确重启动画的可能修复方法。

但是，我将向您展示一个最简单、最容易添加到代码中的方法。事不宜迟，让我们直接进入正题。

# 删除和添加类不会成功

删除和添加类不会成功。抱歉。

但是你很接近了！如此接近，以至于您只缺少一行神奇的代码。

```
void element.offsetWidth;
```

然而，要理解为什么这一行会解决这个问题，我们首先必须确定我们知道什么是 DOM reflow 或 layout**T3。**

# DOM 回流

简单地说，DOM reflow 是一个用户阻塞操作，它计算文档的布局。元素上的重排是对其在文档中的尺寸和位置的计算。

有许多方法可以在浏览器中触发 DOM 重排操作。要获得详尽的列表，请查看这个[要点](https://gist.github.com/paulirish/5d52fb081b3570c81e3a)。

为了简单起见，我将只向您展示一种方法，但是您可以随意选择最适合您需求的任何方法。

# ***性能危险***

责任重大！请记住，DOM reflow 可能是一个昂贵的操作，需要大量的 CPU 能力，并且因为它是一个[阻塞操作](https://kellegous.com/j/2013/01/26/layout-performance/)，它可能会导致不必要的、糟糕的用户体验。

确保你在整个网站中最大限度地减少回流/布局事件。多了解它，知道如何控制它，会让你成为一个真正的 dev 忍者。

现在，回到我的观点。使 reflow 对重新启动 CSS 动画有用的是这个计算过程，它更新 DOM 中元素的属性，然后它们被重新初始化。

也就是说，动画将再次运行*，就像动画元素第一次被渲染一样。*

*酷！现在让我们看看*如何在*时和*触发 JavaScript 代码中的回流事件。**

# *触发 DOM 回流*

*我们可以简单地通过请求元素的宽度或高度来触发 DOM reflow:*

*为什么是`void`？因为我们不关心返回值，我们只想请求元素的尺寸，这将触发一个回流事件。*

# *重新启动 CSS 动画*

*为了正确地重新启动 CSS 动画，您最好在移除和添加触发动画的 CSS 类之间触发一个回流事件，如下所示:*

## ***普通 JavaScript***

## ***jQuery***

*这指示浏览器重新计算元素的宽度，从而导致 DOM 中的回流。reflow 事件触发动画从头开始运行。*

*我建议在树中尽可能低的位置触发回流*以最小化可能在子元素上触发的后续回流。**

# *结论*

*我希望你有一个伟大的阅读，并找到了你正在寻找的修复。让我知道这是否对你有用，以及你用来解决这个问题的其他酷方法。*

*我几乎没有接触过 DOM 回流/布局的东西，所以如果你有兴趣了解更多关于如何最小化它的知识，请查看我的另一篇[文章](https://medium.com/better-programming/web-performance-dom-reflow-76ac7c4d2d4f)， *Web 性能:最小化 DOM 回流/布局抖动**

*祝你今天开心！玩的开心！*
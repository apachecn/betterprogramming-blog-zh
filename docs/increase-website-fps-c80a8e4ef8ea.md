# 网页优化:通过禁用悬停效果增加 FPS

> 原文：<https://betterprogramming.pub/increase-website-fps-c80a8e4ef8ea>

## 当用户滚动时，下层浏览器重新绘制

![](img/5890232ca5c451fa408c1aa792e89fbf.png)

YorKun Cheng 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这种优化技术很容易理解和实现。因此，在这篇文章中，我将带你去公园散步，并向你展示另一种可以提高网站性能的方法，希望你从未听说过。

以下技术很有用，因为它最小化了 [DOM reflow/layout](https://medium.com/better-programming/web-performance-dom-reflow-76ac7c4d2d4f) 和 paint 事件，从而提高了帧速率。

# 当用户滚动时禁用悬停效果

这里的目标是最小化浏览器回流和重绘事件，以提高帧速率。这是通过在用户滚动时从 DOM 中的所有元素中移除`hover`效果来实现的。

让我在我的下一个观点中详细说明为什么和在哪里这是有用的。假设您的页面上有这个 CSS 规则:

```
.color-me:hover {
  background-color: blue;
}
```

当用户的光标悬停在它上面时，这个规则将被应用，不可避免地，当滚动页面时。然后，当元素的颜色发生变化时，它会在浏览器中触发一次重画，因此它会在具有类似 CSS 规则的每个其他元素上进行重画。

Ryan Seddon 的精彩演示很好地解释了性能提升和优势。

# 解药

## 1.滚动事件监听器🖱

为了提高网站的帧速率，我们会在`window`全局对象上附加一个`scroll`监听器。

每当有一个滚动，我们清除之前的超时，并将`disable-hover`类添加到 body 元素，这样它就在整个树中生效了(虽然还没有…)。

最后，我们设置另一个超时，如果在 500 毫秒(`ENABLE_HOVER_DELAY`)内没有调用`scroll`事件，这个超时将删除`disable-hover`类。

您可以尝试不同的`ENABLE_HOVER_DELAY`常量值，看看哪个最适合您的网站，但我发现 500 毫秒是最佳值。

## 2.禁用所有指针事件的 CSS 规则

最后，我们需要为我们的侦听器设置这个 CSS 规则，以便在滚动时对我们的网站产生实际影响。

```
.disable-hover,
.disable-hover * {
  pointer-events: none !important;
}
```

上述规则将忽略所有 HTML 元素上的所有指针事件。
一般来说，当滚动时，用户不能执行任何鼠标事件(点击、悬停、拖动),所以请确保您了解这一点的含义。

确保你理解网站用户体验的变化，并且不伤害网站的实际设计或期望的功能。

# 结论

就这样。这是一个快速简单的修复方法，你现在可以添加到你的网站上，通过提高滚动时的帧速率来优化速度。希望这对您有所帮助！
# 理解 JavaScript 中的交叉点观察器 API

> 原文：<https://betterprogramming.pub/understanding-intersection-observer-api-in-javascript-bb1bf04b8081>

## 只用几行字，你就可以极大地改善 UX

![](img/9023c67729f8b92c4f473f6b8bfdcea9.png)

奇迹发生在十字路口。 [Denys Nevozhai](https://unsplash.com/@dnevozhai?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/intersection?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片。

在学习 [Gatsby](https://www.gatsbyjs.org/) 如何预取链接以给出惊人快速导航的外观时，我遇到了[十字路口观察者 API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) 。我推迟了几个月去调查这件事，但最后还是在上周末一头扎了进去。

# 什么是交叉点观察器 API？

简而言之，它使您能够检测元素的可见性，即它是否在当前视窗中，以及两个元素之间的相对可见性。

# 用例

*   图像的延迟加载。
*   无限卷轴。
*   当链接出现在屏幕上时预取链接(Gatsby 就是这样做的)。
*   检测广告是否被浏览(更多跟踪)。
*   确定用户是否阅读了一篇文章以及阅读到什么程度(想想中等浏览和阅读统计)。
*   仅在屏幕上可见时运行昂贵的渲染和动画。

# 技术方面的东西

像往常一样，MDN 上的[文档很棒，但是我在弄清楚一些事情时遇到了一些麻烦，因此我在这里分享我的笔记。](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)

交叉点观察器 API 的核心是下面两行:

```
var observer = new IntersectionObserver(callback, options);
observer.observe(target);
```

1.  首先，用一些`options`创建一个`Observer`。
2.  然后，你让`Observer`启动`observing`一个`target`。
3.  当期望的`intersection`发生时，您的`callback`函数被调用。

# 选项

这是很难理解的部分。在浏览选项中的设置之前，您应该牢牢掌握以下内容:

```
**Intersection Observer API** lets you do something in the **callback function** when it **observes** an **intersection** (beyond a certain **threshold**) between the **root** element and the **target** element.
```

1.  把`root`想象成外部的矩形，或者你想观察相交的矩形。
2.  `target`是你正在观察的元素。
3.  `threshold`给出重叠发生的程度。
4.  `rootMargin`是在计算相交范围之前应用于根的余量。

# 经验法则

1.  API 是异步的，所以当交集发生时，使用`callback`函数做一些事情。
2.  使用 CSS 选择器可以决定`root`元素。
3.  如果指定`root: document.querySelector(‘null’)`，用户的当前视口将是`root`元素。
4.  摆弄`threshold`和`rootMargin`以获得您期望的确切行为。

这几乎是交叉点观察器 API 的核心概念。更多阅读请参见 [MDN 的文档](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)。

# 学习输出

在学习这个 API 的时候，我想看看我是否能创造一些有用的东西。所以，我只写了几行，创建了一个 Chrome 扩展。该扩展用屏幕上可见的最新部分的 ID 来更新 URL。

因此，当您与朋友分享更新后的网址时，他们可以直接转到您正在阅读的部分。不到一个小时就写了几行，很酷吧？

[](https://chrome.google.com/webstore/detail/microcontext/jichdnfbbcfidobafaolhcefednocbgn) [## 微上下文

### 共享特定内容部分的链接

chrome.google.com](https://chrome.google.com/webstore/detail/microcontext/jichdnfbbcfidobafaolhcefednocbgn) 

# 完整代码

这个扩展的代码不到 30 行。

如果你想使用它，就叉上 [GitHub 代码](https://github.com/learning-paths-io/microcontext#js-flash-container)或者给我发一个 pull 请求。
# 浏览器中的本地延迟加载

> 原文：<https://betterprogramming.pub/native-lazy-loading-in-the-browser-85dabe6653ed>

## 我们现在和早期是如何做的

![](img/fb2329f6a3d7daa94009ef1d33be3fc2.png)

Kate Stone Matheson 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

2019 年初听说原生懒加载的时候，我就火上浇油了！Chrome 是第一个支持它的浏览器。

快进到 2020 年初，Edge、Firefox、Chrome、Opera、Android 和 Android Chrome 都支持它。由于目前这种支持已经好了很多，我想是时候写一写了。

在本文中，我想向您展示在浏览器中延迟加载图像和 iframes 是多么简单。

但是首先，我们将深入了解什么是延迟加载，为什么您想使用它，我们以前是如何做到这一点的，以及您想如何继续做下去。

# 1.什么是懒装？

当一个`img`或`iframe`标签在 HTML 中并被浏览器解析时，它将立即加载源代码。这会阻止整个页面完成加载。

这将导致加载页面的时间更长，因此用户与页面交互的时间也更长。我们希望用户尽可能快地与页面交互。

所以聪明人想出了懒装。这是一种显示图像并只在用户可见时加载 iframes 的技术。这将确保用户只下载可见的内容。

## 历史

过去，我们通过滚动事件监听器进行延迟加载。对于每个事件，我们都用图像和 iframes 检查数组，看它们是否可见。

如果它们是可见的，我们必须将属性`data-src=" source URL"`改为``src=“source URL”`。这会在那一刻加载一个图像或页面。

但是滚动事件是非常低效的，因为它在很短的时间内触发了无数的事件。

我们必须计算元素是否可以用`getBoundingClientRect()`看到。这样做的缺点是，每次调用，都会迫使浏览器重新布局整个页面。

当时来说，并不理想。但这比通过页面加载来加载所有图像要好，因为这样用户就必须等到页面完全加载完毕。

## 交叉观察者

2016 年初，Chrome 中提供了交叉点观察器。其他浏览器随后跟进。

Mozilla Web 文档以非常清晰的方式描述了交叉点观察器:

> “交集观察器 API 提供了一种异步观察目标元素与祖先元素或顶级文档的视口的交集的方式。”— [Mozilla 文档](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)

路口观察者观察你告诉它的东西。它不同于事件。事件不断被触发，即使你没有对它们做任何事情。

# 2.原生浏览器延迟加载

原生懒人加载超级简单！在你的`img`或`iframe`标签上添加一个`loading`属性。您可以选择值`lazy`、`eager`和`auto`。

*   `lazy`:使用延迟加载。
*   `eager`:直接加载。
*   `auto`:浏览器会选择是否懒加载。

这种惰性加载的方式非常简单，使用它是显而易见的。

# 3.它是如何工作的？

如果您想使用延迟加载功能，可以为您的图像或 iframes 添加一个值为`lazy`的`loading`属性:

```
<img src=“imageurl.jpg” loading=“lazy” alt=“...” />
```

使用这个属性，您将告诉浏览器只在图像对用户可见时才显示它。当它不可见时，它不会下载图像或加载 iframe 的页面。

我们需要跨多个浏览器的支持，但是并不是所有的浏览器都支持本地延迟加载，所以我们需要一个 polyfill。

# 4.使用交叉点观察器回退

因为 [Safari(在 macOS 和 iOs 上)](https://caniuse.com/#feat=intersectionobserver)还不支持原生延迟加载，但是支持交集观察器，所以我们可以使用基于交集观察器的 [polyfill 来实现原生延迟加载。](https://github.com/mfranzke/loading-attribute-polyfill)

## 支持

这种 polyfill 支持大多数现代浏览器。

*   苹果电脑:火狐浏览器，Safari 12，11
*   iOS: Safari 12
*   Windows: Chrome、Edge、Internet Explorer 11

## 插件

也有一些插件可供 PHP 和 WordPress 使用原生惰性加载。

*   [WordPress 插件](https://wordpress.org/plugins/native-lazyload-polyfill/)
*   [PHP 分支扩展](https://github.com/tim-thaler/twig-loading-lazy)
*   [工艺树枝装载插件](https://github.com/tim-thaler/craft-twig-loading-lazy)

## 更多支持

如果需要支持 IE 11，还需要为交集观察者加载一个 [polyfill。](https://github.com/w3c/IntersectionObserver/tree/master/polyfill)

我已经写了一篇关于[如何使用交叉点观察器来延迟加载你的图像](https://medium.com/better-programming/lazy-loading-images-with-the-intersection-observer-e8ad57a1682c)的深入文章。

# 结论

超级棒的是，我们终于在浏览器中加入了原生延迟加载功能！添加 polyfill 后，我们可以确保所有现代浏览器都可以使用它。

你已经在你的网站或网络应用上使用了原生的延迟加载功能了吗？还是有兴趣实施？请在评论中让我知道你的想法，如果你发现了一些秘密。

> 我在一个不和谐服务器上聚集了一些来自世界各地的[有抱负的开发者，如果你想加入，请随意。](https://mailchi.mp/fb82491d03f8/dev-by-rayray-discord-community)

# 阅读更多

[](https://medium.com/dev-together/5-steps-give-structure-to-your-development-projects-e1348eb9f17d) [## 5 个步骤为您的开发项目提供结构

### 你不能管理你的编程项目吗？试试这个！

medium.com](https://medium.com/dev-together/5-steps-give-structure-to-your-development-projects-e1348eb9f17d) [](https://levelup.gitconnected.com/how-to-build-a-dark-mode-switcher-with-css-variables-ccb13f7441a0) [## 如何用 CSS 变量构建一个黑暗模式切换器

### 用 CSS 变量、JavaScript 和 TypeScript 构建一个黑暗模式切换器

levelup.gitconnected.com](https://levelup.gitconnected.com/how-to-build-a-dark-mode-switcher-with-css-variables-ccb13f7441a0) [](https://medium.com/dev-together/javascript-concepts-you-need-before-starting-w-frameworks-libraries-25a325312b5c) [## 开始使用框架和库之前需要的 JavaScript 概念

### 在你熟悉它们之前不要开始

medium.com](https://medium.com/dev-together/javascript-concepts-you-need-before-starting-w-frameworks-libraries-25a325312b5c) [](https://medium.com/undefined-developer/5-tips-to-make-100daysofcode-effective-for-everyone-3695587aad64) [## 让 100DaysOfCode 对每个人都有效的 5 个技巧！

### 明智地花时间学习编码

medium.com](https://medium.com/undefined-developer/5-tips-to-make-100daysofcode-effective-for-everyone-3695587aad64)
# 在你的网站上使用 SVG 图标的最佳方式

> 原文：<https://betterprogramming.pub/the-best-way-to-use-svg-icons-in-your-website-ea8312b8f07a>

## 不断改进以提升您的应用

![](img/ed119473be3c57d7e78ff9e7212afa5c.png)

照片由[娜塔莉·邱晨](https://unsplash.com/@naaatsnaps?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果我们想在我们的网站上使用 SVG 图像，我们可以使用 image、object，甚至 iframe 标签、SVG 精灵等等。在本文中，我们将探讨三种最流行的选择——它们的优缺点。

# 第一个也是最简单的方法

我见过的将 SVG 嵌入站点的最常见方式是使用`img`标签，并将其插入 src 属性。这种方法的优点是浏览器在第一次下载后缓存这个图像。但是在第一次渲染时，图像会在收到 HTML 后被下载，这会导致图像闪烁。这种方法的主要缺点是我们不能对 SVG 内部进行样式化，只能以原始形式显示。

这是 HTML:

SVG 技术的最大优势之一是能够通过 CSS 操作图像样式。这对于那些你肯定想在悬停时高亮显示或动画显示的图标来说尤其重要，因为你不需要使用额外的图像。

为了能够通过 CSS 对 SVG 重新着色，您需要将 SVG 直接插入 HTML，即“内联”它们，或者使用一个单独的文件，一个字符精灵。sprite 也必须插入到 DOM 中。没有其他技术允许您从 CSS 访问 SVG。让我们仔细看看这些技术。

# 内嵌 SVG

这是 HTML:

就定制和图像处理而言，将 SVG 直接嵌入 HTML 是最简单也是最强大的技术。

1.  如果图像很大，整个页面的加载时间会更长。
2.  在 js 框架中使用这种技术会大大增加 js 包的大小。
3.  您失去了对加载 SVG 的控制，它们不再是外部图像。你不能分别存储和缓存它们，忽略在连接不良时加载它们，等等。

# SVG 精灵

HTML SVG 精灵代码:

使用 sprite 中的 SVG 图像:

通过 sprite 连接的图像可以通过 CSS 访问，尽管有一些限制。样式不能“突破”阴影边界，阴影 DOM 从这个边界开始，由标签描述。

好消息是级联继续工作。您可以更改填充属性，并在任何属性上使用`currentColor`。对于大多数使用图标的情况，这已经足够了:

这是 HTML:

这是 CSS:

这种方法的缺点:

*   在较老的浏览器中(IE9 之前)，不支持`use`标签。但是有一个插件，你可以在这个链接找到:[https://github.com/jonathantneal/svg4everybody](https://github.com/jonathantneal/svg4everybody)。
*   sprite 可能会变得非常大，但无论如何它比 inline 或`img`标签要好

为了提高性能，您可以在头部预加载 sprite，以便随时可以使用。

# 额外小费

要自动生成 SVG 精灵，使用 [svg-sprite-loader，](https://www.npmjs.com/package/svg-sprite-loader)帮助实现精灵。

[svg-sprite-loader](https://www.npmjs.com/package/svg-sprite-loader) 的好处:

*   最低初始配置。大多数选项都是自动配置的。
*   浏览器的运行时。精灵被自动渲染并注入页面，你只需通过`<svg><use xlink:href="#id"></use></svg>`来引用图像。
*   节点/浏览器的同构运行时。可以手动在服务器或浏览器中渲染精灵。
*   可定制。编写/扩展运行时模块以实现自定义精灵行为。编写/扩展运行时生成器以生成您自己的运行时，例如，用导入的符号配置的 React 组件。
*   为从 css/scss/sass/less/styl/html 导入的图像生成外部 sprite 文件( [SVG 堆叠技术](https://css-tricks.com/svg-fragment-identifiers-work/#article-header-id-4))。

当您需要一个图像时，加载器将其转换为 SVG `<symbol>`，将其添加到特殊的 sprite 存储中，并返回一个表示符号的类实例。它包含`id`、`viewBox`和`content`(提取模式下的`id`、`viewBox`和`url`)字段，以后可用于引用精灵图像。例如:

当浏览器事件`DOMContentLoaded`被触发时，精灵会自动渲染并注入`document.body`中。如果需要自定义行为(例如，不同的安装目标)，可通过`spriteModule`选项覆盖默认子画面模块。

# 结论

谢谢你把文章看完。你可以在 [GitHub](https://github.com/evgeniykravtsov/svg-sprite-example) 上找到 React 的完整代码。总有一些方法可以让好的工具变得更好。小的改进会对你和你的项目有很大的帮助。
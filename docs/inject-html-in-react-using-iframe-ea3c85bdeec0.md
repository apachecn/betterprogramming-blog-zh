# 如何使用 IFrame 将 HTML 注入 React

> 原文：<https://betterprogramming.pub/inject-html-in-react-using-iframe-ea3c85bdeec0>

## 因为有时在新的 React 代码中需要 iframe

![](img/737414bc76582bed1302af236c944dda.png)

在本文中，我将解释如何在不使用`dangerouslySetInnerHTML` 的情况下在 React 组件中注入 HTML，因为使用起来不安全。它会导致跨站点脚本(XSS)攻击。

React 文档称:

> `dangerouslySetInnerHTML`是 React 对浏览器 DOM 中使用`innerHTML`的替代。一般来说，从代码中设置 HTML 是有风险的，因为很容易无意中让您的用户受到跨站点脚本(XSS)攻击。

使用`*dangerouslySetInnerHTML*`非常简单:

```
<div dangerouslySetInnerHTML={{ __html: "<p>Hello world!</p>"}} />
```

这样做的问题是，您注入的 HTML 可能是畸形的，它可能有破坏您的应用程序的 CSS/JavaScript，或者更糟的是，从您的应用程序中窃取数据的 JavaScript。

# 还有什么？

一种选择是使用`srcdoc`属性。它允许您指定 iframe 的内容，因此使用它非常简单:

```
<iframe srcdoc="<p>Hello world!</p>" />
```

但问题是`srcdoc`对浏览器的支持非常有限。IE 和 Edge 不支持。有一个[多孔填充物](https://github.com/jugglinmike/srcdoc-polyfill)可用，但在这种情况下没有用，因为它需要`allow-scripts`，这是不安全的。

# 为什么不用 iframe . document . open()/write()/close()？

让我们编写自己的自定义 Iframe 吧！

这并不像使用`srcdoc`那么简单，但它仍然可以在任何浏览器中安全使用和工作:

# 使用

```
import Iframe from './Iframe';<Iframe content={<p>Hello world!</p>} />
```

简单不是吗？

你今天学到新东西了吗？评论和反馈总是让作者开心！
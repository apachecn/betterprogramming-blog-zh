# DOM 操作和“innerHTML”的危险

> 原文：<https://betterprogramming.pub/dom-manipulation-the-dangers-of-innerhtml-602f4119d905>

## 使用 JavaScript 更改 DOM 时避免使用`'innerHTML’`的 5 个原因

![](img/e3249d0431102dc2def1d916654fecd5.png)

照片由[格雷格·拉科齐](https://unsplash.com/@grakozy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 介绍

当我编写一些基本的 JS 来进行 DOM 操作和事件监听时，我遇到了一个让我困惑的 bug。我花时间研究堆栈溢出，寻找解决方案，但无济于事。为了复制这个 bug，我将展示我的代码的一个简化版本。

首先，我有一个主体中只有一个`div`的基本 HTML 文件:

带有单个 div 的基本 HTML

在 JavaScript 文件中，我向 DOM 添加了一些节点，并在一个按钮上创建了一个事件监听器:

使用“innerHTML”进行 DOM 操作

在浏览器中，我的页面如期出现。有一个图像，后面是一个按钮和一些我添加到 DOM 中的附加文本。

![](img/68f3064da6546f06197e9554e155aa64.png)

web 浏览器中的内容

快速浏览一下代码，这个按钮有一个明确定义的用途。当它被点击时，字符串`hello`应该被输出到控制台。但是，单击该按钮时，不会打印任何内容。为什么？

首先，让我们试着找出 bug 是从哪里被引入到代码中的。首先，我将注释掉`div.innerHTML +=`行。

现在，当我点击按钮时，一切都按预期运行。点击按钮后，`hello`将被登录到控制台。

![](img/4f001a48e32f5f7ba82009eb897a0bc4.png)

按钮按预期工作

显然，带有`innerHTML +=`的那一行引起了重大问题。作为一名 JS 程序员，使用诸如`++`和`+=`这样的操作符似乎很自然。然而，将`+=`操作符与`innerHTML`一起使用将会导致重大问题。下面我们来讨论一下原因。

# 追加到`'innerHTML'`重新解析整个标签

当您将 append 操作符与`innerHTML`一起使用时，Javascript 将重新解析整个标签的内容，并重新创建所有的 HTML 元素。

# `'innerHTML'`非常慢

作为前一个原因的自然副作用，重新解析使得`innerHTML`非常慢。对于包含静态文本的小型 HTML 文档，这可能不是问题。但是，对于较大的文档，重新解析过程非常耗时，并且可能会产生意想不到的副作用。

# `'innerHTML’`删除事件监听器

从我们简短的实验中得出的最明显的结论是`innerHTML`删除了之前已经添加到 DOM 中的事件监听器。由于整个标签(我们代码中的`div`)正在被重新解析，事件监听器在这个过程中丢失了。不要使用`+=`操作符，而是使用 DOM 函数，比如`append()`。

# `'innerHTML’`存在安全风险

使用`innerHTML`会给你的网站带来潜在的安全风险。恶意用户可以使用跨站点脚本(XSS)来添加恶意客户端脚本，从而窃取存储在会话 cookies 中的私人用户信息。

你可以在`innerHTML`上阅读 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML#:~:text=Security%20considerations,creating%20a%20potential%20security%20risk.&text=Although%20this%20may%20look%20like,attack,%20the%20result%20is%20harmless.)。

# 符合 W3C 的方法可以很容易地添加新的 DOM 元素

诸如`.createElement()`、`appendChild()`、`append()`和`removeChild()`之类的方法允许安全的 DOM 操作，而没有`innerHTML`(更具体地说是`innerHTML+=`)的意外后果。除非你只是在一个 HTML 标签中设置文本，比如一个`<p>`或者`<div>`，否则请远离`innerHTML`。

对于初学者来说，`innerHTML`似乎是操作 DOM 上基本元素的最简单、最优雅的方式。然而，随着您的 JS 变得越来越复杂，您的 DOM 变得越来越大，`innerHTML`将损害性能并引入安全风险。

# 参考资料和补充阅读

*   [“在 JavaScript 中使用 innerHTML 的缺点”](https://www.tutorialspoint.com/disadvantages-of-using-innerhtml-in-javascript#:~:text=There%20is%20no%20append%20support,changing%20innerHTML%20directly%20very%20slow.&text=innerHTML%20does%20not%20provide%20validation,the%20document%20and%20break%20it.)
# 微前端组合模式

> 原文：<https://betterprogramming.pub/micro-frontend-composition-patterns-9b789c0dad81>

## 设计模式来帮助您有效地构建微前端

![](img/39974d6fb574413bba465ad516ae72ed.png)

图为[安德鲁·布坎南](https://unsplash.com/@photoart2018?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/lego-wall?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

当构建微前端(mfe)时，有许多不同的技术/模式可以使用。以下是它们的列表，包括使用每种方法的优缺点。

MFEs 把一个网站分割成小的独立的页面和组件服务。在[micro-frontends.org](https://micro-frontends.org/)可以找到 MFEs 的概述。

# 把...嵌入

如果一个组件变化很大，并且被多个页面使用，您可能希望使用这种技术。页面服务向组件服务发出 HTTP 请求，组件服务返回 HTML。然后，组件 HTML 响应可以连接到页面的其余部分。这使得组件团队和页面团队能够以他们自己的速度部署变更。

由于组件请求可能会失败或变慢，因此需要考虑该体系结构的弹性。组件是强制性的吗？如果组件失败，页面可以显示占位符吗？想要拥有服务弹性的组件团队可以提供一个库，在失败场景中返回回退。

具有多个独立组件的页面能够并行发出组件请求。组件的响应时间仍然很重要，因为页面的速度与最慢的响应速度一样快。

高负载的页面可能需要考虑缓存组件，尤其是当组件的输入基数较低时。为了允许组件团队控制缓存，TTLs 页面可以使用`Cache-Control`头来知道响应可以缓存多长时间。

已经被分解成自己的服务的组件现在可以选择被推迟到浏览器。这对于加载缓慢的组件特别有用，因为当浏览器在后台加载组件时，客户可以开始浏览您的页面。这确实依赖于启用 JavaScript 和现代浏览器，但我认为我们可以假设这一点。

# 模板

页面和组件不需要返回完整的响应，它们可以返回包含类似于模板的占位符的响应。占位符可以用于需要本地化的内容或需要嵌入的其他组件。这使得 mfe 变得简单，可能只是在路由服务中建立的静态响应。这允许跨领域的关注集中在一个地方。

这种架构的一个缺点是，它使得测试单个应用程序变得更具挑战性(因为您需要通过填写模板响应的服务进行测试)。解析模板的计算量也可能很大，尤其是在处理大量响应时。让 mfe 处理本地化和组合，保持任何代理/路由服务简单，这可能更具可伸缩性，也更容易测试。

# 图书馆

使用库来分发您的组件可能是最明显和最简单的组合架构，但它值得一提。

与任何软件一样，如果一个模块被许多应用程序使用，它会被提取到一个单独的库中。前端元件也可以做到这一点。React 和其他前端框架是将通用组件提取到库中的绝佳工具。如果你没有使用支持这个的框架或者你想成为框架不可知者，你的组件库可以返回一个包含 HTML 的字符串。

使用库时，保持应用程序在最新版本上运行可能会成为一个挑战——因为这取决于应用程序所有者是否将其作为依赖项进行更新。可能会出现这样的情况，不同的应用程序/页面使用不同版本的组件，给客户带来混乱的体验。因此，当您知道组件不会经常改变或者您可以轻松地更新消费者时，最好使用库。

# 头球

标题最适合用于数据，而不是 UI 组件。如果所有前端都希望访问一组公共数据，那么头文件是传播它的一个很好的方式。通常，这些头由网关设置，但是它们可以由所有 mfe 中的公共库设置。

保持通用的命名约定(例如，公司或应用程序前缀)允许应用程序自动将这些头代理给传递通用数据的下游服务。一个常见的例子是整个应用程序堆栈都需要的客户跟踪数据。

头中的数据最好存储在 URL 编码的 JSON 中。这允许数据被安全地发送，但也允许人们不经解码就能阅读它。使用 JSON 允许消费者解析数据，也允许模式改变，同时保持向后兼容性。

如果所需的数据来自一个复杂的源，那么建议将它从网关中提取出来，放到一个单独的服务中。这增加了额外的复杂性，尤其是在弹性方面。当服务停止时会发生什么？某些数据/报头可能被认为是强制性的，在这种情况下，您可能希望使用非 2xx 状态代码终止请求。其他可能是可选的；但是，仍然建议使用默认值来设置头，以保护应用程序免受空错误的影响。

# 结论

现在，您应该已经了解了构建 mfe 的一些不同方法，以及什么时候最好使用它们。可以在[这里](https://medium.com/better-programming/example-micro-front-end-with-kong-ed001d601c0a)找到一个演示这些模式的示例应用。
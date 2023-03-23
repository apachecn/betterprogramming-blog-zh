# Angular 的 Next.js 等价于什么？

> 原文：<https://betterprogramming.pub/what-is-the-next-js-equivalent-for-angular-2ed7efe902d6>

## 关于 Angular Universal 你需要知道的一切

![](img/328b36a94d0de06163744d9b2c680795.png)

[菲利·桑蒂兰](https://unsplash.com/@filisantillan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

[Next.js](https://nextjs.org/) 是目前最流行的全栈框架之一，它已经迅速成为 web 开发中采用最多的技术之一。

除了添加 SSR(服务器端渲染)功能来进行反应，Next.js 还提供了一个完整的工具集，用于以低加载时间构建快速、高效、高性能的网站。

在本文中，我们将提供 Angular Universal 的概述，它是 Angular 生态系统中与 Next.js 最接近的等价物。

# Next.js 是什么？

正如官方文档中的[所述，Next.js 是一个基于](https://nextjs.org/learn/foundations/about-nextjs/what-is-nextjs) [React](https://reactjs.org/) 的灵活 JavaScript 框架，它提供了创建快速 web 应用程序所需的一切。使用 Next.js，呈现应用程序的大部分逻辑从浏览器转移到了服务器。与 React 相比，这大大减少了客户端渲染的数量，这使得 Next.js 应用程序对 SEO 友好。

Next.js 应用程序是完整的 web 应用程序，这意味着您可以在任何支持 Node.js 运行时的地方部署它们。这对于那些在客户端和服务器端开发中寻找一致的工具集的 JavaScript 开发人员来说非常有吸引力。此外，Next.js 可以利用成千上万个可用的 [npm](https://www.npmjs.com/) 库，这使得它成为一个节省成本和时间的解决方案。

# 什么是有角？

[Angular](https://angular.io/guide/what-is-angular) 是一个基于组件的框架，用于构建可伸缩的 web 应用程序。具体来说，Angular 是一个集成良好的库集合，为您提供了几个特性，包括呈现、表单管理、路由、数据绑定、依赖注入、测试/模拟功能和事件系统。因此，可以把 Angular 想象成一个“包含电池”的框架，带有一套用于开发、更新、构建和测试代码的工具。

Angular 是建立在 [TypeScript](https://www.typescriptlang.org/) 之上的，和 React 一样，它被用来构建呈现客户端的 spa([单页面应用](https://en.wikipedia.org/wiki/Single-page_application))。React 和 Angular 的主要区别在于 React 是一个库，而 Angular 是一个全功能的框架。换句话说，Angular 是一个 MVC 框架，不需要任何额外的库来完成。另一方面，React 只是一个库，需要其他库才能变得完整。所以，你需要外部依赖来轻松实现你的目标。

# Next.js 和 Angular 有什么共同点？

Next.js 和 Angular 是目标不同的技术。然而，这两个框架之间有一些共同的元素。现在让我们来看看三个最重要的。

*   它们都是基于 JavaScript 的:Next.js 已经用 JavaScript 开发了，Angular 用 TypeScript 开发了。TypeScript 是一种构建在 JavaScript 之上的编程语言，这两种语言完全可以互操作。因此，它们都是基于 JavaScript 的，并支持 JavaScript 和 TypeScript 开发，以及用这两种语言之一构建的所有 npm 库。
*   它们都支持客户端呈现:尽管 Next.js 在开发时考虑了服务器端呈现，但它也支持客户端呈现。这意味着您可以避免预先呈现页面的某些部分，然后使用 JavaScript 在浏览器中填充它们。这也意味着您可以在技术上使用 Next.js 来构建 SPA，尽管这不是框架背后的目标，并且您最终将只使用 React 而不使用 Next.js 特性。尽管如此，Angular 和 Next.js 都可以用作客户端渲染技术。
*   它们都是开源解决方案:Next.js 和 Angular 都是免费的开源框架。这意味着他们的代码是由社区支持的，每个人都可以分叉它或简单地看看他们的 repos。两者都托管在 GitHub 上，拥有超过 80k 颗星。这里可以找到 GitHub [上的 Angular repo，这里](https://github.com/angular/angular)可以找到 GitHub [上的 Next.js repo。](https://github.com/vercel/next.js/)

# Next.js 与 Angular 有何不同

尽管 Angular 和 Next.js 有一些共同点，但它们的开发有两个不同的目标，而且它们本质上是不同的。这两个框架之间有几个不同之处，但是让我们把重点放在最相关的三个方面:

*   Next.js 是全栈，Angular 只是前端:Next.js 和 Angular 是两个不同的层次。Next.js 是一个建立在 React 之上的框架，它使用 React 作为前端技术，同时为开发人员提供后端功能。换句话说，Next.js 是一个全栈框架。或者，Angular 是 React 的等价物，这两种基于 JavaScript 的技术处于同一水平。简单来说:Angular 和 React 只是给开发者配备了客户端的功能，而且都是只有前端的技术。
*   与 Angular 不同，Next.js 可用于 API 开发:由于 Next.js 基于 Node.js，所以也支持 [API 开发](https://nextjs.org/docs/api-routes/introduction)。这意味着您可以使用 Next.js 创建一个连接到数据库的后端应用程序，通过 API 公开数据。这在 Angular 中是不可能的，它只能用于调用 API 和使用检索到的数据。
*   Next.js 用于静态生成服务器端渲染，Angular 用于客户端渲染:虽然技术上可以使用 Next.js 进行客户端渲染，但这不是它的主要用途。Next.js 是为多页面应用程序和网站开发而设计的，它应该用于静态生成和服务器端呈现功能。恰恰相反，Angular 是为构建 SPA 而设计的，它为开发人员提供了处理客户端渲染和轻松构建 SPA 所需的东西。

这里需要理解的重要一点是，Angular 不能用于服务器端渲染。这就提出了一个问题:与 Next.js 基于 React 并允许服务器端渲染的方式一样，基于 Angular 也有类似的东西吗？答案是角万能！

# Angular Universal:基于 Angular 的 Next.js 的替代方案

[Angular Universal](https://angular.io/guide/universal) 项目是对由 [Patrick Stapleton](https://twitter.com/patrickjs__) 和 [Jeff Whelpley](https://twitter.com/jeffwhelpley) 创建的 Angular 核心 API 的扩展项目。与 Angular 本身不同，Angular Universal 是一个社区驱动的项目，不是由 Google 开发和维护的。这并不意味着它不是一种可靠的技术。事实上，Angular Universal 已经变得如此流行，以至于它现在是官方支持的在服务器上呈现 Angular 应用程序的方式。

换句话说，Angular Universal 在使用 Angular 时支持服务器端渲染。具体来说，Angular Universal 在服务器上执行，负责生成静态 Angular 应用程序页面，这些页面稍后将由客户端发送和运行。

Angular Universal 的主要特点是:

*   它支持服务器端渲染
*   它允许您预先呈现静态页面
*   它为你提供一切你需要优化搜索引擎优化
*   它支持 API 开发
*   它允许您安全地提供静态文件

现在，让我们学习如何将现有的 Angular 项目转换为 Angular Universal app。

# **从有角到有角万能**

如果您已经有一个角度> = 7 的应用程序，请输入应用程序目录，并启动以下命令:

```
ng add @nguniversal/express-engine
```

这将对您的应用程序进行一些更改，添加和更新一些文件，以使您的应用程序服务器就绪。你的 Angular app 现在是 Angular Universal app。

然后，您可以使用以下命令开始在本地渲染 Angular Universal 应用程序:

现在，在您的浏览器中访问`https://localhost:4200`，您应该能够看到您的 Angular Universal 应用程序在运行。

如你所见，从 Angular 转换到 Angular Universal 只需要一个命令和几分钟。

# Angular Universal 与 Next.js:主要区别

让我们更深入地研究 Angular Universal 和 Next.js 之间的主要区别:

## 它们以不同的方式支持增量静态再生

增量静态重新生成(ISR)允许您仅在需要时静态重新生成特定页面。这可以防止您在每次页面更改时重新构建整个网站。

Next.js 通过 [getStaticProps()](https://nextjs.org/docs/basic-features/data-fetching/incremental-static-regeneration) 和它的[重新验证选项](https://nextjs.org/docs/basic-features/data-fetching/incremental-static-regeneration)原生支持 ISR。相反，在撰写本文时，Angular Universal 并不原生支持 ISR。你可以用 Angular Universal 来实现，正如这里的[所解释的](https://itnext.io/incremental-static-regeneration-for-angular-42b0a8440e53)，但是这涉及到自定义逻辑。

## Next.js 提供了一些优化

Next.js 如此受欢迎的一个主要原因是，它自带了一些特性，如[图像优化](https://nextjs.org/docs/basic-features/image-optimization)、[脚本处理](https://nextjs.org/docs/basic-features/script)、[字体优化](https://nextjs.org/docs/basic-features/font-optimization)等等。这些使你能够毫不费力地建立优化的网站，并帮助你获得更好的搜索引擎优化分数。另一方面，并不是所有的主机提供商都支持这些功能，从一个提供商切换到另一个提供商可能会有问题。

相反，Angular Universal 遵循一种不同的方法，不提供任何内置优化。这种简化的方法考虑到了较少的障碍，但是对于习惯于依赖专门的本地特性的人来说，这也是不方便的。

## 它们以两种不同的速度进化

Angular Universal 是 Angular 的一部分，这意味着它遵循 Angular 的发展路线图。与 Next.js 相比，Angular 有一个缓慢的开发时间表，它允许开发人员总是处理可靠和稳定的框架。这也意味着社区不必不断追赶。

相比之下，Next.js 走的是相反的路，快速开发，频繁发布。因此，Next.js 文档可能不总是最新的。

## 它们处理静态文件缓存的方式不同

Next.js 和 Angular Universal 都允许您安全轻松地提供静态文件。在 Next.js 中，你必须将它们放在`/public`文件夹中，而在 Angular Universal 中则放在`/dist`文件夹中。因为这些文件不会改变，所以应该为它们提供缓存策略。

Next.js 允许您为所有静态文件处理一个[单一缓存策略，除非您为每个文件定义了一个定制的头。相反，](https://nextjs.org/docs/going-to-production#caching) [Angular Universal 允许你毫不费力地为每个文件](https://angular.io/guide/universal#serving-static-files-safely)定义一个定制的缓存策略，这要归功于`serve.use()`功能。

# 结论

在本文中，您了解了什么是 Angular Universal，为什么您需要它来进行服务器端呈现或静态生成 Angular 应用程序，以及它与 Next.js 相比有什么不同。Angular Universal 是一个强大的工具，具有服务器端呈现的所有好处，例如在 SEO 方面使 web 爬虫的工作更容易，提高移动设备的性能，以及更快地显示第一页。

将现有的 Angular 应用程序转换为 Angular Universal 项目只需要一个命令，使 Angular Universal 成为 Angular 的 Next.js 的完美替代品。

感谢阅读！我希望这篇文章对你有所帮助。如有任何问题、意见或建议，请随时联系我。

*原发布于*[*https://reflect . run*](https://reflect.run)*。*
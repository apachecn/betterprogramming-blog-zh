# 介绍现代网络

> 原文：<https://betterprogramming.pub/introducing-modern-web-dfafe83beeeb>

## 开发人员构建现代网站所需的指南和工具

![](img/054c6fa9aee094ff8f467f54b1b55424.png)

你好，球形现代网络标志。弗拉季斯拉夫·克拉平在 [Unsplash](https://unsplash.com/s/photos/hello) 上的照片。

# 什么是现代网络？

我们很高兴终于推出我们全新的项目:[现代网络](https://modern-web.dev/blog/introducing-modern-web/)。

几年前，我们启动了[开放 Web 组件](https://open-wc.org/)项目。我们的目标是帮助人们开发 web 组件，我们创建了指南和工具来帮助人们完成这项工作。在做这个项目的时候，我们意识到我们做的很多东西不一定是特定于 web 组件的。

为了保持对开放 Web 组件项目的关注，并与更大的开发人员社区分享我们的工作，我们决定拆分项目并创建 Modern Web。别担心，开放 Web 组件不会消失！它将重新关注特定于 web 组件的主题，而在现代 web 中，我们将致力于 Web 开发的通用工具和指南。

# 现代网络的目标

> “我们的目标是为开发者提供构建现代网络所需的指南和工具。我们的目标是与浏览器紧密合作，避免复杂的抽象。”

现代浏览器是构建网站和应用程序的强大平台。在寻求定制解决方案之前，我们首先尝试使用浏览器中的可用内容。

当你与浏览器一起工作而不是与之对抗时，代码、技能和知识会在更长时间内保持相关性。开发变得更快，调试也更容易，因为涉及的抽象层更少了。

与此同时，我们意识到这样一个事实，即不是所有的问题都可以由今天的浏览器很好地解决。我们支持开发人员做出明智的决定，将工具和定制引入到他们的项目中，以便开发人员可以在以后随着浏览器支持的改进而升级。

# 我们未来的计划

这一声明标志着现代网络的正式发布。我们的网站位于 [modern-web.dev](https://modern-web.dev) ，我们的包可以在 npm 上的 [@web](https://www.npmjs.com/org/web) 名称空间下获得。我们的代码是开源的，可以在 [GitHub](http://github.com/modernweb-dev/web) 上公开获得。

关于更新，你可以在 [Twitter](https://twitter.com/modern_web_dev) 上关注我们，如果你喜欢你所看到的，请考虑在[开放集体](https://opencollective.com/modern-web)上赞助这个项目。

在过去的几年里，我们一直在从事许多不同的项目。在本文中，我们将带您浏览我们的一些项目，以及我们计划如何将它们融入现代 Web 项目。

# 指导

在我们全新的[网站](https://modern-web.dev)上，我们有一个专门的[指南](https://gist.github.com/../guides/index.md)部分。它旨在帮助你在构建现代网络时变得自信。它提供了使用我们的工具的逐步指南，并且我们记录了开发人员在进行无构建开发时遇到的常见问题。

这一部分正在进行中。随着时间的推移，我们希望添加更多内容，并欢迎您的反馈和改进。随意点击“在 GitHub 上编辑这个页面！”、[未决问题](https://github.com/modernweb-dev/web/issues/new)或[加入讨论](https://github.com/modernweb-dev/web/discussions)。

# Web 测试运行程序

我们非常兴奋地宣布 [Web Test Runner](https://gist.github.com/../docs/test-runner/overview.md) ，这是我们过去几个月一直在做的主要项目之一。

现在已经有很多测试解决方案了。不幸的是，它们要么在 Node.js 中运行测试，使用 Jsdom 之类的东西模拟浏览器 API，要么不支持开箱即用的本地 ES 模块。

我们认为让浏览器代码兼容 Node 中的测试是不必要的复杂。在真正的浏览器中运行测试对(跨浏览器)兼容性有更大的信心，并使编写和调试测试更加容易。

这就是我们创建 Web Test Runner 的原因。

## 突出

*   使用[木偶师](https://gist.github.com/../docs/test-runner/browsers/puppeteer.md)、[剧作家](https://gist.github.com/../docs/test-runner/browsers/playwright.md)或[硒](https://gist.github.com/../docs/test-runner/browsers/selenium.md)的无头测试
*   从浏览器报告日志、404 和错误
*   调试用开发工具打开一个真正的浏览器窗口
*   通过[导入地图](https://gist.github.com/../docs/test-runner/writing-tests/mocking.md)模拟 ES 模块
*   显示浏览器属性，如视口大小和黑暗模式
*   并行和隔离运行测试
*   互动观看模式
*   通过只重新运行改变的测试来快速开发
*   由 [esbuild](https://gist.github.com/../docs/dev-server/plugins/esbuild.md) 和[汇总插件](https://gist.github.com/../docs/dev-server/plugins/rollup.md)提供支持

## 入门指南

今天在 npm 上有一个名为`@web/test-runner`的测试版。功能差不多全了。我们将很快推出 v1 版本！

如果你想现在就开始，看看我们的 Web 测试运行器[入门指南](https://gist.github.com/../guides/test-runner/getting-started.md)。

# Web 开发服务器

`es-dev-server`是 Open Web Components 中最受欢迎的包，但它并不仅仅针对 Web 组件。这就是为什么我们在现代网络项目中致力于它的精神继承者。我们称它为 Web Dev Server，它将被命名为`@web/dev-server`。

如果您正在进行无构建开发，您可以使用任何 web 服务器进行开发。我们的开发服务器通过提供开发人员生产力特性和使您的代码与旧浏览器兼容来提供帮助。

## **亮点**

*   就像一个真正的网络服务器。没有任何标志，它提供未转换到浏览器的代码。
*   在重新加载之间有效缓存未更改的文件
*   使用`--node-resolve`解决裸机模块导入
*   使用`--watch`自动加载文件更改
*   使用`--esbuild-target`与旧浏览器兼容
*   广泛的[插件系统](https://gist.github.com/../docs/dev-server/plugins/overview.md)
*   与 [esbuild](https://gist.github.com/../docs/dev-server/plugins/esbuild.md) 集成，实现 JS、TS 和 JSX 的快速转换
*   重用开发服务器中的大多数[汇总插件](https://gist.github.com/../docs/dev-server/plugins/rollup.md)
*   开发过程中多填充[导入地图](https://gist.github.com/../docs/dev-server/plugins/import-maps.md)的插件

我们的 Web 开发服务器还没有完全完成*和*，但是我们已经为我们的 Web 测试运行器构建了基础部分。这意味着许多列出的特性和插件也适用于我们的测试运行程序。

我们正在努力完成 Web Dev 服务器上的开放任务，请继续关注进一步的更新。

# 生产建筑

虽然我们试图在开发过程中避免复杂的构建，但它们仍然是生产优化的需求。在这里，事情也会变得相当复杂。通过插件和指南，我们将使生产构建与无构建开发工作流的集成变得更加容易。

这里一个很好的例子是[@ open-WC/roll up-plugin-html](https://www.npmjs.com/package/@open-wc/rollup-plugin-html)，我们将把它移到`@web`名称空间中。这个插件可以在现有的 HTML 页面上运行 Rollup。Rollup 将捆绑和优化在 HTML 中找到的任何模块脚本。它可以处理单个页面，也可以处理多个页面，代码分割，以及在页面之间共享公共代码。

我们计划进一步扩展这个插件，增加对优化图片和 CSS 等资源的支持。

# 渐进式网络应用

当我们谈论现代网络应用时，我们也谈论*进步网络应用* (PWAs)。PWAs 是一种很好的方式，通过允许您的应用程序离线工作，并能够在用户的主屏幕上安装您的 web 应用程序，以及许多其他好处，为您的用户提供迷人和用户友好的体验。

不幸的是，服务人员接近火箭科学，并且实现 PWA 特性并不总是像它应该的那样简单。这就是为什么我们将提供技术指南和工具，使您作为开发人员的生活更加轻松。

我们不仅发布了 [rollup-plugin-workbox](https://www.npmjs.com/search?q=rollup-plugin-workbox) 来帮助您在构建时生成服务工人，而且在未来，我们还将有一组零依赖性 PWA 助手作为 web 组件和普通 JavaScript 函数，以及 Codelabs 来帮助您开始构建现代渐进式 web 应用程序。

# **文档(火箭)**

在过去的几年里，我们使用了不同的框架和工具来构建我们的网站。最近我们成了 11 世纪的超级粉丝。它使用简单，与 markdown 一起工作，并且只生成*普通的 HTML。显示页面内容不需要运行时 JavaScript，速度非常快。*

为了给我们的页面添加交互性，我们开始使用 web 组件，并为 [HTML](https://www.npmjs.com/package/@open-wc/rollup-plugin-html) 和[工具箱](https://www.npmjs.com/search?q=rollup-plugin-workbox)应用了 Rollup 插件。

通过这种方式，我们的 JavaScript 得到了优化，页面之间共享了公共代码。workbox 增加了一个服务人员，使我们的网站可以离线使用，并预先缓存页面，从而实现超快速导航。

为了获得出色的内容创作体验，我们将 11ty 与我们的开发服务器进行了集成。这增加了一些功能，如解决裸导入和当文件改变时重新加载浏览器。

通过以这种方式结合现有的工具，我们认为我们已经做出了对其他人也有用的非常强大的东西。这就是为什么我们在现代网络家族中启动了一个子项目，我们将其代号为 Rocket。

它仍处于早期阶段，但我们已经在为自己的[网站](https://modern-web.dev)使用一个原型。我们仍然缺少一些功能，但我们将继续改进它，一完成就会发布公告。所以请留意它！

# **现代网络家族**

Welp，我们意识到，对于第一个公告帖子来说，这是很多信息。但这些项目中有许多已经酝酿了多年，最终找到了合适的归宿。正如我们之前提到的，现代 Web 的目的就是让开发人员的生活变得更容易，降低工具的复杂性，并与浏览器保持紧密联系。

虽然我们现在已经分布在多个存储库，如 Open Web Components、Modern Web 和 Rocket，但我们想向您保证，所有这些项目都属于同一个 Modern Web 家族，我们的目标是帮助您的开发人员生活更加轻松。

# **感谢阅读**

我们为迄今为止取得的成就和前进的方向感到无比自豪，我们邀请您加入我们的前进之路。

还有更多的，所以在 [Twitter](https://twitter.com/modern_web_dev) 上关注我们，如果你喜欢你所看到的，请考虑在[开放集体](https://opencollective.com/modern-web)上赞助这个项目。

由现代网络核心团队与♥️共同撰写。
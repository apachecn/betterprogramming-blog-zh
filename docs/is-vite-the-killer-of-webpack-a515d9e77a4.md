# Vite 是 Webpack 的黑仔吗？

> 原文：<https://betterprogramming.pub/is-vite-the-killer-of-webpack-a515d9e77a4>

## 或者为什么应该为 React/Angular/Vue 使用模块捆绑器

![](img/777e955427f53d315b242586042316e6.png)

照片由[劳塔罗·安德烈亚尼](https://unsplash.com/@lautaroandreani?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

要建立一个网站，你需要 HTML、CSS 和 JavaScript。这似乎很简单，但没有人只是坚持这三个。您可能希望将 JS 转换为 TypeScript，为 UI 添加 React 之类的前端库，使用 SASS 之类的 CSS 处理器和一系列其他模块，这样您就不必从头开始了。

这才是模块捆扎机真正闪光的地方。Vite 是一个新的工具，支持主要的框架和高速可能是前端开发人员现在最好的工具之一。Vite 有时被认为是 Webpack 的 T4 杀手。在这个快速阅读中，我们将了解你是应该开始使用 Vite 还是坚持使用 Webpack 来完成你的所有项目。

# 什么是模块捆绑器？

![](img/7927a33a655e1c50e4712aec89427186.png)

沃洛季米尔·赫里先科在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在我们讨论 Vite 之前，我们首先应该理解什么是模块包。

> 捆绑器将多个 JavaScript 文件合并成一个可以在浏览器中运行的文件。

这个 JS 包包含所有源代码以及所有导入的依赖项。

如果我们试图在我们的网站上添加一个模块而不使用这样的捆绑器，我们可能会得到一个错误，因为浏览器不知道在哪里可以找到这个模块。这就是像 Webpack 这样的东西可以帮助我们的地方。

好的，我们可以使用 Webpack 来处理 JS 模块，但是一些网站样式怎么样呢？比如你想给这个项目添加一些 SASS。它不仅不是 JavaScript，还需要由 SASS 编译器编译。如果您尝试使用它，您会得到一个错误，说没有配置加载程序。

**那么装载机到底是什么？**基本上是对非 JS 的文件进行预处理的一种方式。对于我们的 CSS (SASS)用例，它将把代码注入到文档对象模型(也称为 HTML DOM)中，这将满足我们的样式需求。

更重要的是，我们可以使用插件系统直接进入 bundler 的生命周期。一个非常有用的插件是 bundle analyzer，它可以让我们看到哪些包花费了最多的时间来加载。

如果你曾经开发过一个没有模块捆绑器的网站，你会知道要看到变化，你必须在终端重新运行命令。像 Webpack 这样的工具可以监视变化并更新网站，而开发人员无需运行任何命令。

# 为什么要 Vite？

![](img/c2c822a2182800c815dd15dfa12f99ba.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

> **Vite** 是下一代前端工具。它声称拥有即时服务器启动、快速模块替换、优化构建、通用插件等等。它是由 Vue 的创建者开发的，支持所有主要的框架。

**现在，这些事情到底意味着什么？作为开发人员，我们为什么要关心这些事情？对于大多数打包程序，你必须在每次保存时构建整个应用程序。如果你有一个大的应用程序，你可能需要**几分钟**来反映任何基本的变化。**

让我们来看看捆绑的典型过程，这是 Webpack 和其他流行工具所使用的。从应用入口点**开始，所有的**路径和模块被捆绑在一起，之后服务器就准备好了。

Vite 采取了不同的方法。它会立即启动服务器，然后获取不经常更改的依赖项，并使用 esbuild 预捆绑它们。Esbuild 是用 Golang 编写的，这使得它比基于 JavaScript 的捆绑器快了 100 倍。

Vite 使用基于路径的代码分割来理解代码的哪些部分实际上需要被加载。因此，它不必重新捆绑所有东西。

Vite 还使用现代浏览器中的本地 ES 模块支持来交付代码，这让浏览器承担开发中的捆绑工作。至于生产，Vite 使用 Rollup 来捆绑和实现各种性能优化。

## Snowpack 怎么样？

![](img/8fad0d87650b9bb071699a6152f8c554.png)

由 [Alberto Restifo](https://unsplash.com/@albertorestifo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Snowpack 也是一个无捆绑的原生 ESM 服务器，和 Vite 很像。事实上，Vite 的灵感来自 Snowpack v1。

Snowpack 也是一个很棒的工具，它在开发者社区获得了喜爱。虽然有一些关键的不同，这使我与 Vite 的经历更加愉快。

Vite 的一个特性是自动 CSS 代码分割，它将只加载特定页面的相关 CSS。这甚至可以进一步提高捆扎机的速度。

Vite 支持多页面，不需要任何额外的模块。你可以简单地添加一个带有页面名称和 index.html 文件的文件夹，Vite 会让这个 URL 不需要任何额外的步骤就能工作。

Vite 还有一个遗留模式插件，可以处理不支持原生 es 模块的浏览器。虽然，这只有在你必须支持 Internet Explorer 时才有用，但希望你不支持。

## 速度呢？

![](img/5ee7261d76477b6666ce7feedb1e8a83.png)

乔伊·班克斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

鉴于 React 的流行程度，我们可以对比一下用 Vite 和 Create-react-app 创建新项目的速度。

创建一个新的 Vite 项目需要更多的步骤，因为您需要选择框架，然后安装依赖项。但是速度差异真的让我震惊。

启动并运行一个 Vite 项目只花了我**30 秒**。这很快，但真正的理解来自比较。标准的 create-react-app 用了大约 **2 分 20 秒**才启动并运行。

常用的 Webpack 甚至不能远程比较。这种差异确实令人惊讶，也是促使我开始使用 Vite 的原因。

# 最后的想法

总之，Vite 对于开发者来说是一个很好的工具，可以大大加快捆绑过程。难怪它能够在社区中获得如此多的喜爱，成为我的首选。尝试一下，看看你的开发过程会变得更加无缝。

# **其他资源**

[](https://vitejs.dev/guide/why.html) [## 为什么邀请|邀请

### 下一代前端工具

vitejs.dev](https://vitejs.dev/guide/why.html)  [## 为什么我们从 Webpack 转向 Vite |黑客新闻

### 完全取代 webpack 不是 Vite 的目标。可能有少量的特性/功能…

news.ycombinator.com](https://news.ycombinator.com/item?id=26972400)
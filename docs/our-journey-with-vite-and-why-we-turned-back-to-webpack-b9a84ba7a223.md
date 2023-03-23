# 我们的 Vite 之旅以及我们为何回归 Webpack

> 原文：<https://betterprogramming.pub/our-journey-with-vite-and-why-we-turned-back-to-webpack-b9a84ba7a223>

## Vite 2 承诺了闪电般的开发者体验。抓住机会后，我们面临一些重大问题，不得不变得有创造性

![](img/ec205f49d67301936fe28425e3be3a8a.png)

[Robynne Hu](https://unsplash.com/@robynnexy?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

多年来，我们使用 Webpack 和 typescript 编译器`[tsc](https://www.typescriptlang.org/download)`来捆绑我们的应用程序。启动开发服务器需要很长时间，热模块替换需要很长时间来更新浏览器，这真的很难保持专注。很多时候，我们在等待浏览器显示更改时，会切换到查看新闻栏或社交媒体。

Vite 2 承诺了闪电般的开发者体验，并在 2021 年 3 月终于稳定下来。我们不得不抓住这个机会。这是把我们的开发者体验带到下一个层次的工具。因此，我们做出了决定，建立 Vite 开发环境变得非常容易。

这是我们使用 Vite 的旅程，也是促使我们再次使用 Webpack 的主要问题。

# Vite 的出色性能

我兑现了承诺。启动开发服务器需要半秒钟！热模块更换几乎是瞬间完成的。这正是我们所希望的！

页面重新加载有点令人失望。这花了大约 4 秒钟，主要是因为应用程序太大了。我们公司的主要产品是一个用于俱乐部和协会的[CRM](https://www.webling.eu/)，它有一个巨大的前端，由 1300 个文件组成，包含 160000 行打字稿和 [vue 2 模板](https://vuejs.org/guide/scaling-up/sfc.html#single-file-components)代码。所以不奇怪，浏览器加载 1'300 个模块需要一些时间。但是总的来说，开发者的体验已经改善了很多。

使用 Vite 一段时间后，随着时间的推移，两个痛点变得更加严重。

# 1.不使用 Vite 开发服务器进行类型检查

类型检查是 TypeScript 的主要优点之一。编码时，大多数错误都是通过类型检查发现的。这样可以节省很多时间，避免很多挫败感。如果代码通过了类型检查，那么只剩下逻辑错误。所有的语法错误都是在类型检查中发现的。

Vite 在幕后使用 [esbuild](https://esbuild.github.io/) 将 typescript 编译成 javascript。Esbuild 运行速度很快，但是它忽略了所有类型信息。这意味着我们失去了 typescript 的主要优势。Vite 有一个类型检查工具——`[vue-tsc](https://github.com/johnsoncodehk/volar/tree/master/packages/vue-tsc)`——但是它没有监视模式。这使得它在开发服务器中毫无用处。

我们的产品是在 CI 上编译的，所以我们很少在本地编译它。这种情况经常发生，代码在本地 dev 服务器上运行时没有任何错误，但是在 CI 上却失败了。由于 CI 只需不到一个小时的时间就能完成，因此感觉比旧的 Webpack 解决方案更痛苦。在一个新特性上工作了一个小时之后，我们不得不回去修复一些在配置项上发现的小的类型错误。

# 2.使用 Esbuild，Typescript 接口变成空文件

我们像所有人一样，为 typescript 接口使用了通用的默认导入:

```
import IExportName from '@/path/to/IExportName'
```

如果模块只包含一个接口，Vite 将无法运行应用程序。这是因为 esbuild 忽略所有类型信息，并且文件编译为空模块，无法导入。存在一个解决方案

```
import **type** IExportName from '@/path/to/IExportName'
```

但是这意味着所有的接口导入都必须改变。此外，IDE 会在自动导入时生成普通导入。这非常令人不快，因为每次自动导入都必须手动纠正。

# 两全其美

现在我们要在斯库拉和卡律布狄斯之间做出选择。我们应该使用慢速的 Webpack 设置还是不进行类型检查就使用快速的 Vite？

在一个漫长的夜晚，一个绝妙的想法出现了:如果 Vite 通过使用 esbuild 实现了闪电般的开发人员体验，为什么我们不能在 Webpack 中使用 esbuild？

一个简短的研究就足够了:存在一个用于 esbuild 的 Webpack 加载器— `[esbuild-loader](https://github.com/privatenumber/esbuild-loader)`！我们找出我们的 Webpack 配置文件，并使用`esbuild-loader`来编译和缩小文件。这样更快，但是我们失去了类型检查，因为 esbuild 忽略了类型。

解决方案是使用`[fork-ts-checker-webpack-plugin](https://github.com/TypeStrong/fork-ts-checker-webpack-plugin#readme)`。这个插件跨越了一个单独的进程，它运行 typescript 编译器来检查类型错误。因为它在一个单独的进程中运行类型检查，所以它不会降低 dev 服务器的速度。

Webpack 配置的重要部分非常简单:

因此，Webpack dev 服务器在 12 秒内启动，页面重新加载需要 4 秒。热模块更换会在 2 到 4 秒内更新浏览器。

这没有 Vite 设置快，但比我们最初的 Webpack 设置快得多。

# 结论

我们的 Vite 之旅给了我们快速的开发体验，但减少了类型检查的成本。对我们来说，快如闪电的开发服务器并没有弥补连续类型检查的不足。

在我们的 Webpack 设置中集成 esbuild 允许我们保持类型检查的安全网，同时拥有足够快的开发人员体验，不会被开发服务器的编译时间分散注意力。

# 你有哪些经历？

你有使用 Vite 和 typescript 的经验吗？我期待着听到你从 Vite 学到的经验教训。在开发过程中，你如何处理类型安全？
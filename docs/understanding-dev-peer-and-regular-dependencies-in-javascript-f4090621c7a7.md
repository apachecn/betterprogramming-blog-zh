# 理解 JavaScript 中的开发、对等和常规依赖关系

> 原文：<https://betterprogramming.pub/understanding-dev-peer-and-regular-dependencies-in-javascript-f4090621c7a7>

## 了解 JavaScript 应用程序中包是如何协同工作的

![](img/7eb20031807440060b768dc6ea610886.png)

瑞安·昆塔尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当构建一个现代 JavaScript 应用程序时，您可能会依赖许多其他包和第三方依赖项。例如，您可以将`react`和`react-dom`用于您的 UI，将`lodash`用于实用助手方法，将`webpack`或`rollup`作为您的绑定器，将`jest`和`@testing-library/react`用于单元测试。

所有这些包都作为依赖项存储在您的`package.json`文件中。对于新开发人员来说，理解是否将它们作为`dependencies`、`devDependencies`或`peerDependencies`有时是一场斗争。

在本文中，我们将看看在`package.json`文件中发现的三种类型的依赖关系，以及它们各自的作用。

# 常规依赖关系

`dependencies`您的应用产品捆绑包中是否使用了软件包。如果你正在构建一个 React 应用，那么`react`和`react-dom`就是`dependencies`。

如果您使用`react-router`进行客户端路由，那也是您的`dependencies`的一部分。任何其他包，如`lodash`或设计系统库，如 Material UI ( `@mui/material`)也将是`dependencies`。

这个规则有一个例外，但是我们将在下面的对等依赖小节中讨论这个问题。

# 开发依赖项

`devDependencies`通常是你用来构建项目的工具，但它们实际上并不包含在应用的产品捆绑包中。例如，`webpack`和`rollup`都是`devDependencies`。这些是用于编译你的应用程序的捆绑器，但它们不是你的应用程序的一部分。

这同样适用于测试框架和库，如`jest`、`@testing-library/react`、`karma`、`mocha`或`jasmine`。这些库是为您的代码编写单元测试所必需的，但它们不是您的生产应用程序的一部分。

像`eslint`、`prettier`、`commitlint`、`husky`和`lint-staged`这样的 Linters 和代码格式化器也都是`devDependencies`。

# 对等依赖

`peerDependencies`是您的应用程序依赖的依赖项，但期望另一个包提供。`peerDependencies`是减少最终产品应用程序大小的重要工具。我们来看看为什么。

假设您有一个由许多 npm 包组成的大型 React 应用程序。假设有 20 个人，他们都使用 React。如果这些包中的每一个都在它们的`dependencies`部分列出了`react`和`react-dom`，那么这意味着你的应用中将有 20 个 React 副本！(21 如果算上主母应用提供的副本。)

为了避免这种依赖性的巨大膨胀，库开发者可以使用`peerDependencies`部分来指定他们的库依赖于`react`和`react-dom`，但是包的消费者需要提供它。

这样，你就可以让你的应用有 20 个依赖项，但是只有一个`react`和`react-dom`的副本用于生产，在你的父应用的`package.json`文件的`dependencies`中提供。

当使用`peerDependencies`时，需要注意的是，如果您将一个包标记为对等依赖，那么您也必须将它包含在您的`devDependencies`部分中。这允许您的应用程序在本地开发和单元测试期间安装和使用像`react`和`react-dom`这样的包，但是它不会将它们作为真正的依赖项包含在生产版本中。

最后，当使用 peer/dev 依赖组合时，您还应该在 Webpack 或 Rollup 配置文件的`externals`配置部分中添加这个包。

# 结论

就是这样。现在您知道了如何在任何 JavaScript 项目中正确地使用 dev、peer 和 regular 依赖项。这样做将帮助您减少应用程序的最终包大小，并确保在生产中只安装必要的包。

感谢阅读！
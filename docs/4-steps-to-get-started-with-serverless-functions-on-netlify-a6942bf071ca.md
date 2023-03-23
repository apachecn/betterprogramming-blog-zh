# 在 Netlify 上开始使用无服务器功能的 4 个步骤

> 原文：<https://betterprogramming.pub/4-steps-to-get-started-with-serverless-functions-on-netlify-a6942bf071ca>

## 前端开发人员最强大的工具

![](img/8297f8575e533f56d3b3f2bbfe0732c5.png)

克里斯蒂娜@ wocintechchat.com 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片[。](https://unsplash.com/@wocintechchat?utm_source=medium&utm_medium=referral)

你已经听到这个时髦的词有一段时间了:无服务器功能。每个云主机都在使用它们。他们听起来很简单！

但是为什么前端开发者想要使用它们呢？他们好吗？

在这篇文章中，我想告诉你我所知道的关于无服务器函数的一切。我不打算深入细枝末节。我想说一下它们是什么，为什么你们作为前端开发者要用它们。

# 什么是无服务器功能？

无服务器功能是在 Faas 平台上执行应用程序逻辑而不存储数据的功能。

如今有许多 FaaS 供应商。亚马逊用 AWS Lambda 让它们流行起来。从那以后，FaaS 平台的崛起获得了它的牵引力。

目前，还有很多。[谷歌云功能](https://cloud.google.com/functions)、 [IBM 云功能](https://cloud.ibm.com/functions/)、微软 Azure 功能[是大玩家。](https://azure.microsoft.com/en-ca/services/functions/)

Netlify 也许还没有 Google、IBM、Amazon 或微软那么大，但它更强大，因为它使得创建和托管无服务器功能变得如此容易。

所有这些提供商都有一个免费层来测试他们的无服务器功能。太棒了！

在本文中，我将使用 [Netlify](https://www.netlify.com/products/functions/) 。

# 为什么应该使用无服务器功能？

你想知道为什么你应该使用无服务器功能吗？

如果你熟悉后端的 JavaScript，你应该知道 Node.js(新的替代物 Deno 听起来也很有前途)。Node.js 是一个 JavaScript 运行时，非常适合用于 API。

作为前端开发人员托管 Node.js 应用程序可能有点麻烦。你必须开始和停止它。

使用无服务器函数，您可以进行计算、API 调用等等——就像使用 Node.js 一样——但是无服务器函数要简单得多。

每个无服务器函数都存在于一个 JavaScript 文件中。您也可以使用共享逻辑或依赖关系。

作为前端开发人员，我们不必担心管理 Node.js 应用程序。

我们在项目中部署了一个`functions`文件夹，并将其部署到 Netlify。每个 JavaScript 文件现在都变成了一个 API 端点。

# 如何创建无服务器功能？

既然已经清楚了为什么无服务器函数是强大的武器，我们将自己创建一个无服务器函数。

## 1.设置项目

为这个项目创建一个简单的 GitHub repo。使用您的 GitHub 帐户登录 Netlify 网站。接受所需的授权。

## 2.将项目添加到网络生活

Netlify 的 CI/CD 特性非常棒。不再需要手动部署，甚至不再需要通过 FTP 传输文件。

将您的 GitHub 项目添加到 Netlify，您就可以开始了。

## 3.设置无服务器功能

将你的 GitHub 项目克隆到你的电脑上。在主目录下创建一个名为`functions`的文件夹和一个`netlify.toml`文件。

在`netlify.toml`中，我们将运行无服务器功能所需的设置放在本地计算机上并联机。

`functions`文件夹中的每个 JavaScript 文件都有相同的基础。

当您调用端点时，`event`变量包含关于请求的信息(例如头、主体和路径)。

在`functions`文件夹中创建一个名为`hello.js`的文件，内容如下:

提交并推动它。转到 Netlify 上的项目，等待构建完成。

## 4.检查无服务器功能 URL

点击导航中的“功能”。如果你在`master`部门工作过，那么你会在列表中找到`hello.js`。如果您在特征分支上工作，您必须在输入字段中搜索。

点击您的`hello.js`功能，您将看到您的无服务器功能的 URL。将其复制并粘贴到您的浏览器中。结果应该是这样的:

编写您希望无服务器函数具有的任何逻辑。您可以在任何您喜欢的应用程序中使用它！

# 替代方案是什么？

当然，也有替代方案，比如 Node.js 应用程序。

如果您喜欢编写 JavaScript，但不太喜欢保持服务器在线，这是一个非常强大的选择！

是的，您可以将 Node.js 放入 Docker 容器，但是您仍然需要管理它。

无服务器函数存在于您的项目中。你在网上主持。这是保持简单的好方法！要我说，这对前端开发人员来说再好不过了！

# 结论

我自己正在研究 Netlify 上的无服务器功能。我将把它们与 FaunaDB 结合起来，所以你可以期待一些未来的文章。

你怎么看待无服务器功能？你打算用它们还是用它们做实验？请在评论中让我知道。

> 我在一个不和谐服务器上聚集了几个来自世界各地的有抱负的开发者，如果你想加入，请随意。

# 阅读更多

[](https://levelup.gitconnected.com/jump-start-your-developer-career-from-zero-to-hero-89cb62a1829) [## 从零开始你的开发者生涯

### 一个简单的方法会让你大吃一惊！

levelup.gitconnected.com](https://levelup.gitconnected.com/jump-start-your-developer-career-from-zero-to-hero-89cb62a1829) [](https://medium.com/dev-together/5-steps-give-structure-to-your-development-projects-e1348eb9f17d) [## 5 个步骤为您的开发项目提供结构

### 你不能管理你的编程项目吗？试试这个！

medium.com](https://medium.com/dev-together/5-steps-give-structure-to-your-development-projects-e1348eb9f17d) [](https://levelup.gitconnected.com/how-to-build-a-dark-mode-switcher-with-css-variables-ccb13f7441a0) [## 如何用 CSS 变量构建一个黑暗模式切换器

### 用 CSS 变量、JavaScript 和 TypeScript 构建一个黑暗模式切换器

levelup.gitconnected.com](https://levelup.gitconnected.com/how-to-build-a-dark-mode-switcher-with-css-variables-ccb13f7441a0) [](https://medium.com/dev-together/javascript-concepts-you-need-before-starting-w-frameworks-libraries-25a325312b5c) [## 开始使用框架和库之前需要的 JavaScript 概念

### 在你熟悉它们之前不要开始

medium.com](https://medium.com/dev-together/javascript-concepts-you-need-before-starting-w-frameworks-libraries-25a325312b5c)
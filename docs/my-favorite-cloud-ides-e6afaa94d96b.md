# 我最喜欢的云理念

> 原文：<https://betterprogramming.pub/my-favorite-cloud-ides-e6afaa94d96b>

## 消除管理开发环境的麻烦

![](img/aac062534fe01a67455cb1238f578d86.png)

[杰森·黑眼](https://unsplash.com/@jeisblack?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

**更新(2021 年 2 月 19 日)**:本文的新版本已经发布:

[](https://medium.com/better-programming/my-4-favorite-cloud-ides-for-2021-2f3c09d78eaa) [## 2021 年我最喜欢的 4 个云理念

### Glitch、Gitpod 等等

medium.com](https://medium.com/better-programming/my-4-favorite-cloud-ides-for-2021-2f3c09d78eaa) 

*原文如下。*

建立和维护开发环境本身就已经够麻烦的了。但是如果你同时在多个项目上工作，你会有另一个麻烦:管理多个开发环境！

这些年来，我尝试了许多工具和策略来管理多个开发环境，从`nodenv`和`rbenv`，到像`vagrant`这样的工具。每个人都有自己的时间和地点。

但是现在，我继续前进，并为我的工具箱找到了一个新工具——云 IDE。这是我最喜欢的完全避免管理开发环境的方法。Cloud IDEs 使得给每个项目一个独立的开发环境变得很容易，这个环境位于一个容器中，由其他人管理。

当我说“云 IDE”时，我指的是开发环境即服务。一个完全托管、功能全面的环境，可以通过 web 浏览器在终端级别运行命令和访问文件系统。

有许多云理念，迎合各种口味。在编辑我最喜欢的列表时，我考虑了 20 多个选项。因为我的主要兴趣是将参与 [JAMstack](https://jamstack.wtf/) 项目作为业余爱好，所以我的主要标准是:

*   支持 nodejs。
*   与 GitHub 集成。
*   Visual Studio 代码的外观。
*   好玩。
*   不贵。

# 我的最爱

## 最好玩的:[小故障](https://glitch.com/)

如果您最感兴趣的是编写基于 Node.js 的 web 应用程序，Glitch 是一个很好的选择。Glitch 非常固执己见，使得开始编写代码变得非常容易，而不必决定如何配置大量繁琐的细节。界面简单易用，让您专注于代码。最棒的是，Glitch 是免费的！

Glitch 不仅仅是一个云 IDE。它是一个在线社区，一个受管理的源代码控制库和一个部署平台。部署平台意味着你可以在不使用任何其他工具的情况下与全世界分享你的项目，这非常方便。它还提供了一个 Visual Studio 代码扩展，允许您使用桌面应用程序来编辑云中托管的 Glitch 项目。Glitch 可以从 GitHub 导入项目，也可以将项目导出到 GitHub。

无论您是编程新手，想要在协作环境中学习，还是正在从事可能成为下一个大事件的副业项目，Glitch 都能满足您的需求。

## 最无缝的工作流程: [Gitpod](https://www.gitpod.io/)

当您想将 GitHub 或 GitLab 用于其托管源代码控制库、集成和社区工具时，以及当您想使用 Node.js 之外的其他运行时时，Gitpod 是我的选择

Gitpod 与 Visual Studio 代码感觉完全一样，它支持许多相同的扩展。Gitpod 没有带来 Glitch 那样的乐趣，但就其支持的语言而言，它要灵活得多。然而，Gitpod 最突出的一点是它与 GitHub 和 GitLab 的集成有多好。从登录到签出项目，再到提交更改，体验与源托管平台无缝集成。Gitpod 也是一个很好的方法，可以快速查看您在 GitHub 或 GitLab 上偶然发现的项目，并与它们一起玩。而且很容易上手，因为 Gitpod 还有一个免费层！

Gitpod 也不仅仅是一个云 IDE，而是以不同于 Glitch 的方式。这是一个持续开发环境平台。这意味着 Gitpod 可以配置为在每次发生变化时自动重建您的开发环境，允许您的环境总是预先构建所有的依赖项，并在您打开 IDE 时等待您。

Gitpod 的基本理念是开发环境应该廉价且短暂。它们应该按需使用，用于进行和审查变更，然后在代码被提交或审查后被丢弃。

与 Glitch 不同，Gitpod 不包含自己的持续部署或托管平台。相反，您将这些服务直接连接到 GitHub 或 GitLab 存储库，以便在每次从 Gitpod 提交时触发。

# 也有意思

## 最好玩，亚军: [repl.it](https://repl.it/)

服务是一个有趣的云集成开发环境，更侧重于引导学习。它支持多种编程语言。它基于 Visual Studio 代码，侧重于社区，并具有从 GitHub 导入的能力。该服务还包括源代码管理和虚拟主机，减少了你需要使用的工具数量。而且是免费的！

## 最令人兴奋的: [RunKit](https://runkit.com/home)

RunKit 不是一个云 IDE，而是一个交互式 Node.js 笔记本。与大多数代码不同，它可以运行持久的服务器端进程并公开 web 端点。但它远不止这些，还有输出可视化和时间旅行调试。观看[视频](https://static.runkitcdn.com/assets/videos/demo.mp4?v=runkit)，准备好大吃一惊吧。

# 大片的云彩

每个主要的云供应商都有一个托管的 IDE。其中一个可能适合你。它们通常包括一些便利，比如让云平台的命令行工具和认证令牌在默认情况下可用。

## [谷歌云外壳文件编辑器](https://cloud.google.com/shell/docs/viewing-and-editing-files)

谷歌云壳对谷歌云平台的用户是免费的。它包括一个基于忒伊亚的文件编辑器，Visual Studio 代码用户会感觉很熟悉。

## [AWS Cloud9](https://aws.amazon.com/cloud9/)

AWS Cloud9 IDE 是原版之一。感觉不像 Visual Studio 代码，也不是免费的。

## [Azure Visual Studio 代码在线](https://azure.microsoft.com/en-us/pricing/details/visual-studio-online/)

Visual Studio 代码来自为您提供 Visual Studio 代码的公司。谁不喜欢原创呢？不管怎样，我没有用过这个，但是如果你已经在用 Azure 了，也许它有意义。这不是免费的。

# 我最喜欢的云替代方案

有时，您可能想要在容器中进行开发的好处，而不必依赖于云。在这种情况下， [Visual Studio 代码开发容器](https://code.visualstudio.com/docs/remote/containers)特性是一个很好的选择。感觉和用 Gitpod 差不多，但是一切都用 Docker 在本地运行。

# DIY 云 IDEs

我在寻找云 ide 时遇到的一些项目采用了 DIY 方法，要求用户提供自己的服务器来托管开发环境。虽然这不是我想要的，但您的情况可能需要使用您自己的服务器，在这种情况下，您可能会对这些项目感兴趣:

*   [Code-server](https://github.com/cdr/code-server)by[Coder](https://coder.com/)—在浏览器中运行 Visual Studio 代码，托管在您可以通过 ssh 访问的任何开发机器上。
*   [Koding](https://www.koding.com/)—让您的组织创建并共享全自动开发环境。
*   [Eclipse Che](https://www.eclipse.org/che/)—Kubernetes——面向开发者团队的原生 IDE。
*   [ICEcoder](https://icecoder.net/)—基于 PHP 的浏览器代码编辑器。
*   [Codiad](http://codiad.com/)—2018 年以来未维护。

# 网络游乐场

以下服务是网络游乐场；它们看起来类似于云 ide，但是它们不包括服务器/虚拟机/容器环境:

*   CodeSandbox —一个用于快速 web 开发的即时 IDE 和原型工具。
*   [CodePen](https://codepen.io/) —面向前端设计师和开发者的社交开发环境。
*   [JSFiddle](https://jsfiddle.net/) —卑微的代码游乐场。
*   JSBin —网络语言实验工具。
*   [Codeply](https://www.codeply.com/)—快速、免费的在线编辑器，包括几十个框架、入门模板和超过 40，000 个代码片段。
*   [StackBlitz](https://stackblitz.com/) —测试版全栈。

# 其余的

以下是我遇到的其余云 IDE 服务的列表:

*   [codeanywhere](https://www.codeanywhere.com/)—开发者协作平台。
*   goor mide—强大的云集成开发环境，最大化开发者和团队的生产力。
*   source lair—在您的浏览器中进行无摩擦开发，提供卓越的文本按键绑定。
*   用于开发网站的强大在线集成开发环境。
*   [PaizaCloud](https://paiza.cloud/)—基于浏览器的 web 开发环境。
*   [code tasty](https://codetasty.com/)—现代、智能&可扩展的云 IDE。
*   [Orion hub](https://orionhub.org/)—运行在云中的现代开源软件开发环境。

# 结论

并不缺少选择。无论您是想要云原生 IDE，还是混合云 IDE，还是私有云 IDE；无论你是学习 JavaScript 的初学者，还是经验丰富的专业编写 C++/Java 的人。我希望这篇关于可用选项的综述对您有所帮助！
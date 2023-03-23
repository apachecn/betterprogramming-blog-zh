# 来自黑暗世界的信件反应样板

> 原文：<https://betterprogramming.pub/letters-from-the-dark-world-of-react-boilerplate-5de9b4b8e2a3>

## 介绍 ReacType，这是一个原型工具，允许您在几分钟内创建 Next.js 或经典 React 应用程序的整个框架

![](img/477d57543c7ae327eacfab0da848d50b.png)

由[卡罗来纳·皮门塔](https://unsplash.com/@carolinachadwick?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

“样板文件”在 React 开发人员的某些圈子里是一个肮脏的词。有些人将样板文件与懒得学习 webpack 的绿色工程师联系在一起，有些人一想到他们的技术可能会被简化为`git clone`就退缩，还有一些人只是不想要对他们的应用程序如何构建过于固执己见的样板文件。

然而，即使是最顽固的 React 开发人员也不能否认，构建新应用程序的早期阶段是相对平凡的——构建基本的文件结构，配置 webpack/TypeScript/Babel，创建组件层次结构，并添加基本样式，以便应用程序合理地类似于我们对 MVP 的愿景。这些都是重要的步骤，但是大概*不是*这些步骤是我们开始构建 MVP 时感到兴奋的。

当我们开始创建一个新的 MVP 时，我们的主要动机是解决一个特定的问题，或者告诉世界一个特定的想法/产品。寻找 webpack 配置中缺少的逗号并摆弄 Flexbox 属性并不能让我们兴奋地开始一个项目。我们希望尽快开始构建有趣的应用程序逻辑。

从头开始编写 React 应用程序和使用一个过度构建的模板之间有没有中间地带？我一头扎进 React 样板代码的黑暗世界，这里是您的选项的高级分类:

*   零/低配置工具
*   特定技术堆栈的样板回购
*   按代码设计的工具

# **零/低配置工具**

低配置 react 样板文件之王是 [create-react-app](https://reactjs.org/docs/create-a-new-react-app.html) ，React 用来创建零配置单页面应用程序的工具。React 的网站[是这样描述它的](https://reactjs.org/docs/create-a-new-react-app.html):

> “Create React App 是学习 React 的舒适环境，也是在 React 中开始构建新的单页面应用程序的最佳方式。
> 
> …
> 
> Create React App 不处理后端逻辑或数据库；它只是创建了一个前端构建管道，因此您可以将它用于任何后端。在引擎盖下，它使用了 Babel 和 webpack，但是你不需要了解它们的任何东西。"

尽管没有配置，create-react-app 的可定制性令人惊讶。添加 TypeScript、GraphQL 和 SASS 就像安装软件包一样简单。值得注意的是，Next.js 有一个类似的实用程序( [create-next-app](https://create-next-app.js.org/) )，它允许您非常快速地创建 Next.js 应用程序。

如果您的目标是在设置和维护期间最小化配置，那么 create-react-app 是您的最佳选择。create-react-app 的明显缺点是配置被抽象掉了。如果你发现自己需要一个 create-react-app 不支持的配置，你要么需要[退出 app](https://create-react-app.dev/docs/available-scripts/#npm-run-eject) ，写一个[定制的 react-script](https://auth0.com/blog/how-to-configure-create-react-app/) ，或者使用一个像 [react-app-rewired](https://github.com/timarney/react-app-rewired/) 或 [customize-cra](https://github.com/arackaf/customize-cra) 这样的包。

隐藏配置的另一个缺点是，您将错过熟悉构建过程如何工作的机会。你可以说我守旧，但我相信卷起袖子和 webpack 一起在泥地里摔跤可以塑造性格。

使用 create-react-app 也不会完全免除您编写通用代码的责任。您仍然需要从头开始构建您的基本组件结构和样式——更不用说您决定插入的任何其他框架所需的样板文件了。

# **特定技术堆栈的样板回购**

分散在 GitHub 中的 [React 模板](https://blog.bitsrc.io/11-react-application-boilerplates-for-2019-b49a8226ea54)并不缺乏。这些回购往往是最好的情况下，你正在寻找一个技术组合的利基模板。例如:

*   [安全电子模板](https://github.com/reZach/secure-electron-template):用 React 构建安全电子应用。
*   [React-boilerplate](https://github.com/react-boilerplate/react-boilerplate):React 模板，优先考虑开发者体验。
*   [React PWA](https://github.com/Atyantik/react-pwa) :构建渐进式网络应用程序来优化 SEO。

当用利基技术构建一个新的应用程序时，这些回购可以作为很好的参考，但要警惕大量克隆回购。这些回复往往更新不一致，并且经常包含与你的项目无关的代码。

像 create-react-app 一样，克隆这些 repos 而不花时间深入理解代码可能会剥夺你宝贵的学习经验。

# **按代码设计工具**

有各种形状和大小的技术堆栈的预写样板是很好的，但是如果您正在寻找 UI 级组件架构呢？幸运的是，这个过程的每个阶段都有解决方案。

对于那些对从导入的草图或基于预设库模仿 UI 感兴趣的面向设计的开发人员来说，有一个[成帧器](http://www.framer.com)，它允许用户从视觉和代码界面原型化组件、它们的动画和交互。这是一个很好的工具，它在设计和开发之间架起了一座桥梁。也就是说，团队仍然需要投入时间将它集成到他们的工作流程中，大规模的应用程序可能需要仅在产品的付费层中解锁的功能。

对于那些希望迭代现有组件的人来说，这里有[Bit](http://bit.dev)——一个用于孤立地管理、构建和测试可重用组件的完整生态系统。可以把它想象成一个独立组件的 GitHub，包括一个包注册表，甚至每个组件的版本控制。专注于代码库模块化的大型团队会发现这是一个非常有吸引力的资源。然而，对于拥有专有功能或需要复杂且高度相互依赖的组件的应用程序来说，它可能不太有用。

# **介绍 React type——一种生成 React 代码的新方法**

虽然上面提到的所有工具都很强大，但它们都至少有一个基本问题:开发人员仍然需要编写大量通用代码来创建类似于他们的 MVP 的应用程序。

我们与 OS 实验室的人合作创建了[React type](https://github.com/open-source-labs/ReacType)，这是一种创建样板 React 代码的完全不同的方法。ReacType 的目标是让您尽快构建一个正常运行的应用程序(包括样式化的组件)。

ReacType 是一个 React 原型工具，允许您在几分钟内创建 Next.js 或经典 React 应用程序的整个框架。使用拖放界面创建可重用的组件，添加基本的布局和样式，并实现路由。在构建应用程序时，应用程序代码是动态生成的。将项目导出为使用 TypeScript 和功能组件构建的功能齐全的 Next.js 或经典 React 应用程序。

ReacType 的美妙之处在于，您可以将它与 create-react-app 或其他样板代码生成器并行使用。不需要从 ReacType 导出整个应用程序，只需将组件导出到现有项目中。

[在 GitHub 上查看](https://github.com/open-source-labs/ReacType)下载应用程序或参与项目！

![](img/e35ceca7f5e3d083a4b912a04ff27090.png)

用 ReacType 在 30 秒内构建一个 Next.js 应用程序。
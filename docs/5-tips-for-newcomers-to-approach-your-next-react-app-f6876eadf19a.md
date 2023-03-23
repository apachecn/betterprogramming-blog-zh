# 新手使用下一款 React 应用的 5 个技巧

> 原文：<https://betterprogramming.pub/5-tips-for-newcomers-to-approach-your-next-react-app-f6876eadf19a>

## 为您的下一个 React 项目做出正确的决策

![](img/b16a0122af8a2a73c6fa19394db60497.png)

照片由[桑嘎里玛罗曼塞利亚](https://unsplash.com/@sxy_selia?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/hint?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

如今，开始一个全新的现代前端项目可能会变得相当有压力，因为在每个堆栈中有大量不同的特定工具。

但是，对于您的用例来说，使用某些服务于不同目的的工具是没有多大意义的，所以我想创建这篇文章来帮助新来者和/或老来者澄清一些秘密，只是为了帮助社区。

以下是在 2020 年的今天创建下一个 React 应用的五个技巧。

# 1.考虑一个构建工具

在这个列表中，我想做的第一件事是选择一个工具来帮助你完成构建步骤。它们被称为利用捆绑器的构建工具，如 [webpack](https://webpack.js.org/) 。

以下是一些推荐选项，您可以选择它们来帮助您开始使用:

## 创建-反应-应用

如果您想立即开始在 React 中编写代码，而不必担心项目是如何构建的，这可能是我的首要推荐。

正因为如此，它并不一定要在生产中运行应用程序——但是，如果您知道自己在做什么，您可以选择它作为起点，并将项目配置为生产就绪。

如今，开发人员用`create-react-app`来做这件事并不少见。

一些示例用例可以让您快速了解一些想法:

*   [create-react-library](https://github.com/transitive-bullshit/create-react-library) 将其用作开发服务器，与本地开发服务器(提供生产代码)一起运行，以帮助实时测试您的 react 库。
*   GDC 扩展了 create-react-app 来创建他们的 GDC 数据门户。
*   Tipe 使用 CRA 作为他们创建网站的原型，现在为他们的用户提供 graph QL/REST API 服务。
*   [song ear](https://songsear.ch/)扩展了 CRA 以支持歌曲搜索网站的通用渲染。
*   可能会对[进行扩展，构建 Chrome 扩展](https://github.com/jsmanifest/react-chrome-ext)。
*   可能会将[扩展到构建桌面应用](https://github.com/jsmanifest/electron-react-typescript-app)。

## 盖茨比

[Gatsby](https://www.gatsbyjs.org/) 是一个基于 React 的免费开源框架，帮助开发者构建高速网站和应用。

它充当一个静态站点生成器，这意味着所有的页面都将在构建时而不是运行时准备好。

然而，*并不意味着*不能用于动态页面。如果您需要静态页面的即时页面加载以及顶级的 SEO，Gatsby 的优势确实在博客、电子商务和登录页面等用例中大放异彩。

这实质上意味着，如果你正在建立一个内容不会经常改变的网站，你可能会从与盖茨比一起启动你的下一个项目中受益最多。

但是即使你正在建立一个内容经常变化的网站，它仍然是一个不错的选择！

它支持创建一个混合应用程序，您可以在其中创建动态的客户端路线。您可以利用它的构建时间和客户端运行时数据来构建混合应用程序。

点击阅读更多关于[将构建与客户端运行时数据相结合的信息。](https://www.gatsbyjs.org/docs/data-fetching/#the-benefits-of-the-hybrid-nature-of-gatsby-apps)

我还想提一下，他们有数百个[启动器](https://www.gatsbyjs.org/starters/?v=2)和[插件](https://www.gatsbyjs.org/plugins/)，你可以在你的项目中随时使用，这是一个巨大的优势。

以下是一些用盖茨比建立的制作网站的例子:

*   [reactjs.org](https://reactjs.org/)
*   [stateofjs.org](https://stateofjs.com/)
*   [sendgrid.com](https://sendgrid.com/)
*   [kentcdodds.com/blog](https://kentcdodds.com/blog/)
*   [serverless.com](https://serverless.com/)

## Next.js

利用 [Next.js](https://nextjs.org/) 的应用呈现在服务器端(SSR)和客户端。如果你打算利用像用户认证这样的服务器逻辑，可能更推荐使用这个工具来创建你的下一个 web 应用。

(请记住，GatsbyJS 也可以处理用户认证，但是您可能需要用更多的客户端代码来处理它)。

它提供的一个独特的概念是文件系统路由，其中文件夹中的每个文件都可以成为实际的服务器或客户端路由，一切都在您的控制之下。

GatsbyJS 使用类似的文件系统方法通过指定`src/pages`目录来构建它们的路由，但是它并没有真正深入处理那里的服务器逻辑。

以下是一些用 NextJS 构建的制作网站的例子:

*   [jobs.netflix.com](https://jobs.netflix.com/)
*   [m.twitch.tv](https://m.twitch.tv/)
*   [葫芦](https://www.hulu.com/)
*   [att.com](https://www.att.com/)
*   [ticketmaster.com](https://www.ticketmaster.com/)
*   [m.staples.com](https://m.staples.com/)
*   [futurism.com](https://www.futurism.com/)

## 卷曲

如果你正在寻找构建一个 React 库，那么 [rollup](https://github.com/rollup/rollup) 就是你要走的路。

# 2.考虑实用程序库

首先，显然你不需要*有一个实用程序库来帮助你构建下一个 React 应用程序，但是在你的项目中有一些通用的实用程序，你可以不用写自己的程序就可以使用，这当然很好。*

以下是您可以使用的一些选项:

## 洛达什

Lodash 是 npm 最流行、下载量最大的包，旨在为数组、字符串、对象和参数对象提供一致的跨环境迭代支持。在任何项目中作为通用工具包都是完美的。

它有各种可能的功能来帮助任何你能想到的情况。

它提供了一个全功能的方法，有许多有用的功能，如`.zip`、`.orderBy`、`.sample`、`.shuffle`、`.debate`、`.partialRight`、`.difference`等。

它甚至包装了一些本机循环函数，如`[.forEach](https://lodash.com/docs/4.17.15#forEach)`和`[.map](https://lodash.com/docs/4.17.15#map)`，以普通函数的形式提供它们。

## Rambda.js

像 Lodash 一样， [Rambda](https://ramdajs.com/) 是一个专门为函数式编程风格设计的函数式实用程序库。它旨在使创建实际上从不改变用户数据的功能管道变得容易。

我个人还没有用过它，但是从他们的文档来看，他们提供的工具*非常*有用。

## 数学. js

如果你计划在你的下一个项目中使用大量的数学，你可以利用 [Math.js](https://mathjs.org/) 来帮助你简化生活。

## 日期-fns(或 Moment.js)

几乎所有的 web 应用程序都需要在应用程序的某个地方显示某种日期。如果你需要做的只是显示日期，那么日期就变得很容易处理。

但是如果你需要更多的用例，比如操纵日期，这会变得非常有压力，如果你打算广泛地使用日期，你不应该自己处理日期。

帮助处理日期的一个流行的成熟实用程序库是经过多年考验的 [Moment](https://momentjs.com/) 。

这个库可能会变得很大，因此我现在推荐使用 [date-fns](https://date-fns.org/) ，这是一个较新的库，它提供了几乎相同的实用程序，但支持更模块化的方法，从而导致更小的包大小。

# 3.考虑一个 CSS 框架

不是每个人都有时间创建自己的时尚组件，尤其是如果他们对 CSS 毫无经验的话。

这就是为什么我提供这个列表来帮助那些想利用成熟的 CSS 框架的人，帮助他们扩展它们，作为创建自己的组件的起点。

## 样式组件

我几乎在每个项目中都使用[样式组件](https://styled-components.com/)。它有趣、快捷、有用，让我的生活变得简单多了。

它利用带标签的模板文字和 CSS 的功能来编写实际的 CSS 代码来设计组件的样式。它还将样式从 React 组件中分离出来，使得独立构建组件变得更加容易。

## 语义-用户界面-反应

Semantic-ui-react 是一个流行的库，提供 react 组件和小模块来帮助你创建用户界面。他们的 API 是精心设计的，他们的文档是有据可查的。

我经常将语义 ui react 与样式化组件结合使用。

## 材料-用户界面

[Material-UI](https://material-ui.com/) 是另一个模仿谷歌材质设计的流行库。它还提供了 React 组件和微型模块。和 semantic-ui-react 一样，他们的 API 设计得很好，文档也有据可查。

我在工作中使用 Material-UI，我喜欢它。

## 蚂蚁设计

[Ant Design](https://ant.design/) 是帮助构建 React 组件的流行选择。

## 蓝图

Blueprint 提供了一些组件，这些组件特别适合于构建关注桌面视图的复杂且数据密集的界面。

从组件库中，您可以获得一些代码来生成和显示图标，与日期和时间交互，选择时区等等。

## 反应引导

[react-bootstrap](https://react-bootstrap.github.io/) 中的每个组件都易于访问——这对前端框架构建非常重要。

## React 工具箱

像 Material-UI 一样， [React Toolbox](http://react-toolbox.io/) 是另一个你可以用来在你的项目中实现 Google 材质设计原则的库，因为它提供了 React 组件来简化你的界面的外观。

## 索环

[索环](https://v2.grommet.io/)被网飞这样的大公司使用。这是另一个 React 组件库，提供了多种组件来帮助您构建界面。

## 美女

[Belle](http://nikgraf.github.io/belle/#/) 为您提供了一套 React 组件，如 Toggle、ComboBox、Rating、TextInput、Button、Card、Select 等。

他们的组件经过优化，可以在移动和桌面设备上工作。它们提供了一个高度可定制的 API，允许开发人员定制他们的组件来满足他们的需求。

## React-md

[react-md](https://react-md.mlaursen.com/) 的目标是通过提供 react 组件库，帮助开发者创建完全可访问的材料设计风格的网站。

# 4.考虑在 React 社区周围

保存 React 社区的链接(比如书签)并经常查看它们非常重要，这样你就不会错过重要的更新。

React 生态系统非常活跃，如果你失踪时间很短(例如一个月)，你可能会错过很多东西。

当他们发布他们的 [React Hooks](https://reactjs.org/docs/hooks-intro.html) 特性时，人们已经开始在一周内将它们实现到他们的产品代码中。

像 [react-use](https://github.com/streamich/react-use) 这样的库很快出现了，很多人很快意识到它们比类组件更好的地方，比如可组合性和更少的代码。

此外，根据我的经验，每天以稳定的速度向我们的大脑中吸收新信息要比一个月后回来吸收大量的信息容易得多，这些信息是在您还在为您的类组件而紧张时讨论的，而 Hooks 可以轻松地解决您的问题。

这里有一些你可以保持密切联系的 React 社区。

## [开发到](http://dev.to)

dev.to 是一个受欢迎的社区，React 开发人员喜欢去那里玩。他们支持一个无毒的环境，在这里人们可以走到一起，一起快乐。这是我需要参与的社区。

## 中等

Medium 是另一个推荐社区，开发者可以在这里撰写和分享他们的专业知识和经验。有很多高质量的文章可以提供信息，尤其是如果你是 React 开发人员的话。

## 哈希诺德

Hashnode 是与来自世界各地的其他聪明的开发者联系的最简单的方式，他们的目标是帮助那些参与其中的人发展他们的职业生涯。

## Reddit /r/reactjs

Reactjs 子编辑是 React 社区中一些受欢迎的开发人员经常去的地方。

然而，如果你想寻找好的评论，我不建议你来这里。如果你对毒性敏感，请远离，转而与 [dev.to](https://dev.to) 交往。

# 5.去一家不无聊的咖啡店

我喜欢在一个不会感到无聊的环境中用 React 开发代码。我去的地方是一些星巴克，一些咖啡豆的位置，和一些当地的咖啡店。

我意识到，当我在一家没有死气沉沉(但也不太拥挤)、音乐不太响、员工喜欢相互交谈(有时听听背景中可爱、有趣的对话可以点亮气氛)的咖啡店时，我可以完成更多的工作。

当我享受写代码的时间时，我写的代码质量更好，因为我实际上是在享受写好代码的时间，而不是为了消磨时间而写。

# 结论

这篇文章到此结束！我希望你发现这是有价值的，并期待在未来更多！
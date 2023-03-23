# 我最喜欢的反应堆

> 原文：<https://betterprogramming.pub/my-favourite-react-stack-1beda91ae909>

## 从建立一个新项目到状态管理等等，这就是我所使用的

![](img/14b994b65e8de3c8ecd31b2164af0efa.png)

照片由 [Esther Jiao](https://unsplash.com/@estherrj?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果您熟悉 React 环境，您可能会知道确定使用哪个库是多么具有挑战性。由于 React 不是像 Angular 那样成熟的 JS 框架，我们需要额外的库来构建大型项目。然而，这不是一件容易的事，因为在每个类别中都有多个选项可供选择。

在这篇文章中，我将解释我最喜欢与 React 结合使用的库。这是我的观点，当然也是主观的。如果你对某些部分有不同的观点，请随意发表评论。

# 引导一个新项目

为了方便开始，我喜欢使用一个工具来启动一个新项目。这样我就不需要处理任何构建工具，它就能工作。

基本上，我总是选择其中一个选项:

*   NextJS
*   盖茨比（姓）

几个月前，我会选择[创建 React 应用](https://github.com/facebook/create-react-app)来构建动态 React 应用。然而，在我看来，NextJS 很快流行起来，似乎是一个更好的选择。它既支持静态页面，也支持服务器端页面(混合页面),通常更注重性能。

在构建一个完全面向公众的网站时，Gatsby 是我更喜欢使用的框架。我喜欢对 SEO 和开箱即用性能的关注，以及伟大的生态系统。有相当多的插件可用于轻松集成功能，如分析、CMS 和延迟加载图像。

所以总的来说:如果是面向公众的网站，我更喜欢盖茨比。如果我要构建一个动态(大型)React 应用程序，我会使用 NextJS。

# 状态管理

如果应用程序足够大，我喜欢用状态管理框架来补充我的应用程序。据我所知，有两个主要的库可以使用:

*   Redux
*   MobX

一年前，我一直倾向于 MobX，因为它带来了大量的样板文件 Redux。然而，自从 Redux 背后的创造者推出了 [Redux Toolkit](https://redux-toolkit.js.org/) 以来，这种情况最近发生了变化。这确保样板文件保持在最低限度，同时仍然享受这个库带来的结构。

自从这次调整之后，选择就有点难了。从我的角度来看，Redux 更适合更大的项目，在这些项目中，更多的结构是必要的，开发工具可以成为很大的资产。当项目规模相当小或中等时，MobX 是我最喜欢的选择。

MobX 最好与 [Mobx-react-lite](https://github.com/mobxjs/mobx-react-lite) 配合使用。钩子方法更适合功能组件。

# 式样

对于 React 中的样式，有相当多的好选项可供选择，比如 SASS、CSS 模块和 JS 中的 CSS。我更喜欢使用样式化组件的 CSS-in-JS 方法。所有选择都有利弊，但我喜欢它的运作方式。将样式放在组件定义附近以最小化上下文切换是一个主要优点。

样式化组件的一个缺点是需要大量的样板主题。下面是一个例子:

这也是我喜欢将 [xstyled](https://xstyled.dev/) 与样式化组件结合使用的原因之一。这确保了我们可以将前面的代码片段翻译成:

所有那些神奇的数字？它们被转换成主题值。`margin: 0 3;`例如可以变成边距 0px 16px。

# 航行

React 默认不带路由器。这意味着我们也需要为此选择一个库。我更喜欢最流行的一款: [React 路由器](https://github.com/ReactTraining/react-router)。它在 GitHub 上有超过 38k 的星星，使用起来非常简单。

尽管它是最受欢迎的，我还是想尝试一下 [Reach](https://reach.tech/router) 。它的设置非常相似，并且支持开箱即用的可访问性。[盖茨比从 v2](https://www.gatsbyjs.org/blog/2018-09-27-reach-router/) 就开始用这个了。

如果您想了解 Reach 路由器的辅助功能，请点击此[链接](https://reach.tech/router/accessibility)了解更多信息。

# 形式

为了将表单添加到我的应用程序中，我通常在这两个库中做出选择:

*   反应挂钩形式
*   福米克

这两者中最受欢迎的是[Formik](https://github.com/jaredpalmer/formik)——GitHub 上有大约 20k 名明星。然而，这一个可能是相当令人生畏的使用。它可以被描述为表单的一个扩展工具箱。我喜欢在构建包含许多不同表单的大型应用程序时使用它。

我个人最喜欢的是[反应钩形](https://github.com/react-hook-form/react-hook-form)。它完全支持新的 React hook 系统，并且易于设置和使用。对于大多数表单来说，这就足够了。然而，当必须编写复杂的表单时，也可以选择使用 Formik。

# 结束语

希望我最喜欢的 React 堆栈可以成为你决定构建一个新项目的起点。

这绝不是完美的堆栈，但在构建各种类型的应用程序时，它对我很有用。

感谢阅读。
# 我如何在一周内学会并构建一个 Nuxt.js 应用程序

> 原文：<https://betterprogramming.pub/how-i-picked-up-and-built-a-nuxt-js-app-in-one-week-889c8b87dee9>

## 这么多年后我重新学习 JavaScript 的旅程以及你如何开始

![](img/2850ced611915dab1de08d27f9a327db.png)

YAFIG Feed 页面

上图是我用一周时间学习 Vue.js 和 [Nuxt.js](https://nuxtjs.org/) 后搭建的。

我正在构建一个名为“又一个免费(OSS) Instagram 克隆”(YAFIG)的 Instagram 克隆项目。前端站点完全用 Vue.js 和 Nuxt.js 搭建，一周时间。

# 我是谁

我是一名全职软件工程师，从事 CDN 产品的工作。我整个职业生涯(大约三年)都在从事后端服务，主要是 Python、Java 和 [Lua](https://www.lua.org/) 。

有时我也会和 devo PS tool chains([Jenkins](https://jenkins.io/)，Docker，Kubernetes， [Puppet](https://puppet.com/) 等一起工作。).我有使用 [Flask](https://palletsprojects.com/p/flask/) 和 [Django](https://www.djangoproject.com/) 编写服务器端 web 应用的不错的经验。

但是，我的一个弱项是 JavaScript。我对前端编程和现代 JavaScript 编程一窍不通。很长一段时间，JavaScript 是我的敌人(也是我的恐惧)。

# 我的个人 JS 历史

![](img/e12879e366c57e28a0dff2cae2ed90b7.png)

就我个人而言，我很久以前就尝试过学习 JavaScript。2015 年，我曾经学过 [Ruby on Rails](https://rubyonrails.org/) 和 [Ruby Sinatra](http://sinatrarb.com/) (多么怀旧的框架)。那时候很时髦。还有 [MooTools](https://mootools.net/) 和 jQuery。

那时候我 15 岁；大多数时候，我只是在跟踪 RailsCast 的 YouTube 频道的教程。jQuery 和 MooTools 当时对我来说还可以，因为它们写起来非常简单(直到你面临 JS 中的异步问题)。

之后又出现了 [Ember.js](https://emberjs.com/) 、 [Backbone.js](https://backbonejs.org/) 、[流星](https://www.meteor.com/)、 [Knockout.js](https://knockoutjs.com/) 等众多框架。然后，Node.js 和 npm 出现了，还有 js 生态系统中的角度和依赖性问题。事情进展得太快了。

当时，我试图学习这些框架，以便跟上行业中最新的技术。不幸的是，我无法跟上他们的步伐。我放弃了 JS 世界，主要专注于后端编程，那里的东西更成熟(也更无聊)。

React.js 对外发布的时候，我一直想学习框架。我已经阅读了它的文档和教程，但我仍然不明白它是如何工作的。

结构对我来说总是很复杂，不是我所期望的。同样的事情也发生在 Ionic 框架上。当时我对 JS 世界持怀疑态度。每一个新发布的 JS 框架总会让我失望。我真的希望有一个新的 JS 框架像 jQuery 一样简单。

# 大为什么

你得找到你自己的“大为什么”你想做点什么。以前，我对 Vue.js 不太感兴趣。每个人都在谈论 React.js，但只有少数人提到 Vue.js。

然后，在我看了这个视频之后，一切都变了:

蜜罐的 VueJS 纪录片

在这个视频中，尤雨溪，Vue.js 的创造者和维护者，因为他的创造而被描绘成中国的英雄。旁白提到 Vue.js 的发布恰逢其时。

2014 年，当 Google 发布框架的第二版时，Angular.js 社区有点动摇，在第二版中，框架的完全重写导致了许多向后不兼容的问题。

另一方面，React.js 很棒，但对于许多人(比如我)来说，它们仍然太复杂了。

Vue.js 就是为了解决这个问题而建立的。这时候我被激起了兴趣！Vue.js 与 React 相比，进入门槛低得多，学习曲线也更低。

做了这么多研究，评估了 Vue 生态系统和权衡之后，我开始学习 Vue.js。

# 强烈要求编写应用程序

我脑子里有很多 app 的想法。太糟糕了，我自己不能把它们变成现实，因为我不能写正确的 JavaScript。对我来说这是个阻碍。我知道我必须挑选一些东西来构建一个完整的应用程序。jQuery 的模块化程度不足以让我开发一款现代应用。

我定下了自己的目标:学习一个现代的 JS 框架，从头到尾开发自己的 app。

我不打算为我的应用程序开发一个复杂的 UI(至少现在是这样)或者甚至进一步发展我作为前端工程师的职业生涯(同样，至少现在是这样)。对于基于 CRUD 的系统来说，一个合适的 UI 对我来说已经足够好了。

我的灵感来自于像 [lodash.debounce](https://medium.com/u/b1ba708ec566#debounce) 和 [axios](https://github.com/axios/axios) 的自动完成搜索(片段来自 Buefy 文档)。

*   使用谷歌分析进行用户跟踪。![](img/4eec200ab93313ef414e9ec9b739e039.png)

我的应用程序的简单演示

完整的代码可以在我的 GitHub 库中找到。

[](https://github.com/yafig/frontend) [## yafig/前端

### 在 VueJS (NuxtJS)中实现的前端站点，JAMStack-style - yafig/frontend

github.com](https://github.com/yafig/frontend) 

附:以后会多写这个项目。

我还没有掌握 [webpack](https://webpack.js.org/) 、 [Babel](https://babeljs.io/) 等底层 JavaScript 结构作品的概念。希望有一天我会。

# 我个人的看法

Nuxt.js 非常擅长简化 Vue.js，Nuxt.js 做的事情可能适合 80%的用例。如果您正在构建一个基于 CRUD 的系统，Nuxt.js 应该是您考虑的第一个框架。

尽管与 React.js 相比，Vue.js 生态系统在市场上的采用率较低，但我仍然相信 Vue 会继续存在。React 可能有一个大公司(脸书)支持这个生态系统，但 Vue 和 Nuxt 有 [GitLab](https://gitlab.com/) 、 [Doist](https://doist.com/) 和[更多的](https://madewithvuejs.com/)。

如果你想做一名前端开发人员，我个人建议你去 React，因为他们可能会在全球范围内为你提供更多的工作机会。但是，如果你只是想作为一个 solo 开发者完成你的事情，你应该试试 Vue.js。

# 我是如何学习 Vue.js 的

这一点我怎么强调都不为过；有优秀的资源让你开始学习 Vue 和 Nuxt。

*   专业建议 1:以 1.5 倍的速度观看，这样你不会很快厌倦观看它们。
*   专业技巧 2:跟着老师在屏幕上写的每一行，即使你还不明白它的意思。首先运行/执行它，然后谷歌它。

**免责声明:**我与 Traversy Media 没有关联，也不隶属于该公司。我只是觉得这些视频非常有帮助，希望你也一样。

你应该了解 Vue 一般是如何工作的。在本视频中，Brad 解释了 Vue.js 的基本概念。在观看下一个视频之前，一开始可能会觉得乏味:

在这个视频中，Brad 解释了 [Vuex](https://vuex.vuejs.org/) 如何帮助你管理应用程序中的状态。请注意这个视频，因为你以后每次编写 Vue.js 应用程序时都会用到它。

在这个视频中，Brad 通过构建一个简单而有趣的项目来解释 Nuxt.js 是如何工作的。在这个阶段，你应该理解 Nuxt 如何帮助你简化在 Vue.js 应用中管理状态和路由器(以及许多其他东西)的过程。

到第三个教程结束时，你应该能够开发自己的基于 CRUD 的应用程序。

你大概需要一天的时间来观看和尝试每一个视频。如果你仍然不明白如何编写一个简单的待办事项应用程序和爸爸笑话应用程序，也许你应该在 YouTube 上寻找更多的教程。

# 下一步是什么

至此，您应该对 Vue.js 和 Nuxt.js 的工作原理有了很好的基本理解。你不需要知道太多的细节就可以开始构建一个应用。

你现在可以探索 Vue 和 Nuxt 如何处理认证，如何将它们与 CSS 框架集成(例如，[布尔玛](https://bulma.io/)、[顺风](https://tailwindcss.com/)和[引导](https://getbootstrap.com/))，以及使用实用程序库(例如， [Lodash](https://lodash.com/) )。

然后，你就可以开始建设令人兴奋的项目了！

# 结论

说实话，我很高兴我发现了 Vue.js 和 Nuxt.js，谢谢你，[尤雨溪](https://medium.com/u/4f198f5f1f12?source=post_page-----889c8b87dee9--------------------------------)的创作。这是一个 JS 框架，在这么久之后，它再次让我兴奋。

现在有很多闪亮的新框架，比如[svelet](https://svelte.dev/)、 [Next.js](https://nextjs.org/) 、 [Gatsby](https://www.gatsbyjs.org/) 等等。就我个人而言，我还没有理由迁移到那些新的框架。我现在对 Nuxt 很满意，你可能也一样。
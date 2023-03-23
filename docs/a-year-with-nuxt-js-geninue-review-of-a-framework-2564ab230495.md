# Nuxt.js 一年——对框架的真正回顾

> 原文：<https://betterprogramming.pub/a-year-with-nuxt-js-geninue-review-of-a-framework-2564ab230495>

![](img/e6fb1c06956fe28d855576fc6e732ac6.png)

照片由[亚历山大·戈德罗](https://unsplash.com/@alexandre_godreau?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/mountain-home?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

一年多来，我每天都在用 [Nuxt.js](https://nuxtjs.org/) 工作。

在这篇综述中，我将尝试总结使用这个框架的所有起起落落。希望这篇文章可以说服您在新项目中尝试 Nuxt，同时注意一些注意事项。

## TL；速度三角形定位法(dead reckoning)

*   我每天在 Nuxt 工作已经有一年了
*   Nuxt 对于生产来说非常方便和神奇
*   Nuxt 是可扩展的，有很多特性，并且是由一个巨大的有才华的社区驱动的
*   Nuxt 确实有一些警告，对此我已经建立了一些变通办法

## 利益

*   表演的
*   结构
*   社区
*   文档(测试指南、部署指南)
*   模块(i18n、网站地图、SVG、Apollo、Axios、PWA、Sentry、GA、GTM)

## 警告

*   共享状态问题(节点问题)
*   和 Heroku (i18n 多域，网站地图)
*   `this`到处都是
*   微弱的`rx.js`支持

在我开始之前有一句话:我不知道它是否有意图，但我只是喜欢 Nuxt 的标志是一座山——就像你在一堵可靠的墙后面。

![](img/b97a208774daf9c6faa2ef3497915de8.png)

Nuxt 山脉标志

# 项目

首先，我想简单介绍一下这个项目。

一年前，2018 年 10 月，我的朋友[总而言之，相关性有时很差——对必需的编程技能和软技能的认识本身就是一个问题。](https://medium.com/u/4b902866d8d0#，ASP.net 等等)。</p><p id=)

[我们的想法是利用机器学习算法建立一个搜索系统，该系统可以识别任何职位描述文本、任何搜索文本和申请人的简历，因此我们可以直接显示与简历最匹配的职位。](https://medium.com/u/4b902866d8d0#，ASP.net 等等)。</p><p id=)

[这就是我们如何想出](https://medium.com/u/4b902866d8d0#，ASP.net 等等)。</p><p id=) [clusterjobs.de](https://clusterjobs.de) 这个主意的。我负责 web 应用程序，并成为这家初创公司的首席技术官，[拉米·阿尔萨尔曼](https://medium.com/u/4b902866d8d0?source=post_page-----2564ab230495--------------------------------)成为首席执行官和机器学习搜索引擎工程师。

![](img/33b1d115c5cca270388d336fef74cadc.png)

通过简历和技能识别进行搜索

# 为什么是 Nuxt？

首先，我想用一个长期的框架来启动一个项目，帮助我们快速启动，并能够扩展到未来。

另一个要点是有 SSR，因为我们希望有可持续的 SEO 作为主要的流量渠道。将 PHP 放在 SSR 的后端会导致复制所有的模板和加倍的工作，这是我们无法承受的，因为开发团队只有我一个人。

我开始研究 JavaScript SSR 解决方案，Nuxt 似乎是明显的赢家。有 2.0.0 主要版本和良好的文档，我们决定在新项目上冒险使用新技术。所以我们把 Nuxt 作为集群作业的框架。

# 使用 Nuxt 的部署和 CD

为了节省一些手动部署的时间，我花了几天时间建立了一个适当的 GitLab 管道来将应用程序部署到 Heroku。

关于如何部署到 Heroku， [Nuxt 文档](https://nuxtjs.org/faq/heroku-deployment/)是一个很好的资源。这里有一篇关于如何在 GitLab 管道中的 Heroku 上部署 Vue 的文章。将它们组合在一起——砰！这是我目前所拥有的:

在 Heroku 上部署 Nuxt 的 GitLab 管道配置

# 一年的发展

环境完成后，大约需要 2-3 个月来准备 MVP 并投入使用。经过无数次的迭代和改进，我依然不后悔选择 Nuxt。

那么为什么说它好呢？我想起了我经历过的最美好的时刻，它们是:

## 表演

这是表演。即使它是一个完整的 JS 框架，需要将所有的库文件交付给客户端，但它仍然尽力以最不有害的方式来做这件事。

![](img/f5a79ad801d912c3ebc5bc88ca4a4286.png)

Lighthouse on slow 4G，Macbook Pro 速度降低 4 倍

在最近的 2.10 更新中，我发现 webpack 配置已经被更新，所以在开发过程中只有更新的块被重建，这真的加快了开发速度。

此外，用于生产的 webpack 是可扩展的，您可以自己使用它或使用默认配置，这本身就具有很高的性能。

我的 webpack 构建配置。这是这方面的文件。

## 结构化

好处是，作为开发人员，我不需要考虑将这个或那个放在哪里。Nuxt 附带了一个应用程序的框架，拥有构建一个复杂的 web 应用程序所需的一切:页面、组件、资产、静态、中间件、插件等等。

唯一让我恼火的是，Nuxt 鼓励你使用`*~/component/blah-blah*`类的东西来导入整个应用程序。
打心底喜欢的 JetBrains IDE，认不出那些路径。

![](img/56745564e6626c0f5fb56cc732730217.png)

网络风暴未能识别路径

解决这个问题的方法很简单:

把这个文件放在你的 Nuxt 应用的根文件夹中，让你的导入路径起作用

![](img/c0b6015783cf0a61db665d8fb1b24b1d.png)

网络风暴现在成功地识别了路径

## 社区

这个社区欣欣向荣。非常感谢[塞巴斯蒂安·肖邦](https://medium.com/u/15ad6870b9ad?source=post_page-----2564ab230495--------------------------------)，他创造了 Nuxt 本身，并一直驾驶它直到现在。另一个巨大的感谢是由于核心团队和它的所有贡献者为这样一个惊人的产品。

如果你尝试过 Nuxt，你可能知道这些资源，但是我还是把它们放在这里:

*   全新更新的 Nuxt 网站,提供指南、文档和您可能感兴趣的一切
*   [这是开源社区的一个令人惊叹的平台](https://cmty.app/nuxt):关于 Nuxt 和`nuxt-community`模块的所有问题、疑问等等

## 模块

这才是让你爱上 Nuxt 的原因，真的。

来自这样一个伟大的社区，Nuxt 已经重新调整了 Vue 模块，新模块，和一切模块。在 Nuxt 中发现了一些没有覆盖的用例？写一个模块，让它 Nuxt-社区开源！下面是我在生产中使用的模块列表:

*   [**i18n**](https://github.com/nuxt-community/nuxt-i18n)
    国际化，每种语言都有多个域，开箱即用。好像很棒吧？语言和独立的域名对于 SEO 来说是至关重要的，可以大大提高你在 Google 搜索结果列表中的地位。在这里，你只需要添加模块，将其与你的用户界面集成，就是这样。
*   [**sitemap**](https://github.com/nuxt-community/sitemap-module)
    每个 prod 网站都需要的超级重要的东西。这个模块将根据你的路线生成一个网站地图。您还可以使用 Axios 请求您的 API，并获得所有动态路由(或任何您想要的东西)。
*   [**svg**](https://github.com/nuxt-community/svg-module)在整个应用程序中使用 SVG，但是从自动生成的精灵中加载
*   [**阿波罗**](https://github.com/nuxt-community/apollo-module)
    GraphQL 牛逼
*   [**pwa**](https://github.com/nuxt-community/pwa-module)
    PWA 是我们的未来——如果你还没做，那就试试调查这个话题吧！[三星开始在 Galaxy store 展示 PWA 应用](https://medium.com/samsung-internet-dev/introducing-progressive-web-apps-to-samsung-galaxy-store-47ecd317725b)。
*   [**哨兵**](https://github.com/nuxt-community/sentry-module)
    通过这项将为您记录所有生产错误的出色服务，了解您的用户的痛苦
*   [**GA**](https://github.com/nuxt-community/analytics-module) ， [**GTM**](https://github.com/nuxt-community/modules/tree/master/packages/google-tag-manager)
    追踪你 app 性能的必备邪恶

# 警告

当然，在我与 Nuxt 合作的这一年里，出现了一些问题。
现在，我将试着给出每一个问题的背景，并帮助你将来避免它们:

## Nuxt 中多个请求的共享状态

这和 Node.js 的工作方式有关。如果你有一个全局变量，它将在一个并发请求中被覆盖。在这个[堆栈溢出问题](https://stackoverflow.com/questions/48550284/how-to-keep-multiple-requests-separate-in-nodejs-expressjs)中给出了一些见解。

我遇到的问题与我的报价列表页面(搜索结果)上的`fetch`功能有关。

我做了一个行动号召，就像:

在服务器端进行搜索

这样做是为了在服务器端填充商店，并在客户端使用相同的产品。

但是当它在一个动作中完成时，比如 action -> commit -> store，那么由于某种原因，数据是混合的。

我承认，我没有调查真正的原因——可能是 Vuex 商店的某个全局对象或类似的东西，但问题是当我让我的应用程序为第一个请求运行时，每个下一个请求都有它的状态。因此，你可能最终会登陆[“全栈开发者”招聘页面](https://en.clusterjobs.de/search/fullstack developer)，并获得机器学习工程师的结果。

解决方法是:

直接从 fetch 函数提交

所以动作->返回获取->提交->状态。该操作应该返回一个用正确数据解析的承诺，您可以在 fetch 函数中使用它。

此后，`commit`调用可能会接近`fetch`的末尾，页面会有正确的数据，但一般问题可能仍然存在。

## Heroku 和 is-https

我使用 Cloudflare for DNS 和 Heroku 作为服务器托管应用程序。将域名指向 Heroku 是通过 CNAME 完成的，这给我带来了一些问题。

Nuxt (sitemap，i18n)的几个模块使用 is-https 库在服务器端识别请求的类型。对 Cloudflare 的请求是 HTTPS，但代理可能不是。我在这方面得到了 CMTY 的一些建议。

启用`x-forwarded-proto`应该有帮助，不过我还没试过。

## 到处都是这个

我个人喜欢用 JS 编写函数代码。用 Nuxt 是可以的，但是所有的模块都让你用`this`。

想在 Vuex 商店或组件中找到当前的语言环境吗？`this.app.i18n.locale`切换区域设置和获取所有区域设置列表也是如此。

想在 Vuex 中换页？`this.router.push`

我可以接受这一点，但是将这些对象作为函数内部的参数也可以有利于更好的代码分离。

## Rx.js

我喜欢 RX，尤其喜欢将它应用到状态管理用例中。RX 可以集成到 Vue 中——如果我们谈论 DOM 事件的话，也可以集成到 Nuxt 中。有[这个包](https://github.com/vuejs/vue-rx)。

要将其集成到 Nuxt 应用中，只需创建一个插件，如下所示:

将这个文件放入 plugins 文件夹，并将插件添加到 nuxt.config.js 中

也有几次尝试将其集成到 Vuex 中，但到目前为止，回购已被否决。最近没看到任何关于这个的文章。

# 摘要

总而言之，我爱 Nuxt。我甚至为我的同事们准备了一个研讨会，并做了几次，以传播知识并鼓励他们尝试。

我认为这是一个非常成熟和发达的工具，可以满足任何需求。从简单的静态登录页面和个人网站到复杂的网络应用和电子商务平台，你都可以使用它。

我遇到了一些问题，这些问题是可以解决的，但我也经历了许多美好的时刻，一切都变得如此简单，而且运行得棒极了。我真的相信这个框架，并且深深地感谢那些创建了它并且还在维护它的人们。
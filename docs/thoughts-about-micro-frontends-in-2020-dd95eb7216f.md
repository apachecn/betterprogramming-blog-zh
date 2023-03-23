# 关于 2020 年微前端的思考

> 原文：<https://betterprogramming.pub/thoughts-about-micro-frontends-in-2020-dd95eb7216f>

## 为什么打破你单一的前端代码库会有好处

![](img/b8b138f13b68d4c666643c065c41c5a4.png)

苏海尔·萨瓦在 [Unsplash](https://unsplash.com/s/photos/micro?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在过去的几年里，我看到“微前端”这个术语变得比以前更常见了。

在后端开发人员的世界里，将一切都拆分成微服务已经是比较常见的了。得益于 Docker 的技术，将后端扩展到多个服务和这些服务的实例比以往任何时候都更容易。

但是在前端开发人员的世界里，还没有那么普遍。

在这篇文章中，我想分享我的想法，为什么我认为从单片前端应用程序到微前端方法可能是好的。

# 为什么？

对于那些多年来一直在单一架构中构建网站或 web 应用程序的前端开发人员来说，微前端的方法让人感觉有点过时。(嗯，那是我发现的第一个意见。)

## 单片前端的问题

当您想到我们在单芯片前端应用方面面临的挑战时:

*   更难与多人/团队合作。
*   构建时间长。
*   在你不知情的情况下覆盖样式。
*   当 API 发生重大变化时，需要部署整个应用程序。

当你第一次开始建立一个单一的网站，这不是一个大问题。但是当组织成长时，工作人员的数量将成为一个挑战。

## 微前端解决的问题

切换到微前端方法可以解决其中一些问题。

*   易于在小部件中部署。
*   构建时间更短。
*   更容易与多人/团队合作。
*   突破 API 变化将只需要一个小的部署。

但是对于开发团队来说，这需要不同的心态。除此之外，它需要一些额外的工作来改变前端架构。

# 怎么

幸运的是，我们并不是第一批在单芯片前端架构中遇到这些挑战的开发者。

## 大公司引领潮流

像 Spotify、Klarna、Zalando、Upwork、Allegro、HelloFresh、Airbnb 和脸书这样的大公司也经历了这些挑战。

所以，他们在这方面做了很多开创性的工作，找到了一些很酷的方法来解决问题。

*   扎兰多建造了[马赛克。看看他们的谈论:](https://www.mosaic9.org/)[Zalando Tech 的马赛克微服务](https://www.microservices.com/talks/mosaic-microservices-zalando-tech/)。
*   Klarna 在 [HackerNews 上解释了他们是如何接近它的](https://news.ycombinator.com/item?id=13012916)。
*   Upwork 写了一篇博文:[用微前端实现 Upwork 的现代化](https://www.upwork.com/blog/2017/05/modernizing-upwork-micro-frontends/)。
*   Allegro 写过一篇博文:[管理微服务架构中的前端](https://allegro.tech/2016/03/Managing-Frontend-in-the-microservices-architecture.html)。
*   HelloFresh 写过一篇博文:[hello fresh 的前端微服务](https://engineering.hellofresh.com/front-end-microservices-at-hellofresh-23978a611b87)。
*   Airbnb 创建了一个名为 [HyperNova](https://github.com/airbnb/hypernova) 的工具来服务服务器端的 JavaScript 视图。

# 技术

如果你检查了所有大公司的帖子，你会看到一些关于如何从技术上接近微前端的技巧。

*   元框架: [Single-SPA](https://single-spa.js.org) ，这个框架让你在运行时组合多个 JavaScript 框架/库，而无需刷新页面。
*   不同 URL 上的多个 SPA:这是拥有多个微前端的最简单方式。
*   IFrames。
*   Web 组件:使用 JavaScript 包装器将 Angular 和 React 组件转换成 web 组件，并把它们放在一起。 [**克里斯麦显杰**](https://medium.com/u/222c05246f8e?source=post_page-----dd95eb7216f--------------------------------) 创建了一个关于它的很酷很实用的博客: [*使用 Web 组件创建微前端*(支持 Angular 和 React)](https://medium.com/javascript-in-plain-english/create-micro-frontends-using-web-components-with-support-for-angular-and-react-2d6db18f557a)

# 当...的时候

但是问题是:“什么时候转向微前端是个好主意？”

嗯，我觉得比较简单。当你建立一个小网站时，坚持使用整体的方法。当您构建一个大型应用程序，与大量人员/团队一起工作，并使用“微服务”作为后端架构时，您肯定会从微前端方法中受益。

# 谢谢

谢谢你一直读到这里。我希望它能为您研究具有微服务架构的微前端提供一些资料。
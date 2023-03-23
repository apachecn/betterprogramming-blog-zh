# 2022 年 Cloudflare 开发者周—发布的所有内容

> 原文：<https://betterprogramming.pub/cloudflare-developer-week-2022-everything-that-was-announced-7e0c91d2f636>

## 从新产品和新特性到技术深度，我们将在本文中一一介绍

![](img/3627a05435b92754ed626e83063c7490.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)拍摄

我总是喜欢科技公司用一周时间发布新品，毕竟，谁不喜欢听到新品上市的消息呢？亚马逊通常会在年度 re:Invent 大会上宣布很多有趣的事情，而 Cloudflare 刚刚举办了开发者周。

顾名思义，这是专门讨论 Cloudflare 开发的所有事情的一周。今年，他们发布了全新的产品，现有产品的新功能，对一些产品的使用进行了深入的技术探讨，甚至详细介绍了一些客户和合作伙伴的故事。

有 30 多个公告，所以我挑选了最令人兴奋的。渴望了解更多信息？让我们开始吧！

# 新产品和新功能

任何一组公告的亮点通常是美国工程师可以尝试的新技术，Cloudflare 的开发者周也不例外。他们的产品已经很稳定了，但我相信本周的发布会真正将 Cloudflare 的平台推向了一个新的高度。

## 引入队列

当我第一次开始使用 Cloudflare Workers 时，我认为它非常简单，并且支持我的大多数用例。然而，我总是觉得它缺乏优雅地处理异步工作流的能力。多亏了排队，现在不再是这样了。

简而言之，Workers 是 Cloudflare 的无服务器产品，您可以在其中部署定制的应用程序来处理 HTTP 请求(或者按计划运行它们，如果您愿意)。使用队列，您可以设置完整的异步工作器，来处理放在队列中的消息。如果你曾经使用过 Kafka 或 RabbitMQ，它与这些产品非常相似，尽管它在你作为一名工程师的交互方面进行了大量的简化。

点击阅读完整公告[。Queues 目前处于私人测试阶段，但我预计在不久的将来它会被更广泛地发布。](https://blog.cloudflare.com/cloudflare-queues-open-beta)

## 用 D1 存储关系数据

与异步工作流类似，我所缺少的另一大难题是更典型的模式持久存储。耐用品是他们拥有的最接近的东西，但最终它仍然只是一个键值存储。

进入 D1，现在在开放阿尔法，提供 SQL 数据库给你的工人。它的工作方式与您习惯的方式相同:使用 SQL 创建一个模式，在 D1 运行该 SQL，然后您就能够以关系格式存储来自您的员工的任何数据。

点击阅读完整公告[。](https://blog.cloudflare.com/d1-open-alpha/)

## 工人使用模板入门

当开始使用一个新平台时，看到所有的产品和工具而不知道从哪里开始会让人不知所措。幸运的是，Cloudflare 创建了一个充满模板的网站来帮助您入门，其中包含大量使用案例的模板，使用了各种工具。

想要创建您的第一个工人吗？点击阅读[模板。](https://blog.cloudflare.com/cloudflare-workers-templates/)

## 和 R2 超级思乐冰一起从 S3 迁移过来

R2·超级思乐普可能是我最近看到的一个功能的最佳名称之一，他的目标是让从 S3 上的简单存储到 Cloudflare 的迁移变得毫无痛苦。

对于任何不熟悉的人来说，S3 是一个 AWS 产品，它在云中提供简单的基于文件的存储。Cloudflare 提供了一款名为 R2 的兼容产品，但是如果您想要进行迁移，那么之前这样做是很痛苦的。

点击阅读完整公告[。](https://blog.cloudflare.com/cloudflare-r2-super-slurper/)

## 创建具有页面功能的动态网站

当 Pages 最初启动时，它只允许您创建静态网站，没有 Cloudflare 提供的后端功能。这要么意味着你被限制在静态网站上，要么你不得不在其他地方托管你的后台，这使得事情变得有点复杂。

不过，Pages 函数解决了这个问题，使您能够轻松地将 Workers 作为 Pages 部署的一部分进行部署。它们可以是简单的函数，也可以是成熟的 API，一切尽在掌握。

点击阅读完整公告[。](https://blog.cloudflare.com/pages-function-goes-ga/)

## 将日志接收到您选择的观察平台中

我想在 2022 年开发者周上强调的最后一个特性是可观察性方面，而不是一个花哨的新工具。作为工程师，日志对于理解我们在云中运行时发生了什么至关重要。

Cloudflare 已经提供了简单的日志记录功能，甚至能够从您的工作人员那里实时获取尾部日志。不过这很简单，而且有专门研究可观测性的平台，比如 Datadog 和 New Relic。

很可能你的公司已经有了自己选择的平台，现在在这个平台上查看你的日志很容易。Workers Trace Events Logpush 可以配置为以很小的成本将您的日志推送到这些平台。

点击阅读完整公告[。](https://blog.cloudflare.com/workers-logpush-ga/)

# 技术性深潜

这完成了 Cloudflare 提供的产品的主要发布，或者至少是我认为最大的产品。

然而，这并不是他们在开发者周中讨论的全部内容。事实上，这只是故事的一半。除了产品发布之外，他们还对一些技术方面进行了更深入的研究。我在下面挑选了一些最好的！

## 使工人符合标准

我知道，标准不是最令人兴奋的话题。然而，它们对于获得围绕特定技术的牵引力，并最终推动该技术向前发展是至关重要的。

Cloudflare 是 W3C 社区组织的一部分，该组织旨在围绕其工作人员使用的 JavaScript 运行时创建标准。如果标准达成一致，从工人到另一个平台的迁移将变得相对容易，这对每个人都是一个胜利。

在这里阅读完整的深度潜水。

## Cloudflare 的 API 现在使用 OpenAPI

这是我最喜欢的，主要是因为我已经使用 OpenAPI 很多年了。当我开始阅读使用 OpenAPI 的 API 文档时，会产生一种温暖的模糊感。它易于使用，具有强大的社区工具。例如，您可以使用 OpenAPI 模式在本地快速轻松地运行模拟服务器。

Cloudflare 显然同意这一点，因为它的 API 现在可以与 OpenAPI 模式一起使用！

点击这里阅读完整的深度潜水[。](https://blog.cloudflare.com/open-api-transition/)

## 使用 Pages 函数的服务器端呈现

在上面的公告中，我们介绍了 Pages 功能，它允许您向部署到 Cloudflare 的站点添加动态功能。

这一技术深度探讨了如何利用新功能在服务器端呈现页面，并提供了完整的代码示例！

点击阅读完整的深度潜水[。](https://blog.cloudflare.com/pages-full-stack-frameworks/)

## 随着工人逐渐迁移您的前端

由于 Cloudflare 的产品仍然相对较新，他们热衷于就如何在 Cloudflare 的生态系统中迁移您的部分架构提供指导。

这正是你使用片段在前端所能做的，允许你以自己的速度慢慢地迁移前端的一部分。

点击阅读完整的深度挖掘[。](https://blog.cloudflare.com/fragment-piercing/)

## Cloudflare 的速度和本地开发改进

我把最后两个放在一起，看看任何平台的两个关键方面:性能和开发的容易程度。

对于性能，Cloudflare 深入研究了他们的网络，以及与其他提供商相比的表现。正如你所料，它的进展相当不错。我总是对这些平台生成的基准持保留态度，但即使如此，结果看起来确实令人印象深刻。

最后，我们有地方发展。使用 Cloudflare Workers 在本地开发已经有一段时间了，但是他们[更进一步](https://blog.cloudflare.com/miniflare-and-workerd/)，也能够在本地使用他们的数据服务！

# 摘要

这是我在 cloud flare 2022 开发者周中发现的最有趣和最有用的公告和主题。

我想给一篇关于【Cloudflare 的工作人员如何扩展得如此之好，以至于破坏了他们的基础设施的非常有趣的文章加分！

您尝试过在 Cloudflare 上创建任何东西吗？如果有，你有什么经历？如果不是，你对他们的产品感兴趣吗？

如果你还没有尝试过 Cloudflare out，他们的[免费层](https://www.cloudflare.com/en-gb/plans/free/)非常慷慨，可以让你使用他们的大部分产品！

> 我的书《用现代图表技术创建软件》已经出版了！
> 
> [单击此处](https://pragprog.com/titles/apdiag/creating-software-with-modern-diagramming-techniques/)学习如何创建图表来比文字更直接、更清晰地传达信息。仅使用基于文本的标记，由 Mermaid 提供支持，创建有意义和有吸引力的图表来记录您的领域，可视化用户流，揭示任何所需级别的系统架构，等等！
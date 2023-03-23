# 谁杀了 IBM Kitura？

> 原文：<https://betterprogramming.pub/who-killed-kitura-e5aa1096a4c1>

## 服务器端 Swift 的未来会如何？

![](img/8c5f185a51b2bf0313c229f70748e8c2.png)

卡森·马斯特森在 [Unsplash](https://unsplash.com/s/photos/ibm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

那是 2016 年，我在田纳西州美丽的纳什维尔参加了由 [Greg Heo](https://medium.com/u/4c3430b2cdcc?source=post_page-----e5aa1096a4c1--------------------------------) 主持的 IndieDevStock 会议。Greg 谈到了使用 IBM Kitura 框架的服务器端 Swift。作为前任。NET 开发人员，从事服务器端技术工作超过十年，看到 Swift 在服务器上运行，我欣喜若狂。

快进到 2019 年， [IBM 刚刚宣布](https://forums.swift.org/t/december-12th-2019/31735)它将不再致力于 Kitura 框架。

尽管 Kitura 是开源的，开发者甚至可以在 IBM 拔掉插头后继续工作，但现实是当你砍掉树根时，树最终会死。

Kitura 松散地基于流行的节点框架 Express。易用性和架构的简单性使 Kitura 成为其他服务器端 Swift 框架中的一个很好的选择。Kitura 团队甚至更进一步，开发了一个桌面 Mac 客户端来快速配置和创建基于 Kitura 的应用程序。

![](img/16ae59029967de916b579fec1619c8f3.png)

Mac 版 Kitura 桌面客户端

在为 Kitura 和 Vapor 开发了 Udemy 课程后，我一直认为 Kitura 比 Vapor 更干净，更易于使用。不幸的是，这不足以让产品存活下去。

Kitura 或任何服务器端 Swift 框架都未能获得开发社区的支持。除了一些小的业余爱好项目，服务器上的 Swift 没有被大规模使用。

## 为什么？

对我个人来说，最大的问题之一是 Kitura 更新破坏了旧代码。这包括对 PostgreSQL 库等的依赖。我仍然记得当我在加利福尼亚州的 Carpinteria 时，为 LinkedIn 录制了一门全新的课程，一次更新彻底摧毁了我的依赖，让我陷入了数据访问的工作中。这很混乱，但并不新鲜。大多数新框架都在不断发展，随着它们向更稳定的架构发展，它们打破了许多依赖关系。从 Swift 1.0 到 Swift 2，我们已经看到了这种情况。x 甚至蒸汽 2 到蒸汽 3。

我认为更严重的问题是开发者、架构师和管理者不愿意在一个新的平台上下注。尽管 Kitura 是由 IBM 开发的，但它试图取代服务器，而服务器是应用程序不可或缺的一部分。服务器是应用程序的心脏。如果不符合预期，产品就会死亡，除非提供紧急心脏移植。

*正如* [*马可阿蒙*](https://medium.com/u/b263e50a8bd7?source=post_page-----e5aa1096a4c1--------------------------------) *曾经说过，你永远不要成为任何事情的最大客户。*

部署是 Kitura 的另一个大问题。没有点击部署选项。整个过程既乏味又痛苦。我写了一篇关于部署 Kitura 应用程序的文章，您可以在下面查看:

[](https://medium.com/@azamsharp/deploying-kitura-swift-app-to-ibm-bluemix-c884fb3de164) [## 将 Kitura Swift 应用程序部署到 IBM Bluemix

### 当您在寻找将 Kitura Swift 应用程序部署到 Bluemix 的选项时，您可能会遇到 IBM 云应用程序工具…

medium.com](https://medium.com/@azamsharp/deploying-kitura-swift-app-to-ibm-bluemix-c884fb3de164) 

# 那么服务器端 Swift 死了吗？

不要！Vapor 围绕服务器端 Swift 仍然有一个非常活跃和充满活力的社区，他们为现有框架提供了许多工具和更新。还有以服务器端 Swift 为主题的会议。其中一个会议是 [serversideswift.info](https://www.serversideswift.info/) ，它引起了 swift 社区的极大兴趣。

只有时间能告诉我们服务器端 Swift 的未来会是怎样，以及它会在多大程度上被社区所接受。
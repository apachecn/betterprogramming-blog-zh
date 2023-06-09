# 去无服务器？治理就是一切

> 原文：<https://betterprogramming.pub/going-serverless-governance-is-everything-c70589c9cee9>

## 当你决定不使用服务器时，你需要竖起一些护栏来保护你自己。如果你不这样做，你可能会发现自己陷入了一个无法摆脱的困境

![](img/49bb05ec941a503993ad35c3b66948a9.png)

[廷杰伤害律师事务所](https://unsplash.com/@tingeyinjurylawfirm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/law?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

几周前，一篇名为[“五年后，我不再热衷于无服务器计算”](https://dev.to/brentmitchell/after-5-years-im-out-of-the-serverless-compute-cult-3f6d)的文章开始流传。这有点…退一步说，两极分化。

如果你关注我的时事通讯，你就会知道我是无服务器“邪教”的活跃成员。

这篇文章列举了一系列关于无服务器如何以各种方式导致不良行为的论点。

当我读它的时候，他的每一个观点中都出现了一个中心主题，这个主题真的可以解决所有的问题。

**治理。**

治理是开发团队在构建他们的应用程序时应该遵循的一套规则和指导方针。如果我们遵循架构师和开发负责人制定的指导方针，我们可以减轻一些与采用新技术相关的风险。

在无服务器空间中的任何人都会告诉你，有时感觉就像是西部荒野。但这并不是无服务器独有的。只是新的。它需要时间来摆脱最佳做法。我们需要*弄清楚它能做什么*。

为了最好地描述架构治理，我们将[使用一个类比](https://allenheltondev.medium.com/the-mighty-metaphor-your-new-secret-weapon-in-tech-a483957d72f0)来将其与汽车联系起来。想象一下 T 型车刚发明的时候。1908 年，它是第一辆大规模生产的汽车，使成千上万的司机能够跳上驾驶座。

在早期，有许多问题有待发现和解决。直到 1910 年，第一部酒后驾车法才出现。第一个交通灯直到 1930 年才被发明出来。见鬼，第一条安全带直到 1950 年才开发出来！

发现某物如何被使用需要时间。汽车是这样，无服务器也是这样。

# 治理级别

与大多数事情一样，治理来自不同的层次。在最高级别，我们有*广泛的治理*。比如“总是实践最小特权原则”这一层面的治理由高层领导决定。

但是随着你越来越接近开发团队，你会得到更多的细节和细微差别。

下一层是*一致性治理*。标准化事情是如何做的。这比它上面的级别包含更多的细节。这个治理层可能会建议类似“使用由 OAuth2.0 保护的 REST APIs”这样的事情。这个级别通常在部门或开发组级别确定。

最后一级是*细节治理*。这是决定如何构建应用程序的细粒度细节。您可能会看到治理规则，如“绝不在 IAM 权限中使用星号”。这个治理级别是由单独的开发团队决定的。

> 治理来自内部。

治理的三个层次应该来自你的公司内部。*别指望人家给你补偿*。这些是与您的公司和您的个人开发团队相关的具体细节。

驾驶法也是一样。联邦政府层面有宽泛的治理法律。这些都是很高层次，通常定义了安全标准和规则。

然后你就有了州级的一致性治理。像速度限制和保险最低限额之类的东西。

最后，在县/地方一级有详细的治理。这些包括罚款和费用的细节，如果你得到一张交通罚单的话。

无论你去哪里，交通法规都不一样。有的地方甚至开在车的对面！

这里的要点是没有“一刀切”的治理或无服务器方法。你必须找到适合你和你的团队的方法。

# 治理的类型

当我第一次参与治理时，感觉它只是一堆“不，不，不”。这似乎是规则，以保持开发团队在他们的车道上，并防止他们做任何疯狂的事情。

> *治理可以帮助你*

虽然可能感觉治理是限制性的，但它的唯一目的是帮助您。它提供了使代码变得熟悉和一致的机制，所以如果你转换团队，你可以很快学会并专注于获得领域知识，而不是学习所有新的模式和工作流。

治理有许多不同的类型，但是您会发现(并且在开始使用无服务器时需要)一些最常见的类型是:

*   **最佳实践** —一组模式或风格指南，为所有正在编写的代码提供*一致性*。这提供了统一的体验和一致的结果。这些被认为是允许例外用例的官方建议。*示例—所有返回列表的 GET 端点都应该返回一个 200 状态代码和一个 items 数组，结果为*
*   **标准** —应该如何进行操作的明确指导方针。与最佳实践略有不同，因为它们不仅仅是建议。这些是解决问题的首选方法。*示例—为了易读性和可发现性，所有端点必须通过自定义域进行路由*
*   **指令**——构建软件时必须遵循的规则。在变通规则方面没有灵活性。*示例—认证必须使用 XYZ 提供者来提供 SSO*

以上每一项对他们都有自己的份量。这意味着它们的范围从一般的指导方针到必须做的努力。提供一系列治理规则可以确保使用它们的人不会感到被限制所束缚。

# 无服务器前瞻

您可能会问自己“这与无服务器有什么关系？”

好问题。**不是**。

与其他新技术相比，无服务器没有任何独特的问题(除了少数例外)。但是采用无服务器的人们担心缺乏来自社区的最佳实践和标准。由于在无服务器领域还处于早期，所以没有明确的标准。

当人们期望这项新技术是已经存在了几十年的东西的基础时，他们的日子就不好过了。*定义自己的标准。回馈社会*。

目前，在无服务器领域有很多工作要做。每周都有成百上千的博客文章、视频和白皮书发布，展示社区的工作。

接受人们正在做的事情并采纳它。分享你的收养，帮助促进这些标准。没有官方的无服务器授权。像你我这样的人正在为我们所看到的最佳实践做出贡献。

所以不要屏住呼吸等着别人为你定义你的治理规则。那需要一段时间。但是一定要花时间自己建立治理规则。查看社区正在做什么，并根据需要进行修改。做最适合你和你的同伴的事情。

# 结论

从您的无服务器之旅开始，您应该实现许多类型的治理。留出一些时间[来积累专业知识](/moving-toward-modern-how-to-get-to-the-cloud-as-a-large-software-company-b95e99710d33)并利用这些经验来驱动你的模型。

制定指导方针来构建一致的软件。注重提供从命令到一般建议的一系列规则。不遗余力——每个细节都有帮助。从安全性到存储库和微服务结构，再到服务使用指南，清单一直在增加，每一个细节都很重要。

当采用无服务器(或任何新的技术堆栈)时，构建、建立和维护治理将是您成功的关键。请记住，治理是为了帮助，而不是阻碍。如果您的团队得到的印象是治理是太多的阻碍，那么可能是时候重新评估了。

编码快乐！
# 走向现代化:大型软件公司如何实现云计算

> 原文：<https://betterprogramming.pub/moving-toward-modern-how-to-get-to-the-cloud-as-a-large-software-company-b95e99710d33>

## 说到敏捷性，大型软件公司处于劣势。很难转动一台巨大的移动机器。从小处着手

![](img/d23069029e2f225777903def16c6ef29.png)

图片来源:作者

你玩过时髦的宾果游戏吗？

这是一个有趣而简单的游戏，包括一张宾果卡，上面有一堆科技术语。如果你在会议中听到其中一个流行词，把它划掉。第一个得到宾果游戏的人获胜！

如果你足够幸运地拿到了*云*流行词宾果卡，你很可能会在大型软件公司几乎任何会议的前五分钟内获胜。像“到云”、“供应商锁定”和“提升和转移”这样的流行语似乎在会议室的墙上无限回响。

有充分的理由。

在 2020 年的这个时候，将你的软件转移到云端的原因应该是显而易见的。当您开始与云供应商进行托管时，增强的安全性、更好的可扩展性、降低的成本和快速的周转都是立竿见影的好处。

但我不需要告诉你这些。你已经听过 500 遍了。大概今天吧。

然而，我们将要谈论的是作为一家中大型软件公司，你可以用来打入云计算的具体策略。您可能有一个势头强劲的遗留应用程序，如果仅仅“放入云中”会有风险所以我们要谈谈如何下这盘棋。

首先，我们来谈谈你的选择。

![](img/08c3a589a3091ad56452027d6c9a4c9b.png)

*照片由*[巴特贝利](https://unsplash.com/@7bbbailey?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) *上* [*下*](https://unsplash.com/s/photos/one?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 云迁移的类型

在我们讨论如何迁移到云中之前，我想先谈谈云迁移的类型。您和您的公司会考虑不同程度的云计算。三种主要的迁移类型是:

*   提升和移动
*   云构建
*   热调换

虽然迁移到云可能有更多的变化，但它们都可以以某种方式归结为这三种。所以让我们了解一下他们，好吗？

## 提升和移动

也被称为*再托管*，云迁移的[提升和转移方法](https://aws.amazon.com/products/storage/lift-and-shift/)通常是最常见的。这种迁移策略包括将您的应用服务器从本地或托管在数据中心，并按原样转移到云供应商。服务器是虚拟的，托管方式与本地完全相同。

虽然从技术上来说这是一个云迁移策略，但我倾向于认为它不会让你快速迈向现代化。然而，它将做的是让你的软件由云供应商托管，并可能降低你和你的客户的成本。

提升和移位可以被认为是简单地移动箱子。我从这个位置把它捡起来，放在那个位置。除了盒子放在哪里，什么都没变。

作为一家大型软件公司，这种方法通常是**最不具破坏性的**，也是**风险最小的**。

## 云构建

另一方面，我们有*云构建*。这就是“从零开始，除了学到的经验教训”的方法。

这种方法包括从零开始，使用云服务来构建您的应用程序。它可能涉及[容器](https://www.docker.com/)、 [lambda 函数](https://aws.amazon.com/lambda/)、 [Kubernetes](https://kubernetes.io/) 、 [API 网关](https://aws.amazon.com/api-gateway/)、 [MongoDB](https://www.mongodb.com/) 、 [DynamoDB](https://aws.amazon.com/dynamodb/) ，或者任何介于两者之间的东西。

这种方法的优点是，当你完成后，你就有了一个云原生应用程序——一个可以优雅地伸缩的应用程序，充分利用了在云中的优势，实际上是一个现代应用程序。它由云供应商托管，利用托管云服务，并且是安全的。

作为一家大型软件公司，您当然必须维护您的遗留软件，直到弃用日期，但是您已经对现代化和实现云的优势做出了重大承诺。

这种方法可以被认为是撕掉创可贴。快点做完就行了。

正如你可能想象的那样，从零开始构建云是最具破坏性的 T21，并且有很大的风险。

## 热调换

假设您现在已经将应用程序投入生产。这是一个很大的应用程序，有 20 多年的开发经验，由几十名开发人员维护。

作为一家公司，您承诺在某个日期前实现现代化，但您知道您无法进行新的云构建，并且觉得提升和转移还不够。

你是做什么的？

将您的应用程序分解成小块，并开始将它们迁移到云中。

你的应用程序允许上传和维护文件吗？创建一个只处理文档管理的新的云原生构建。一旦构建好并放入云中，更新您的应用程序以使用新的服务，而不是遗留的文档管理代码。进行一次*热插拔*。

这种方法就是乐高法。你在云中构建了一堆服务，这些服务可能与你目前拥有的功能对等，也可能不对等，然后开始转移传统应用程序来使用它们。

通过热插拔，您可以按照自己的节奏移动，随着时间的推移，使用云原生功能将整个应用迁移到云中。双赢！

如果操作不当，热交换方法可能会带来破坏性和风险。但是，我们在这里讨论您可以做些什么来减轻中断和风险。

![](img/66adec643bafe08af0fbda374835bca6.png)

*照片由* [*艾迪·莱克曼*](https://unsplash.com/@eddyray?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) *上* [*下*](https://unsplash.com/s/photos/number-one?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 从一个开始

您的组织中可能有许多小型开发团队。每个团队负责大型应用程序的一个特定领域。这太棒了。

如果你想向现代化迈进一大步，就创建一个“豚鼠团队”来搞清楚这一切。探索未知云空间的团队。

创建一个小型工程师团队，让他们在一段时间内做 R&D。在设定的时间后，让他们使用他们的新技能和知识来构建第一个*热插拔*服务。

当这个团队开始研究、开发和向云发展时，您的开发团队的其余成员应该照常工作。继续朝着你的承诺努力，增强主应用，不要偏离。

像往常一样继续构建您的应用程序，但是请记住，新的东西即将到来。

![](img/baf4e16fdbcd13fde706a27786df64ce.png)

*照片由*[*S O C I A L . C U T*](https://unsplash.com/@socialcut?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*[*Unsplash*](https://unsplash.com/s/photos/startup?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*

# *内部启动*

*把这个团队当成一个创业公司。不要给他们设限。让他们按照自己的进度学习。*

*截止日期是可以的，但是不要在他们正在做的事情上添加你当前的遗留过程。这个团队是为了学习如何实现现代化而组建的。除了期望他们学习的领域知识之外，他们还将学习:*

*   *[持续改进和持续交付(CI/CD)](https://medium.com/better-programming/are-you-really-ready-for-ci-cd-9a8bf6d01b8a)*
*   *[托管云服务如何协同工作](https://aws.amazon.com/managed-services/)*
*   *[云开发的最佳编程语言是什么](https://medium.com/better-programming/how-to-choose-the-best-programming-language-for-your-new-project-d72b2e586aac)*
*   *[从整体服务转向微服务](https://medium.com/better-programming/micro-frontends-role-based-applications-and-you-5d6cd8d796eb)*
*   *[需要哪些新的开发实践](https://medium.com/better-programming/think-unique-build-the-right-software-not-the-easy-software-ad6615ef58c9)*

*一个创业公司不仅仅专注于开发。这是生意。它必须处理[监控和支持](https://medium.com/better-programming/the-challenges-of-stateless-architecture-and-how-to-monitor-your-serverless-application-94c0e8b8dd1)、[成本预测](https://medium.com/better-programming/5-steps-to-making-a-predictable-cost-model-for-aws-serverless-projects-78d78909bb82)、[走向市场策略](https://medium.com/better-programming/the-greenfield-paradox-why-is-building-a-new-app-so-easy-and-also-so-hard-b3cc58552ab)、[技术写作和发布说明](https://medium.com/better-programming/build-a-continuous-changelog-with-aws-and-jira-cbf0f10776bb)等等。*

*这是您在有限的范围内完善云战略的机会。让整个组织的人与这个创业团队一起工作，确定未来事情将如何运作。*

*不要强调要把事情做好。随着你了解更多，事情肯定会改变。但是，尽早制定一个初始计划将允许您随着时间的推移迭代和改进策略。当时机成熟的时候，建立在你的想法之上将会导致一个更强大、更成熟的过程。*

*如果你有可用的资源，*内部启动*方法将会产生非凡的结果。当你开始扩展并让更多的开发团队采用它们时，拥有一个致力于为公司证明最佳实践和云规范的团队将导致明显更少的中断。*

*![](img/21eab94679ef5146855a3c39a01351af.png)*

**照片由*[](https://unsplash.com/@chuttersnap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)**上* [*下*](https://unsplash.com/s/photos/grow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)**

# **采用和扩展**

**内部启动成功。他们在云中构建了一个可热插拔的软件。**

**他们进行了研究，建立了云开发最佳实践，提供了关于部署策略的参考资料，用最适合公司需求的编程语言给出了示例代码，并与组织合作开展了与云相关的业务工作。**

**现在是时候看看它是否可重复。**

**让组织中的另一个团队使用创业团队构建的东西。他们能用定义的过程得到相似的结果吗？记住，从小处着手。证明曾经做过的事情可以在不同的人群中再次发生。让创业团队成为你的专家。**

**第二个团队应该比创业团队行动更快。是的，将会涉及技能提升，但是发现和研究的重要部分已经完成。另外，你现在有专家了。创业团队靠他们自己，但是现在他们可以为你的其他开发团队提供专家建议。**

**一旦第二个团队证明它是可重复的，继续第三个。然后是第四个。开始一次移动多个团队。无论您的企业允许什么，同时仍然能够支持旧软件通过弃用日。**

**不知不觉中，您已经用云原生解决方案取代了庞大的遗留应用程序。你在云端。你很现代。都是因为你从小做起。**

# **现在怎么办？**

**如果你还没有，现在是时候让你的现代化计划就位了。随着当今技术行业的快速发展，尽快实现云计算是保持相关性的关键。**

**开始新生活的最佳时间是昨天。第二好的时间是今天。**

**弄清楚你进入云计算的主要动机是什么。检查几个盒子并节省一些额外的现金是严格的吗？试着举一举，移动一下。**

**你口袋里有钱烧个洞还爱豁出去？尝试云构建。**

**您是否想充分利用云，但又想降低重写的风险？尝试热插拔。**

**做颠覆者真好。但不是内在的。把你的中断留给你的目标市场，而不是你的开发团队。从小处着手。创建一个开发团队来做脏活，并为公司的其他部门想出一个策略。**

**最好的情况是，您有一个清晰、可靠的策略来迁移到云。最坏的情况，你没有前进，只是损失了一个小团队的时间。**

**在大型遗留应用程序上改变方向可能很困难。但如果你采取“从小处着手”的方法，并随着时间的推移逐步积累，你将会取得成功，并成为云游戏中的一员。**
# 大规模代码分析

> 原文：<https://betterprogramming.pub/code-analysis-at-scale-abc4fc8a630c>

## 探索高级工程师建立清单和分析代码的方法，而不是不经分析就删除它们

![](img/33c98b95062cf2246d26631cee6c3197.png)

[regularguy.eth](https://unsplash.com/@moneyphotos?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

你有没有想过如何在构建 vs 购买的环境之外评估软件？听起来是个疯狂的问题，对吧？事实是，为了大规模重构，你必须做的不仅仅是重构所有的东西。重写、重构和重新架构必须是机会主义的，并向业务交付真正的价值。

所以，如果重构一切都很困难，你如何大规模地开发软件呢？

使事态合理化是很重要的，因为合理化会导致正确的优先顺序。当与良好的执行相结合时，它可以成为实现变革和提高质量的有力工具。首先，这个问题可能听起来有点难以回答，但是当你深入研究时，你会意识到什么有意义，什么没有意义。之后，一些形状出现了。所以，在我深入探讨这个问题之前，让我们从一个简单的问题开始——为什么要评估软件？

# 为什么要评估软件

除了构建与购买的背景之外，还有很多理由来评估软件，例如:

*   理解。事情是如何运作的？什么不起作用？怎么会更好呢？
*   优先排序。我们应该先改善服务 A 还是服务 B？
*   决定中。我们应该让服务 A 退役还是修复它？什么可以？什么不是？
*   策略。我们应该这样做吗？我们应该委托给一家公司吗？我们应该开始一个新的代码库吗？我们应该合并/拆分代码库吗？

评估很重要，因为它使表格变得清晰，并为您提供多方面的有用数据，例如:

*   向你的团队宣传做出改变
*   向公司的其他人宣传更大的变革，通常是以项目或倡议的形式
*   避免在需要维护的软件上花费精力(这适用于软件是否将被退役、替换或替换为另一个工具)。例如，在新“功能”的 pr 上花费太多时间
*   通过加速一些关键计划来避免更多的问题，例如，拆除一个整体

# 如何评估软件

IMHO，你需要回答下面这个问题:我们能和这个软件一起生活吗？这并不意味着软件没有债务和缺陷或者完美，但这意味着它是可管理的和合理的。现在，公司与公司之间有什么合理的变化？

人们非常善于适应问题并解决它们。这是好事，不要误会，但是有一点这个机动太贵了，没几个人能做到。这是一个很好的迹象，我们可能需要这个软件的帮助。

在规模上，总会有问题。总有软件需要重写，总有技术欠账。成功的公司有很多技术债务——这没什么。这并不意味着我们需要停止战斗。相反，我们需要继续对抗复杂性并控制它。一个好的开始方式是评估你手中的东西。评估很重要，因为它是对抗复杂性、人才密度和正确的工程文化的主要输出。

评估软件，我们需要看代码；光靠和人说话是没办法评价的。面试很有趣，也很有用，但仅限于当前的现状和文化。即使您正在阅读代码，您应该寻找什么？让我们考虑三个例子。

## 示例 1 —库迁移

假设您计划推出一个全公司使用的内部公共共享库，这个库非常重要。因此，您对公共 API 做了一些更改，打破了向后兼容性。现在，记住这个场景，您可以在您的消费者的代码库中寻找以下元素:

*   我们在破坏多少消费者的利益？
*   有哪些用例？他们如何使用您的解决方案？
*   我们看到任何反模式或代码滥用了吗？
*   在重大变更的情况下，我们能否提供一个方法(步骤)来执行迁移或为新版本开发代码？

已建立的框架通常有变更日志和迁移指南。出于某种奇怪的原因，内部共享库通常没有这种功能。阅读您的消费者代码可以为您提供必要的数据，帮助您规划迁移，并提前提供正确的期望和说明。

## 示例 2 —固定传统整体式和/或分布式整体式

现在，假设我们需要构建一个新特性。这将需要创建新的表并更改您当前的代码库。这是一个改变做法的绝佳机会——也许添加一项新服务，使用一个新数据库，甚至重构现有的数据库。现在你需要问自己的问题是——这样做对吗？我们是在让事情变得更好还是更糟？再次回答这个问题，我们需要做一些代码评估，寻找以下内容:

*   代码有多大的隔离性？新表与其他表隔离了吗？
*   代码在数据库前面有服务接口吗？数据库是共享的吗？
*   上游和下游的服务依赖是什么？
*   我们有清晰、简单、明确的服务合同吗？
*   内部共享库呢？有没有滥用或者正在使用的重库？
*   测试覆盖率是什么样的？任何测试都包括公共合同吗？

新服务可能是好事，但不要认为它们是理所当然的。你可能会让一个分布式的整体变得更糟、更大，所以在移动之前先评估一下。

## 示例 3 —企业重组

假设您的业务正在从一个领域转向另一个领域。对于企业来说，改变商业模式、收购、出售和拥有多项可能一直在上升和下降的业务是很常见的。所有这些都意味着你可能需要改变所有权，但是谁将是所有者，拥有什么？您可能会开始管理一堆以前没有管理过的软件。

做一些代码评估是一个好主意，可以更好地优先考虑你的团队的未来行动，甚至决定什么团队应该拥有什么软件。在这种情况下，我们可以查找以下内容:

*   业务变化的频率是多少？
*   测试覆盖率是什么样的？任何测试都包括公共合同吗？
*   我们是否隔离了服务的合同和数据库？
*   我们有多少技术债务？最严重的罪犯是什么？

评估很重要，因为面对机遇，你将能够充分利用它们。否则，你可能会做更多老一套的事情，失去改善现状的宝贵机会。

## 气味、风险和特征

代码和设计气味可以帮助你。他们并不意味着 100%的麻烦，并有不同的危险程度。例如，一个写得很差的内部 DSL 要比缺乏数据库隔离要好，例如，与来自不同业务领域的三个服务共享一个数据库。

风险需要通过看趋势来看。这意味着随着时间的推移，事情会变得更好或更坏。特征是软件和团队中常见的行为，它们实时更新状态。举个例子，

*   经典的巨石柱往往充满了技术债务。所以，他们强制微服务发生。
*   错误的微服务的爆发倾向于创建分布式的单片共享数据库。
*   随着时间的推移，分布式整体往往会变得更糟，因为它们很难测试和发展。这可能会迫使你再次整体思考，让你在原地打转。

# 存货的价值

清单对于总结调查结果非常重要。无论您是在进行内部库迁移、修复整体，还是重组团队或产品，都没有关系。清单可以通过简单的表格或维基页面来完成。建立库存需要很长时间，但这是值得的。

## 你怎么知道你在打正确的仗？

不幸的是，在规模上，并不是所有的问题都能一下子解决。优先顺序需要切合实际，并得到你想要的结果。这并不意味着放弃或忽视问题，而是要有立竿见影的效果，并为取得更多改进创造动力。

# 前进

始终进行评估。了解代码中发生了什么对于做出好的调用和理解复杂性是至关重要的。任何人都可以执行代码分析；如今几乎所有公司都有 GitHub、GitLab 或可搜索的基于网络的公共或内部界面。下载源代码并阅读你的想法是一个好主意。

读代码比写代码难多了。我们行业认为初级工程师写代码，高级工程师删代码。但依我看，高级工程师会建立清单并分析代码，而不是不经分析就删除代码。

干杯，

迭戈·帕切科

*原载于*【http://diego-pacheco.blogspot.com】
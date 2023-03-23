# 7 面向解决方案架构师的 AWS 无服务器设计原则

> 原文：<https://betterprogramming.pub/7-aws-serverless-design-principles-for-solutions-architects-2be22717713b>

## 了解这些原则如何转化为设计新项目的指导方针

![](img/94ade0a1c0560654aceb112fb9710f0f.png)

[科学在](https://unsplash.com/@scienceinhd?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/s/photos/teacher?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上高清拍照。

如果你曾以任何身份与 AWS 合作过，你可能已经知道他们有一种独特的做事方式。他们开始开会[在沉默中阅读文件](https://www.inc.com/justin-bariso/jeff-bezos-knows-how-to-run-a-meeting-here-are-his-three-simple-rules.html)，他们通过逆向工作开始新项目[，无论他们做什么，他们都努力推动他们的](https://uxdesign.cc/working-backwards-my-experience-with-the-aws-design-strategy-f4bd90c87b8)[领导原则](https://www.amazon.jobs/en/principles) …。

亚马逊基本上统治世界是有原因的。他们做事的方式奏效了。

在他们为帮助公司构建一流软件而生产的众多工件中，有他们的通用设计原则。如果你曾经经历过一次 [AWS 架构良好的](https://aws.amazon.com/architecture/well-architected)审查，你会非常详细地了解它们(以一种好的方式)。

如果你喜欢[构建无服务器应用](/how-and-why-you-should-start-generating-your-serverless-infrastructure-diagrams-49cfd4568935)，AWS 有一套完全不同的设计原则，你应该遵循。他们采用架构良好的模型的核心支柱，并通过[无服务器应用程序镜头](https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/welcome.html)来看待它们。

今天，我们将看看[所有七项原则](https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/general-design-principles.html)，并讨论作为解决方案架构师，这些原则如何转化为您的设计。

# 1.快速、简单、独特

> “功能简洁、简短、用途单一，并且它们的环境可能符合它们的请求生命周期。交易具有高效的成本意识，因此更快的执行速度是首选。”

## **这意味着什么**

函数(在这种情况下是 Lambdas)应该是被聚焦的。它们旋转起来，完成一项工作，然后旋转下来。

尽可能缩短执行时间。尽可能利用异步工作流。将任务放入 SQS 队列进行额外处理，并停止执行原始 Lambda。尽量避免从一个 Lambda 中调用另一个 Lambda。

功能应遵循[单一责任原则](https://deviq.com/principles/single-responsibility-principle)。如果你需要做两个独立的动作，考虑使用两个独立的函数。

# 2.考虑并发请求，而不是全部请求

> "无服务器应用程序利用并发模型，并且基于并发性来评估设计级别的权衡."

## **这意味着什么**

不要花费大量的时间试图找出如何优化您的流程，使请求尽可能少。这不是重点。

这个原则很重要，因为它告诉解决方案架构师要依靠无服务器应用程序的能力，在高峰时间进行[水平扩展。如果你遵循设计原则#1，你的功能将会简短而甜蜜。他们会在几百毫秒内做出反应。](https://docs.aws.amazon.com/lambda/latest/dg/invocation-scaling.html)

在高峰时期，您的应用程序会向外扩展。它可能每分钟处理 100，000 个请求，但是对于您的最终用户来说，它会让您觉得只有他们在使用这个系统。会很快的。该软件将对重载做出响应。

记住这一点，不要太担心你提出的请求的数量。设计您的系统以利用 AWS 的自动伸缩能力。

如果你能把一个长期运行的任务分解成多个部分，那就去做。这个设计原则是关于[水平缩放超过垂直缩放](https://www.section.io/blog/scaling-horizontally-vs-vertically/)。通过将一个大而重的任务转化为小而单一的任务，你可以得到更高性能的软件，同时花费更少的钱。

![](img/8f61bb91fe278e7fe401a4289a59aee9.png)

*照片由* [*安妮·斯普拉特*](https://unsplash.com/@anniespratt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) *上*[*Unsplash*](https://unsplash.com/s/photos/not-sharing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*。*

# 3.不分享任何东西

> “功能运行时环境和底层基础设施是短暂的，因此不能保证本地资源，如临时存储。可以在状态机执行生命周期内操作状态，对于高度持久的需求，持久存储是首选。”

## **这意味着什么**

假设函数是无状态的。每当你需要对实体状态做些什么的时候，就去数据库。不要依赖全局变量。

优化 Lambda 性能的一个技巧是[全局实例化 sdk 客户端](https://pages.awscloud.com/rs/112-TZM-766/images/2020_0316-SRV_Slide-Deck.pdf#page=19)(查看第 19 张幻灯片)，但就此打住。因为你的兰姆达整天上下旋转，你永远不知道什么会在那里，什么不会在那里。

开始每一次执行，就像它是全新的一样，每次都加载您需要的内容。

# 4.假设没有硬件关联

> “底层基础设施可能会改变。利用与硬件无关的代码或依赖关系，例如，CPU 标志可能无法始终可用。”

## **这意味着什么**

您选择无服务器是有原因的:不必处理硬件。无服务器功能意味着在比 CPU 标志或其他硬件命令更高的级别上运行。

设计系统时，忘记服务器端硬件。使用环境变量进行配置，并在实现功能后[调整功能](/power-tune-your-serverless-api-for-happy-customers-289e83e3938f)。

# 5.用状态机而不是函数来编排您的应用程序

> “在代码中链接 Lambda 执行来编排应用程序的工作流，会产生一个整体的、紧密耦合的应用程序。相反，使用状态机来编排事务和通信流。”

## **这意味着什么**

如果你遵循原则 1 和 2，你应该设计一个健壮的模块化系统。一个命令可能需要 4-5 个小时才能完成。记住，这是好事。您已经使系统能够水平扩展，并在此过程中允许更高的吞吐量和性能。

不要从其他 lambda 内部调用 lambda。它更慢，[被 AWS](https://aws.amazon.com/blogs/compute/operating-lambda-anti-patterns-in-event-driven-architectures-part-3/) 认为是反模式，而且明显更贵。它的成本更高，因为您必须为内部 Lambda *和调用 Lambda*在等待时的执行时间付费。

[阶跃函数](https://aws.amazon.com/step-functions/)旨在解决这些问题。它们为您提供了一个易于理解的工作流状态机图，以及无与伦比的执行可追溯性。

如果 Step 函数对您的系统来说似乎有点重，您可以尝试一个 [express workflow](https://aws.amazon.com/about-aws/whats-new/2019/12/introducing-aws-step-functions-express-workflows/) ，它要轻得多，具有更高的吞吐量/并发性，并且收费类似于 Lambda。

![](img/e969c4056892ea093b7ae4cb71982279.png)

*形象由* [*约瑟夫*](https://pixabay.com/users/jmexclusives-10518280/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5614048) *而来*[*Pixabay*](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5614048)*。*

# 6.使用事件触发事务

> “诸如写入新的亚马逊 S3 对象或更新数据库之类的事件允许响应业务功能的事务执行。这种异步事件行为通常是消费者不可知的，并推动即时处理以确保精益服务设计。”

## **这意味着什么**

异步操作是你的朋友。您的客户不需要等待系统中发生的所有事情。拥抱[事件驱动架构](/how-to-integrate-your-app-with-webhooks-using-amazon-sns-a36aad22f3b1)。

这是软件工程师的自然想法:“如果发生了这种情况，那么另一件事也应该发生。”

无服务器应用程序本质上是分布式系统。为了让这些片段一起播放，使用基本事件，比如当一个新文档完成上传时使用*，或者在数据库写*之后触发的 [*DynamoDB 流*](https://aws.amazon.com/blogs/database/dynamodb-streams-use-cases-and-design-patterns) *来执行后续活动。*

软件不再需要同步。

设计您的系统时，要尽可能少地让您的用户等待。

# 7.针对故障和重复的设计

> 由请求/事件触发的操作必须是等幂的，因为失败可能发生，并且给定的请求/事件可能被传递多次。包括对下游呼叫的适当重试

## **这意味着什么**

异常时有发生。有时，由事件触发的 Lambda 会随机失败。作为解决方案架构师，您必须以能够处理相同请求的方式设计系统，并在系统上执行相同的结果。

这是[等幂](https://www.restapitutorial.com/lessons/idempotency.html)。

你应该将幂等性和重试设计到(几乎)应用程序中的每个 Lambda 中。阶跃函数提供了一种简单的方法，在遇到问题时，[使用可配置的补偿速率](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-error-handling.html#error-handling-retrying-after-an-error)重试 Lambdas。

由于您不能可靠地相信请求是否会以正确的顺序出现，或者它们只会出现一次，因此在设计系统时考虑到这一点是至关重要的。

# 结论

AWS 真的很了解他们的东西。在设计无服务器解决方案时，明智的做法是将这些原则放在自己的口袋里。

我们都希望系统快速、可靠且经济高效。遵循 AWS 无服务器设计原则将确保我们做到这一点。

所以记住，保持专注。保持小规模。并行执行的设计。不要担心你打的电话数量(在某种程度上)。与托管服务协调。设计时考虑到可重复性。

无服务器有时会令人害怕，有点吓人，但这需要练习。优秀的解决方案架构师会将复杂的问题转化为复杂的解决方案。一个优秀的解决方案架构师会将复杂的问题变成简单的解决方案。

很棒。按照预期的方式使用工具。

干杯！
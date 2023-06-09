# 永远不要在生产中使用概念证明的 4 个理由

> 原文：<https://betterprogramming.pub/4-reasons-you-should-never-use-proof-of-concepts-in-production-ca54432e52b9>

## 为什么你的迷你杰作永远见不到天日

![](img/9ff0cae19c4559c92ee7c3de3727da00.png)

由 [Faizal Sugi](https://pixabay.com/users/463259-463259/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=458842) 在 [Pixabay](https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=458842) 上拍摄的照片。

我从未遇到过不喜欢构建概念证明(POC)的开发人员。从零开始建立新的东西来测试一个理论/过程/技术的机会…我一想到它就兴奋。

他们身上有些东西让你的创造力源源不断。也许是你没有任何约束。也许这只是绿地开发的[快感。不管是什么，开发者都喜欢。](https://medium.com/better-programming/the-greenfield-paradox-why-is-building-a-new-app-so-easy-and-also-so-hard-b3cc58552ab)

构建概念证明的部分吸引力在于各种各样的用例。您可以为任何东西构建 POC！无论你[想创造一些有趣的东西](https://medium.com/better-programming/how-i-built-the-best-christmas-gift-ever-with-twilio-aws-and-outsystems-6c6bc79c1c9d)，[向某人传授一个陌生的概念](https://medium.com/better-programming/how-to-build-both-kinds-of-aws-lambda-layers-yes-there-are-two-edb945979f17)，[为一个企业创造一个想法](https://medium.com/pushtostart/how-i-built-an-automated-text-messaging-ordering-system-in-less-than-a-day-224445f16407)，还是[学习如何使用一项新技术来解决一个问题](https://medium.com/better-programming/how-i-built-a-serverless-geo-search-app-with-dynamodb-491879233754)，你都有无数的选择。

但是小心你的期望。一次又一次，我看到开发人员被要求将他们的 POC 投入生产。不要那样做！

概念证明仅仅是一个概念。它不是遵循最佳实践、既定模式和开发流程的生产主力。这是一个在短时间内传达一个想法的工具。

今天，我们将讨论为什么您的 POC 不应投入生产的四个原因。

![](img/cef33d8ee2a59b9da070e30984ac5029.png)

*照片由*[*Alexas _ Fotos*](https://pixabay.com/users/Alexas_Fotos-686414/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3094035)*上*[*pix abay*](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3094035)*。*

# 生产改变了你的建造方式

构建 POC 的一个好处是，您没有正常的开发约束。你可以[用任何你想要的语言写](https://medium.com/better-programming/how-to-choose-the-best-programming-language-for-your-new-project-d72b2e586aac)，把一些快速和肮脏的东西扔进去，然后[硬编码。](https://medium.com/better-programming/new-project-just-hardcode-it-bac72e1a231e)

只要你能证明你的观点，你如何实现并不重要。这是字面上的意思。

如果您假设这个 POC 将在生产中使用，那么您将对它进行稍微不同的编码。您将做出不同的设计决策，有意使用不同的模式构建，并且更加细致地处理错误。另一种说法是，你会走得更慢。

你应该把大部分时间花在构建真实的东西上，而不是把你的想法卖给产品所有者的概念验证。

概念证明应该在尽可能短的时间内传达你的想法。

软件不是由一个人建造的。它是由一个多元化的思想家团队建立的，旨在推动创造力和创新。POC 可能是由一个人创建的。当你想真正实现它时，你需要你的团队在那里挑战极限，增加它的潜力。

![](img/63474470e956f2fe1d2de96aa33da5fe.png)

*照片由* [*史蒂夫·布辛纳*](https://pixabay.com/users/stevepb-282134/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=880735) *上*[*pix abay*](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=880735)*拍摄。*

# 它不是为此而生的

你的概念验证是为了说明一点。这是一个想法。它不是为支持生产工作负载而设计的。

当你重新装修房子时，你在餐巾纸上勾画出你的想法，向你的朋友、家人和承包商展示你的想法。每个人都明白你想要什么，但你不会把那张潦草的餐巾纸给建筑工人。

不，你要让承包商在他们的 CAD 软件中画出来，计算数字，找到承重支撑，并在开始前正式设计。

你的概念证明是餐巾画。它服务于它的目的，并显示了一个明确的方向，但你不会把它扔进生产。它需要被详细阐述，正确设计，分解成故事，并记录在案。

如果您的开发团队遵循敏捷方法，您的过程将如下所示:

*   创建概念证明。
*   在 sprint 回顾中演示它。
*   让分析师提出完整的解决方案。
*   将解决方案分解成可增加业务价值的独立可行的故事。
*   在几次冲刺的过程中练习这些故事。

遵循此流程可确保采取适当的措施来构建*用于生产的*软件，从而提供更高的可靠性和安全性。

# 概念的证明是脆弱的

你做得很好。您构建 POC 的唯一目的是为新概念提供一个工作示例。它几乎没有花费你任何时间，所有的利益相关者都喜欢它。干得好！

不出所料，产品负责人要求您将它投入生产，因为对他们来说，您拥有一个工作产品。

但你知道真相。

没有错误处理。你开发了一个只适用于*快乐之路*的软件。一旦你偏离了你编写的工作流程，它就会崩溃。

在 sprint 审查中，您没有偏离演示脚本是有原因的。当你点击按钮 X 而不是按钮 Y 的时候，整个事情就散了。但是没关系！你建造了你应该建造的东西。概念证明显示了它应该显示的内容。

虽然像这样的情况肯定被认为是生产中的错误，但它们不是概念验证中的错误。请记住，这是一张餐巾纸图纸，而不是蓝图。

期待特性差距、有趣的错误、可悲的路径失败、[安全漏洞](https://medium.com/better-programming/micro-frontends-role-based-applications-and-you-5d6cd8d796eb)和[缺乏可观察性](https://medium.com/better-programming/the-challenges-of-stateless-architecture-and-how-to-monitor-your-serverless-application-94c0e8b8dd1)。这些随你的真实体型而来。但是现在，设定一个预期，你的 POC 比炎热的夏天路易斯安那州的沼泽还要糟糕。

![](img/8ce8e037560b48415ad34d00db43ffcc.png)

*照片由* [*犹贝克*](https://unsplash.com/@judebeck?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) *上* [*下*](https://unsplash.com/s/photos/thumbs-up?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) *。*

# 第二次总是更好

活到老学到老。迭代。练习。祝下次好运。

所有这些短语有什么共同点？

他们暗示你做得越多，结果就会越好。

你认为 NFL 球队在比赛中运行他们没有练习过的战术吗？不，他们没有。他们练习，练习，练习，再练习，希望练习得越多，表现得越好。

同样的概念也适用于构建软件。第一次构建某个东西(比如 POC)时，您处于发现阶段。你了解事情的来龙去脉，并设法拼凑出一些东西。

第二次构建时，您会移动得更快，因为您已经了解了一些细微差别。您编写的代码更易于维护，性能更高。你写*更好*。

你永远不会比现在知道的更少。

将概念验证作为一次学习经历。举例说明这个想法，并学习如何编码这个概念。抓住你了。准备好再做一次。准备好构建更好的软件。

# 结论

概念验证几乎总是值得花时间的。即使结果证明你不想使用某样东西，至少你没有花太多时间去弄明白它。

概念验证让您有机会尝试一些新的东西，并为您的实际构建权衡您的选择。他们不是你真正的体型。虽然有时候把代码扔进你的生产应用程序很诱人，但是不要这样做。

以此为参考。用你学到的东西作为指导，让事情变得更好。拧紧你做的东西上的扣子。迭代。

将您的注意力集中在 POC 构建上。尽快完成它。花尽可能多的时间来构建真正的具有安全性、可重试性、容错性和适当的错误处理的软件。

但最重要的是，享受它！尽情享受不受约束地编写代码吧！
# 作为软件工程师管理技术债务的 3 个技巧

> 原文：<https://betterprogramming.pub/3-tips-to-manage-technical-debt-as-a-software-engineer-1c386731ec6a>

## 了解什么是技术债务，以及作为一名个人开发者如何管理它

![](img/861459eeee2039abba5a88f63a0e48c7.png)

迪伦·吉利斯在 [Unsplash](https://unsplash.com/s/photos/debt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我是一名软件工程师，正在开发一个已经投入使用几十年的应用程序。

应用程序概念本身很简单。但是已经有十年的业务决策、团队变更、特性增强，以及更多的代码基础，使得它比它需要的更复杂。

除了性能问题和偶尔的错误，它做了它应该做的事情。但是坦白地说，给它增加新的功能对我来说很可怕。可以想象，如果打算维护和增强系统的开发人员害怕接触系统的某些部分，那就有点徒劳了。

这不是一个新问题。也不是开发者可以避免的。这被称为技术债务，它是大量研究和马丁·福勒惊人文章的焦点。但不幸的是，它在我们永无止境的积压工作中不够明显。

# **那么，什么是技术债务？**

维基百科告诉我们:

> “技术债务(也称为设计债务[1]或代码债务，但也可能与其他技术工作相关)是软件开发中的一个概念，它反映了由于现在选择一个简单(有限)的解决方案而不是使用一个需要更长时间的更好的方法所导致的额外返工的隐含成本。”

这是一个限制性的定义。这意味着在软件乌托邦中，如果开发团队没有选择“简单”的解决方案，并且有无限的时间来交付，就不会有债务。

从零债务开始是不可能的。软件设计包括一系列的权衡。伟大的软件工程师非常善于选择正确的折衷方案，拥有多年的经验和对未来的深刻理解。但是伴随着权衡而来的是妥协。因此，在我们构建的系统中，我们总是有一些技术债务。

顾名思义，技术债就像金融债。你需要从未来借钱来完成今天的交货。我们不能一直等待和过度思考未来的情况，否则我们将错过今天的机会。公平地说，软件应该在今天和明天帮助交付一个集体的商业成果，但是如果我们今天不交付，明天可能就没有了。

因此，和金融债务一样，科技债务并不是坏事。我认为这是必要的，因为我们无论如何都无法避免它。久而久之就会积累利息。这种兴趣使得开发者更难构建功能，也使得商业更难从中获取价值。

# 作为一个个体开发者，你能做些什么来帮助偿还这笔债务呢？

管理技术债务是一个必须在组织、产品团队、开发团队以及个人层面解决的问题。这一切的等级制度不都是人建立的吗，包括你？

在这一部分，我将做一些假设，因为这是我在与自己的科技债务怪兽战斗时的经验。

1.  你是敏捷开发团队的一员。
2.  您的敏捷团队在很大程度上是自组织的。
3.  您有代理机构来提供完成分配给您的功能所需的评估和工作。

# 寻找小规模设计返工的机会以及特性需求。

科技债务有办法用生产中的新特征抬起它丑陋的头。

重构会导致特性交付花费更长的时间，但是我们会在更好的软件稳定性上赢得时间。这种妥协需要你的开发团队内部有巨大的透明度。

有一段时间我像机器一样制造 bug Jiras。有一天，我正在做另一个项目，本来打算当天结束。我注意到已经有一系列补丁可以修复同一个 bug 的不同版本。这是修复这个问题的绝佳机会，因为现在我们有一个客户专注于关闭这个 bug。

我本来可以添加更多的补丁代码来修复这个 bug，但是我决定向开发团队推荐一个重构。重构比最初的估计多花了三天时间。但这比我们在未来修复更多同类错误所花费的时间要少得多。

我们付了部分本金，避免了将来产生利息。

# 提出来，清楚而经常

虽然上面的例子没有造成太多的延迟，但是一些重构本身就是成熟的项目。

因此，要经常清楚地向团队提出这些问题。在站立会议、回顾会议以及任何可行的情况下。强调它是如何阻碍你作为一个开发者对代码库进行修改和增强的。

开发团队在技术债务上可能犯的最大错误(在我看来)是继续绕过它。在脏衣服使你的房子窒息之前，把它们晾干。

我团队里的每个人都知道我对这个组件的由衷痛恨。在不经意的交流中，我把这件事告诉了我的经理和团队，好像这是一个我讨厌的讨厌的同事。但是它已经引起了一个转变。从事这个组件工作的人注意到了导致我们团队速度变慢的明显的设计错误。我们已经慢慢地开始开发文档并记录我们积压的债务。

谁知道呢，也许有一天我们能替换掉这个部件。那岂不是我一生中伟大的一天！

# 技术债务不是糟糕代码的借口

当然，一些不可避免的技术债务会在设计过程和我们随后对其进行的增强中悄悄进入系统。不要通过使用糟糕的编码实践在系统上引入另一层债务。

以下都是坏代码的例子:

严格和紧密耦合的软件。脆弱的代码，任何改变都会破坏它。未经测试的代码，它会在生产中出现在测试环境中从未出现过的边缘情况。

> “好的代码应该解释它在做什么。读起来应该没意思。一切都非常明显。这是很好的代码。”

罗伯特·马丁

# **结论**

我同意整个技术社区的观点，管理技术债务是一个持续的过程，就像金融债务一样，应该从最早的阶段就纳入预算。

但我知道我不能直接控制组织层面或预算的事情，所以我不谈论它。我尽我所能去弥补我看到的债务。我尽可能多的提起这件事。我试着坚持良好的开发实践，并且我对在自动化测试中覆盖边缘案例有点讨厌。

通过这些小的实践，我的团队正在积极地记录和管理这些债务。希望对你也有帮助！

[大家好，我是 Meghna，一名软件工程师，撰写关于技术、生产力和生活的文章。如果你喜欢你所读的，考虑加入我的个人时事通讯“一个接一个”，你可以做一些小小的改变，让生活变得更好 1%。](http://newsletter.meghnabhave.com/)
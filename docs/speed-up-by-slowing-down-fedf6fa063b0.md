# 通过减速来加速

> 原文：<https://betterprogramming.pub/speed-up-by-slowing-down-fedf6fa063b0>

## 实现软件开发的可持续发展

我绝对肯定在你生命中的某个时刻，你或你认识的某个人玩过街机赛车游戏。你坐在方向盘后面，脚踏和变速杆。在我年轻的时候，美国代托纳和世嘉拉力赛是很受欢迎的选择。你第一次玩的时候，大概是这样的:

![](img/1b409a94a4a0af9183497eedc0229e78.png)

[凯文·朗莱斯](https://unsplash.com/@laukev?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

你踩下油门踩到底。你飞过了比赛。第三名第二名第一名！你轻松地处理了前几个转弯。然后是急转弯。猛踩刹车，旋转方向盘，太晚了！粉碎。和你一起去山那边。火焰，烟雾。也许你甚至滚了。你翻滚的时候很酷。不过，你不再是第一名了。

如果这感觉像你写软件的方式，请继续读下去。拆开这个比喻，你可能患有以下任何一种症状:

*   在一次冲刺或主要功能之后，团队总是精疲力尽。
*   一切感觉都很‘停，开始’。有时工作跑在前面，有时你被堵住了。
*   有时你的团队取得了伟大的成就，有时却充满了困惑和混乱。
*   你走得很快，但你赢不了。每当您认为您已经完成了，另一个 showstopper bug 就会出现，或者需求会发生变化。
*   你不可能始终如一地达到你的预期。外部利益相关者对你的团队信心不足。

在这些症状背后，可能发生了以下一种或多种情况:

*   团队成员正在全力以赴地工作。如果您是一个共享资源，并且您使用了 100%的容量，那么您正在阻塞工作。
*   角色高度专业化，或者存在很深的知识孤岛。如果任何一个人生病、休假或离开团队，工作就会陷入停顿。
*   在获取新特性的竞赛中，在所有当前已知的需求都被正确理解之前，工作就开始了。
*   在推出新功能的竞赛中，一个功能被过早地宣布“完成”。

如果这是你的团队，有几个关键问题需要解决。很可能您已经针对 velocity 优化了您的工作实践。有人可能会说，或者你自己可能会想，“我们需要像发动机一样运转。”一台高度协调、高效的机器，每个人都有自己特定的角色。你已经这样做了，所以你可以走得很快，它正在工作。

但是如果一个零件坏了，发动机会怎么样呢？一切都停止了。不要造发动机。造一架喷气式飞机。它有四个引擎。即使其中一个坏了，它也能继续飞行。

另一个值得思考的相关思想是详述动量和速度的区别。如果你施加足够的力，你可以把一个乒乓球加速到很高的速度，但是它会被遇到的第一个固体障碍物弹开。相比之下，岩石以同样的速度运动。或者一块滚下山坡的巨石。

做一块巨石，而不是一个球。做喷气式飞机，不要做发动机。

# **如何用喷气发动机把你的团队变成一块会飞的石头**

让我们重温一下代托纳明喻。如果赛道不熟悉，或者你还不知道如何驾驶你的车队，你可能想降一两档，放松油门。嗯，是的，你会输掉第一场比赛…至少你不会崩溃。

将这转化为软件开发，我们需要关注几个关键的事情。放慢工作节奏，即不要撞到山的一侧，消除知识孤岛——建造我们的喷气式飞机。

以下是从个人经历中提炼出来的实用建议。有些是我的，有些是亲密同事的。所有这些都经过了战斗考验。它还假设您在您的组织中有试验不同过程和团队结构的自由和灵活性。如果你没有——对不起！至少你可以享受物理模拟，当你的车翻转过来，然后倒着停在赛道边。

## 停止冲刺。学习看板。

我不一定说*采用*看板。[但是理解它](https://www.digite.com/kanban/what-is-kanban/)。我们想慢下来的一个原因是帮助找出为什么——尽管我们试图以最快的速度前进——事情没有进展。看板为你理解工作流程和检测瓶颈提供了一些强大的工具。

还要考虑到“冲刺”这个术语意味着短期的突发活动，你应该预料到在结束时会感到疲劳。一个疲惫不堪的团队是生产力的大敌。从稳定的、可消化的、可实现的工作开始。很容易安排有规律的团队活动节奏，而不需要真正的“冲刺”。例如每隔一周的周一做计划。每隔一个星期五来一次复古。

## 采用特征标志驱动开发

我只是建议你停止冲刺，但是如果你仍然需要围绕一个发布周期来调整你的工作，这将是困难的。把自己从“释放危机”中解放出来。在发布前的一两天，你可能会问，“这个特性会出现吗？”在头脑中，这很容易变成，“这个特性*应该*能够实现。”

如果离发布还有一天，还有一天半的工作时间，开发人员会崩溃的。我们没办法！我们想要成功。我们希望它完成。如果这事悬在我们头上，我们无论如何也睡不着。但是我们将跳过对一些验收标准的彻底测试。或者通过省略一些“最好有”的重构来引入一些技术债务。

我们需要找到分离特性和部署的方法，这将消除任何纯粹的技术压力以达到目标日期。将所有代码保存在共享的“特性分支”上是一种方法，但不推荐。您将无法足够早地进行集成，并且很难遵循本文后面的一些建议。您的组织确实需要“持续集成”和“持续交付”。

如果您的组织中没有一个成熟的 CI/CD 过程，至少要以快速发布周期为目标。每周一次或每两周一次都可以。然后特性预先标记您所有工作，这样您就可以不断地将工作合并到您的集成分支中。这意味着你可以在某个东西准备好的时候打开它，并从你的常规发布周期中获得更大的独立性。

## 采用结对编程

根据我的经验，当结对编程做得正确时，没有比这更快的方法来消除团队中的知识孤岛和人力瓶颈。但是仅仅知道结对编程是两个人在一台计算机上是不够的。我已经写了一篇关于如何将结对编程集成为常规工作方式的文章[这里](https://hackernoon.com/pairs-are-not-a-sometimes-food-2c2b6857af8e)。

## 小团队，T 型工程师

“两块披萨”团队受欢迎是有原因的。对于一个自主的、跨职能的工程团队来说，六到八个人是合适的。六个开发人员会给你三个工作对，这足以完成一些体面的工作。这些应该是“全栈”工程师。

专业化是好的，也是被鼓励的，但是如果你的某些人不能或者不愿意接触系统的某些部分，结对是行不通的。添加交付线索，并加入嵌入式测试人员或设计人员，增加一点额外的味道。

## 定义“完成”

在一个故事开始编码之前，应该有一个“完成”的定义。这包括的内容因组织和团队而异。但至少你可能会有验收标准和用户界面模型。

事先澄清这一点并不是“瀑布”，因为你一次只处理整体的一小部分。但是，如果您在实现过程中意识到设计不正确，或者不同的利益相关者对范围内或范围外的内容有不同的想法，它肯定会帮助您消除您可能会经历的“停止/开始”的颠簸。

## *一对，一个故事。*

大多数用户故事至少有一个前端和后端组件。在微服务环境中，可能还需要跨多个服务的工作。将这项工作分成两人一组很有诱惑力。反抗！我更喜欢让一对端到端地处理整个用户故事。为了重新获得效率，并行运行多个故事。

这避免了由于通信开销、协调工作和切换造成的效率损失。这也使得推断任务的当前状态变得更加容易。

这是一个软规则，可以打破。我们称之为“群集”。群集需要更高程度的监督，因为任务可能会在两人之间丢失。要意识到这种风险，并做出相应的补偿。例如，指派一名“功能负责人”来协助协调可能会有所帮助。

## *每两天轮换一对*

每隔两天，在你的团队中进行一次结对交换。理想情况下，这应该是你日常站立仪式的一部分。避免连续四天以上从事同一项工作。尝试加入一个你还没见过的任务。如果你已经看过心流中的所有任务，与你最近没有合作过的人配对。

这里的目的是消除知识孤岛。这意味着你永远不会陷入一个人掌握所有秘密的情况。这也被称为“总线因子”。比如，“在我们的项目面临失败的危险之前，需要有多少人被车撞了？”在没有交换的情况下对一个特性进行结对编程，至少可以得到两倍的总线系数。但是你可以通过引入旋转做得更好。

我建议两天，因为从不交换，或很少交换配对，会减少一些有益的副作用，如知识转移。另一方面，过于频繁的切换会破坏任务的整体流程。根据多年的实验，两天是个神奇的数字。

## *团队计划*

作为一个团队一起执行产品规划，至少是高层次的技术规划。将详细的计划留给一个功能团队或最初承担工作的两个人可能很有诱惑力。对此引用的理由通常是效率。我们不想‘浪费大家的时间’。然而，如果你团队中的任何人都有可能参与一项任务(因为你们是轮流搭档)，那就不是浪费时间。

相反，如果你把太多的实现细节留给这两个人，你就冒着有人转到一个任务上并质疑实现和架构的风险。这在最好的情况下会浪费时间，在最坏的情况下会导致团队内部的冲突。

这并不是说工作的方向永远不应该改变。通常，当代码开始出现在屏幕上时，您会发现原始计划中的缺陷或遗漏的需求。随波逐流。但至少要确保每个人都知道河水流向。

## 回顾、反思、加速

经历一段时间的变化可能会减缓你的交付。即使我们没有刻意改变来控制我们的步伐，那也是真的。但是随着新的工作流程变得舒适，你需要寻找方法再次加速。

在这里很难做出规定，因为不可能预先知道什么会让你最慢下来。不起眼的回顾展可能是你最有力的工具。接受事情不会尽善尽美。接受它需要时间去适应，需要时间去做正确的事情。寻找小的行动项目。你下次可以尝试的东西，调整，调整。

例如，您可能会发现技术规划会议总是要持续一段时间。在回顾中，你可能会意识到这是因为你浪费时间争论不明确的需求。一个可能的解决方案是，在规划会议的前半部分，让产品或利益相关者代表在场，或者随叫随到，以澄清出现的任何问题。

密切关注，找出不顺利的地方，并进行实验。

# 可持续速度

采取谨慎的措施来减缓交货速度会让人感觉像是强行推销。结对编程、整个团队规划……仅仅这几个词就让人担心产量会减少 50%甚至更多。

就我自己而言，我在一个没有测试、没有结对、甚至很少进行任何代码审查的工作场所工作了多年后，过渡到了全职结对编程。起初，确实感觉事情进展缓慢。然后我意识到一件事。当一项任务完成时，它就完成了。我们不必一次又一次地回去重新访问它，修复错误，“强化它”，添加缺失的功能。

有一种极限编程实践叫做“[可持续速度](https://www.agilealliance.org/glossary/sustainable/)”。不仅仅是消除加班。配对有助于此。规划有助于这一点。工作从未停止。我们接受事情进展得稍微慢一点，在我们的估计中考虑到这一点，工作继续进行。产品团队学会相信，当我们说到做到的时候，我们就会交付，我们构建的软件就会正常工作。

我们没有“强化冲刺”。我们没有“唯一了解 ACL 系统的人”。如果有人生病或休假，工作会继续进行。他们没有掌握专属于他们的知识。没有只有他们能维护的系统。

如果我们将软件开发视为一场[马拉松，而不是短跑](https://hackernoon.com/software-development-sprint-vs-marathon-mindsets-3bbb7505a7ab)，实现可持续的速度实际上是欺骗。这就像跑 400 米跑道是接力赛一样。但是接下来的 400 米呢？下一公里？一个训练有素的运动员将会一次击败你的接力队到达终点线。然后他们会停下来休养生息。与此同时，你的团队仍在不停地生产软件。

在软件领域，没有终点。
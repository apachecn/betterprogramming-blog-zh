# 打破你自己的系统:混沌工程

> 原文：<https://betterprogramming.pub/break-your-own-systems-chaos-engineering-875ac9683fb4>

## *使用混沌工程，通过有控制地故意破坏东西来提高系统的弹性*

![](img/d60368b2c54aa1e9c9927edd339a866b.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/chaos?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

现代软件应用程序及其运行的基础设施正变得越来越复杂。通过 API 或消息队列集成的多个微服务的网络，每个微服务都有自己的数据库。在集装箱内运行，根据交通流量的大小，集装箱每天会上下旋转几次。部署在不同地理区域的云基础架构上。所有这些都会产生日志、指标和跟踪，并被潜在的大量用户日夜不停地使用。所有这些都需要产品团队更频繁地进行更改和添加新功能，同时实现零停机时间和对用户的零影响。

这些复杂的系统也会以越来越复杂的方式失效。应用程序可能会有奇怪和意想不到的错误，API 版本可能会引入突破性的变化，消息队列可能会崩溃，数据可能会损坏，云基础架构的整个区域甚至可能会离线。一个团队所做的更改可能会影响另一个团队所拥有的在其他地方运行的独立服务，并且没有任何警告。像黑色星期五这样的事件周围的大规模流量高峰可能会淹没系统的一部分，并影响您的客户体验。或者一些完全意想不到和看似随机的事情可能随时发生，并带来灾难性的后果。

那么，你如何理解这种疯狂？如何避免一辈子不眠之夜担心这个脆弱的纸牌屋随时会倒塌？这就是混沌工程可以帮忙的地方。

# 什么是混沌工程？

官方定义称:

> “混沌工程是在生产中对软件系统进行实验的学科，目的是建立对系统承受动荡和意外情况的能力的信心。”— [维基百科](https://en.wikipedia.org/wiki/Chaos_engineering)

换句话说，混沌工程包括在生产环境中以可控的方式有目的地导致事情失败，目的是了解这些失败是如何表现的，它们的影响是什么，以及系统是否能够如预期的那样检测到这些失败并从中恢复。

这里有几个要点需要强调:

*   生产——真正的混沌工程包括打破生产环境，尽管这看起来很可怕。
*   受控——我们谈论的是采取科学的实验方法并限制它们的影响。
*   预期—我们正在验证我们预期会发生的事情确实发生了。这不仅仅是一个“这个红色的大按钮是做什么的”的问题。
*   学习—目标应该是进一步了解我们的系统，以便我们能够实施改进。

本质上，它是关于接受在任何足够复杂的系统中，一些东西要么已经坏了，要么在任何时刻都将坏。要防止每一个可能的故障发生几乎是不可能的，所以让我们集中精力不断测试当不同的故障发生时会发生什么，以增加我们对系统的弹性和优雅地处理故障的能力的信心。

这是一个持续的旅程，而不是一次性的活动，应该成为你的工程文化和实践的关键部分。

![](img/aac78942a98b7851ff9d8b8b17520e90.png)

布雷特·乔丹在 [Unsplash](https://unsplash.com/s/photos/chaos?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 更少混乱，更多工程

当我们的团队第一次开始冒险进入混沌工程的时候，我们在处理它的方式上有些天真。

我们会举行所谓的“游戏日”，我们几个人秘密计划，制造某种失败，然后观察结果。我们最初的几个游戏日包括，例如，停止一个数据库实例，同时把几个有知识的关键工程师藏在大楼另一层的一个房间里。然后，我们会坐下来，观察团队跑来跑去，头发着火，试图自己解决问题。

这有点过于偏向混乱，而不是使用合理的受控工程方法。我不建议遵循这种方法！这有助于确认我们团队中的一些单点失败(即，只有一个人知道如何解决这个问题，如果他们碰巧发现自己被锁在会议室里，我们无法很好地应对)，但我们最终学到的东西很少。

所以请不要让名字欺骗了你。这并不意味着仅仅通过从墙上拔下电缆或关闭电源来制造混乱。我们正在讨论采用工程方法来验证和了解我们的系统。

# 混沌实验和应用一些科学

处理混沌工程的正确方法是，首先创建一个你想要验证的假设，以可控的方式实施一个实验，观察结果，并将其与你的假设所暗示的情况进行比较。在此基础上，您可以确定任何差距，并实施进一步的自动化或流程更改，以减轻未来的特定问题。

让我们更详细地介绍一下运行混沌实验的不同步骤。

## 建立一个假设

您首先需要了解系统中的正常行为是什么样的，以及您想要测试什么样的故障。作为一个团队这样做是非常有效的，可以帮助确定重点关注的领域。召集一群软件工程师、测试人员、软件工程师，以及任何能够洞察系统如何运行的人。问问自己哪里可能出错，并确定系统中需要重点关注的优先领域以及可能出现的不同故障场景。

一旦你发现失败是你实验的一部分，你的假设应该陈述失败发生时的预期行为。

您还应该确保您知道要寻找的指示系统行为的指标和信号。你认为某个地方会发出警报吗？您希望在特定的仪表板上看到流量峰值吗？

例如，当一个微服务崩溃时，我们希望在我们的监控工具中触发一个警报，让微服务重新启动，连接到数据库和消息队列，而无需手动干预。

## 模拟失败，带来混乱

运行您的实验，模拟您发现的现实世界中的失败。这可能意味着停止一个数据库实例或 Kubernetes pod，使用高网络流量进行 DDoS，增加请求延迟，导致 CPU 或内存出现峰值，等等。

有工具可以帮助注入这些故障，或者可以简单到在机器上手动运行命令和脚本。

## 观察系统行为并验证假设

现在你可以验证你所看到的是你期望看到的。使用作为假设一部分的度量和监控工具。

记住也要寻找任何额外的意外结果。注意系统中其他需要进一步调查的区域发出的警报，并警惕连锁故障造成的影响超出预期。

你的实验有两种可能的结果:

1.  系统按预期运行。
2.  你推翻了你的假设，一些不同的事情发生了。

## 修复系统中的任何问题

确保您跟进并实施任何措施，以防止未来再次发生意外故障。这可能意味着向 CI/CD 管道中添加更多的自动化测试，扩展到一个数据库集群而不是单个实例，在两个微服务之间添加一个消息队列以增加解耦，或者如果没有看到预期的警报，甚至对您的监控和可观察性进行更改。

![](img/cc0e946639f84dcdd290bd45d8efc4b3.png)

照片由 [ThisisEngineering RAEng](https://unsplash.com/@thisisengineering?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/engineering?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。

# 最佳实践和其他考虑

请记住，我们谈论的是采取工程方法，而不是故意造成真正的失控混乱。有很多方法可以确保你的实验尽可能安全。

## 度量、监控和可观察性

确保你知道如何检测出问题。仪表板、指标、日志、跟踪和警报需要到位。否则，你是盲目的，不知道你的系统是如何运行的。

在你开始实验之前，花时间了解你的系统中“正常”是什么样子，这样你就不会误解信号。例如，在正常操作期间，CPU 可能以 40%的速度运行，但是如果您不知道这一点，那么在您的实验期间，它可能看起来是一个相当高的值。这也是可观察性所鼓励的。

## 最小化爆炸半径

可能最好避免破坏整个生产环境的实验。将测试范围集中在系统的特定区域，并尽可能减少任何级联故障——或者至少能够快速检测并从中恢复。

## 有一个逃跑计划

如果需要的话，确保您总是有办法中断实验，并且您可以立即回滚任何更改以恢复到工作状态。自动化工具在这方面也有帮助，因为它们通常有办法立即停止任何故障注入，并允许系统恢复。

## 在生产环境中运行实验

对于刚接触混沌工程的团队来说，这听起来很可怕，但这是正确的做法。生产是您的客户生活的地方，也是您想要测试弹性和可靠性的地方。

生产环境总是独一无二的，无论您使用多少代码基础设施或拥有多少数据库副本。有更多的流量，不同的使用模式，以及其他你从未考虑过的细微差别。

当然，首先在非生产环境中实践混沌工程比直接投入更有意义，如果这有助于给你的团队(和你的经理！)更有信心。

## 自动化混沌实验并持续运行

当你运行了一个成功的实验后，试着更频繁地自动运行它。这允许持续验证您所采取的任何缓解措施，并有助于检测您之前观察到的相同问题的再次出现。

一些团队甚至让这种自动化的混乱在他们的生产环境中连续地、随机地发生。众所周知，网飞有一些工具可以在特定时间窗口内随机终止微服务甚至整个区域的云基础设施，这是对您的弹性和从故障中恢复的能力充满信心的最终标志。

# 工具和技术

随着这个领域的兴趣和成熟，越来越多的工具和技术可以帮助你实现混沌工程。

对于高级产品，可以考虑 Gremlin，它提供了一整套将故障注入系统并验证假设的工具。或者，如果你在 AWS 上运行，他们现在甚至通过他们的故障注入模拟器提供“混沌工程即服务”。

如果你更喜欢走开源路线，那么总有网飞开发的著名的混沌猴。或者看看 Litmus，它是云本地计算基金会(CNCF)的一个沙盒项目。

如果你想让混沌工程变得更加有趣，甚至还有一些稍微古怪的选择！KubeInvaders 是一个*空间入侵者*启发的界面，用于杀死 Kubernetes 豆荚。或者还有 Kube Doom，它使用了一个围绕旧视频游戏构建的类似概念。

# 结论

混沌工程是测试和提高系统可靠性和弹性的好方法，并最终增强客户的体验。

随着系统变得越来越复杂，变化越来越频繁，我们防止失败和不利影响的方法也必须如此。

有条不紊地接近它，经常进行实验，收获对系统信心增强的好处，并从中获得乐趣！
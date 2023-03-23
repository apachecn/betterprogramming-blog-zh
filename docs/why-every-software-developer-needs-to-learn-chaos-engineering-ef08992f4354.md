# 为什么每个软件开发人员都需要学习混沌工程

> 原文：<https://betterprogramming.pub/why-every-software-developer-needs-to-learn-chaos-engineering-ef08992f4354>

## 混沌工程是在生产中对软件系统进行实验的学科

![](img/81c4423c360a35caeb65e48112f69f1c.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

作为一名负责管理大型系统的技术专家，您总是需要考虑系统的连续性、安全性和可管理性。

最近，我花了很多时间来更好地了解如何为大型分布式系统建立一个合适的灾难恢复(DR)和业务连续性计划(BCP)。

我对这个话题的探索最终导致了一种每个系统都应该采用的新工程——*混沌工程！*

我最后读了一本免费的奥莱利的书，名为 [*混沌工程——通过实验*](http://www.oreilly.com/webops-perf/free/chaos-engineering.csp) *建立对系统行为的信心。* 我知道[网飞的《混乱的猴子》](https://github.com/Netflix/SimianArmy/wiki/Chaos-Monkey)已经很多年了，但我决定永远不说我在打造另一个网飞，直到我读完这本书。

这本书以一个非常有趣的混沌工程定义开始—

> **“混沌工程**是一门在分布式系统上进行实验的学科，旨在建立对系统承受生产中动荡条件能力的信心。”
> —混沌原理

来自 [Gremlin](https://www.gremlin.com/the-discipline-of-chaos-engineering/) 的另一个非常简单但有意义的定义是:

> "故意破坏东西来建立更有弹性的系统！"

我从没想过混乱可以用来带来纪律。

在分布式系统中，你永远无法防止所有的故障，但你可以随时做好准备。当您在基础设施上进行试验时，您会逐渐了解系统的弱点，并可以更好地计划如何避免这些弱点。

到现在为止，你一定想知道——混沌工程和测试有什么不同？让我们试着更好地理解这一点。

# 测试与混沌工程

大多数时候，一个好的测试计划会谈到负载测试、安全性测试和负载时的功能测试。

不幸的是，我们只在非生产环境中进行这些测试。我们只测试非生产环境，并希望系统在生产环境中表现相同。

这是混沌工程试图让我们做好准备的地方，通过尽可能接近生产环境的实验，有时甚至是在生产环境中。

测试和混沌工程之间的另一个区别是，混沌工程的成果带来了关于系统的新知识，甚至开发人员或测试人员可能都没有意识到。

# 混沌实验

现在，让我们试着去理解混沌实验是什么样子的。

1.  如果您正在运行 [Hadoop](https://hadoop.apache.org/) 环境，关闭 Hadoop 集群的一些节点，甚至通过关闭高可用性组件来产生混乱。
2.  在 [Kafka](https://kafka.apache.org/) 集群上，从特定分区或主题中删除消息。
3.  让系统隐形不同步。
4.  耗尽 [Elasticsearch](https://www.elastic.co/) 集群、NoSQL 集群、web 服务器等的 CPU/内存。
5.  在函数中注入混乱。

这些是一些例子，对你的系统进行适当的研究将有助于为你的系统确定实验。

# 谁实践混沌工程？

已经有社区建立起来，像 [http://chaos.community/](http://chaos.community/) ，专注于这个工程学科。

这个社区表明，混沌工程的实践者在像谷歌、亚马逊、优步、雅虎这样的公司里。、Dropbox、Visa、New Relic 等。这个数字每天都在增长。

这种做法不仅限于基于软件的公司，而且在国防、制造、农业、医疗、金融等领域也在增长。

# 该不该练混沌工程？

引用书中的话:

> *“您的系统对现实世界中的事件(如服务故障和网络延迟高峰)有弹性吗？”*

如果答案是否定的，那么你需要练习混沌工程。您应该有强大的监控系统来了解系统的状态。

随着我们越来越多地转向基于微服务的架构，实践混沌工程变得至关重要。

# 混沌工程高级原理

根据[http://principlesofchaos.org/](http://principlesofchaos.org/)，以下是混沌工程的高级原理。

## 1.围绕稳态行为建立一个假设

关注系统可测量的输出，而不是系统的内部属性。

通过在实验中关注系统行为模式，混沌验证了系统*确实*工作，而不是试图验证*如何*工作。

## 2.变化真实世界的事件

混沌变量反映了真实世界的事件。根据潜在影响或估计频率对事件进行优先级排序。

考虑与硬件故障相对应的事件，如服务器死亡，软件故障，如错误的响应，以及非故障事件，如流量峰值或扩展事件。

任何能够破坏稳定状态的事件都是混沌实验中的潜在变量。

## 3.在生产中进行实验

根据环境和流量模式的不同，系统的行为也会有所不同。Chaos 强烈倾向于直接试验生产流量。

## 4.自动连续运行实验

混沌实验应该连续运行，而不是一次性或定期检查。

## 5.最小化爆炸半径

在生产中进行试验有可能给客户带来不必要的痛苦。因此，混沌工程师需要确保实验的影响是可控的。

# 我如何设计我的混沌实验？

正如这本书所建议的，可以通过考虑以下步骤来设计混沌:

1.  选择一个假设。
2.  选择实验的范围。
3.  确定你要观察的指标。
4.  通知组织。
5.  进行实验。
6.  分析结果。
7.  扩大范围。
8.  自动化。

步骤和细节可能因系统而异，但总体设计应该基本围绕上述步骤。

# 当心

请记住，首先要理解，然后才实施混沌实验。你要对自己的行为负责。

# 进一步阅读

如果你认为这是你想尝试和了解更多的东西，看看下面的内容:

1.  [http://www . or eilly . com/webops-perf/free/chaos-engineering . CSP](http://www.oreilly.com/webops-perf/free/chaos-engineering.csp)
2.  http://principlesofchaos.org/
3.  [https://www . gremlin . com/the-discipline-of-chaos-engineering/](https://www.gremlin.com/the-discipline-of-chaos-engineering/)

```
Hey, if you enjoyed this story, check out [Medium Membership](https://deshpandetanmay.medium.com/membership)! Just $5/month!*Your membership fee directly supports me and other writers you read. You’ll also get full access to every story on Medium.*
```
# 什么是架构决策记录？

> 原文：<https://betterprogramming.pub/what-is-architecture-decision-record-110c597c13d>

## 了解为什么你的公司或团队做出某些软件设计决策

![](img/a6e1bada94371974e79a8dab3c67c96a.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com/s/photos/planning?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

> *波兰文版文章可在下面* [*链接下*](https://bulldogjob.pl/news/1087-czym-jest-architecture-decision-record)

您是否曾经想过为什么您正在开发的应用程序使用一种给定的技术？为什么选择 Java 而不是 Python？为什么你需要微服务或一个整体？开发人员总是会想到大量的问题。通常，我们被告知这些是设计规则，我们被这些规则束缚住了。这些决策是谁做出的，什么时候做出的，为什么选择这些决策？这些选择背后的人还在这里工作吗？

可能有一个团队在做重要的决策。他们可能理解设计原则和规则。这些人通常被称为*架构师。如果我们不用每次都被迫询问他们的动机，而是让他们明白并随手可得，那就太好了。每当我们做出一个会影响我们的软件设计和其他人的工作的重要选择时，我们应该让它有迹可循，并留下一些东西。这将使我们有可能从这些决定中学习，并了解情况和替代解决方案。*

架构决策记录(ADR)是一个描述这一点的小文档。有多种简单和复杂的模板可用于入门。每个决定都保存在一个单独的小型 ADR 文件中，其中包含对问题、环境、备选方案、要点和最终决定的描述。它可以保存在代码库中的一个单独的文档中，并经历一个常规的代码审查过程。我们将能够清楚地看到关键挑战是什么，以及我们的架构是如何随着时间的推移而发展的。这种方法非常有利于知识共享和透明度。

一个流行的 ADR 模板由 Michael Nygard 提出，由以下部分组成:

我们不必非常严格，需要时可以修改。ADRs 的伟大之处在于，我们还可以记录被拒绝的解决方案，并向每个人解释为什么这种方法没有好处。让我们看一个 ADR 的例子。我们正在用 PHP 开发多个微服务，并希望将使用的 web 框架标准化。它应该具有较低的开销，并且易于被现有的开发团队采用。

使用轻量级 ADRs 也是 ThoughtWorks 的技术雷达推荐的开发技术。

# 资源:

[](https://www.oreilly.com/library/view/design-it/9781680502923/) [## 设计它！

### 不要通过巧合来设计——像你想的那样去设计！充满实用技术，设计它！是完美的…

www.oreilly.com](https://www.oreilly.com/library/view/design-it/9781680502923/) [](https://github.com/joelparkerhenderson/architecture_decision_record) [## joelparkerhenderson/建筑 _ 决策 _ 记录

### 架构决策记录(ADR)是一个文档，它记录了一个重要的架构决策以及…

github.com](https://github.com/joelparkerhenderson/architecture_decision_record) [](http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions) [## 博客|记录架构决策|相关性

### 标签:敏捷项目的敏捷和架构架构必须被不同地描述和定义。不是全部…

thinkrelevance.com](http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions)
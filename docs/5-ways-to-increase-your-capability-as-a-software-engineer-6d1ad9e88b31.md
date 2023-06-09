# 提高软件工程师能力的 5 种方法

> 原文：<https://betterprogramming.pub/5-ways-to-increase-your-capability-as-a-software-engineer-6d1ad9e88b31>

## 需要立即改进的重点领域

![](img/1653709651c4f1efaceae387f6ea0798.png)

软件工程师的角色是解决复杂的问题。理想情况下，se 发现并分析需求，并采用最佳实践、设计模式、代码、运行时、框架、API 和库来交付简化但全面的解决方案，同时降低未来挑战的可能性。

作为一门学科，软件工程需要强有力的批判性思维、对细节的关注、编码的熟练程度以及保持恒定学习速度的纪律。

知识和经验使系统工程师与众不同，然而角色或项目的构成数量经常变化。作为 SE 的改进需要扩展这两个领域。

在这里，我分别列出了几个我认为非常有效的实践和领域。

# **优先阅读**

尽可能多、尽可能经常地阅读，尽管不是为了记忆信息，而是为了积累更多的理解。有许多权威的指南、掌握系列、入门、常识，以及你能找到的任何语言、框架、模式或运行时的必读书籍。这些标题中最好的恰当地代表了主题的广度和深度。

Medium 是一个很好的资源，可以在广泛的软件工程主题中找到更集中的阅读内容——专注于特定组件或更一般主题的各个方面的故事。

在我们的日常工作中，最大限度地实现某项特定技术超出了建议的解决方案的范围，或者超出了必要的复杂性阈值，这种情况很常见。尽管如此，考虑到适当的要求，了解这些功能是有益的。根据我的经验，专业知识的缺乏导致的次优解决方案和严格的技术限制一样多。

顺便说一句，鉴于本节中概述的意图，我不一定建议做笔记。在我看来，传统笔记有一个收益递减点。我相信你正在牺牲你的曝光率来换取潜在的未实现的留存率的增加。如果有什么不同的话，我会建议用简短的主题引用来代替传统的笔记，一两个词虽然足够具体，但可以在第一页的谷歌搜索结果中找到。你可能会发现这非常有效。

# **缩减、重用、重构**

如果你有机会写代码，就重构它。更好的是，您必须重构您的代码，或者在可能的情况下丢弃它。如果你已经假设了一个代码库的所有权，研究它，直到你可以自信地删除任何腐烂的东西，要么丢弃它，重构它，或者用库功能替换它。

除了引入潜在的性能改进之外，重构通常还提供了对新获得的领域知识或语言特性采取行动的机会。示例包括可选链接、无效合并、不可变变量、纯函数或异步/等待承诺处理。例如，您可能还会重新评估您实现的数据结构，以便更喜欢字典或对象而不是数组。

编码是提高自己编码能力最有效的方法。如果你没有积极地编写新代码或重构现有代码，我建议你寻找代码来回顾。理想情况下，在您的环境中，也许是您的同事，或者甚至来自其他项目或所需的库。理解好的代码是生产好代码的第一步。

# **记得给你的宠物喂食**

如果你还没有一个宠物项目或有一个具体的项目想法，你可以克隆一个。有几个公共的 Git repos，带有社交媒体或按需交付应用程序的模板代码。

Pet 项目是对设计模式、框架或语言(例如，动态类型与推断或静态类型)和 API 消费更为熟悉的极好的试验台。例如，Google Firebase、PubNub 和 Vonage 为它们的公共服务 API 提供免费的服务层。如果您不熟悉的话，Firebase 是一个 BaaS(后端即服务)平台，它提供身份验证、对象存储和远程功能执行(功能即服务)以及其他服务。

PubNub 提供了用于实时位置跟踪和消息传递的 API 和 SDK，Vonage 提供了用于 SMS 消息传递的 API。所有这些现在都是许多现代应用程序的普遍特征。

API 消费有助于考虑和实现抽象、代码可维护性和模块化。所有这些都是质量代码和设计的特征。

# **提前计划，避免计划外停机**

首先识别并解决问题，然后构建解决方案。在项目的开始阶段，人们通常会下意识地开始构建:编码、安装二进制文件或修改配置元素。我目睹过那些支持*的组织失败得很快，敏捷——像*的开发实践通过降低计划和设计的重要性无意中促成了这种行为。

随着解决方案生命周期的进展，与规划不足和/或次优设计相关的成本只会增加。同样，抽象、通用设计模式的实现和错误处理等设计考虑也是提高解决方案质量的常见领域。

当您进行到构建阶段时，适当的测试覆盖可以显著减少代码中不希望的结果。如果函数、组件、API 等。，是必不可少的，它应该有一个单元测试，最好是一个预定的和自动化的。

# **强调沟通**

无效的沟通是通向次优或更糟的不可用解决方案的最短路径。警惕非正式的需求收集、稀疏的需求文档和有限的股东代表。这些是不良结果的常见领先指标。

如果可能，推后不可量化的结果或指标，澄清任何不明确的数据，并请求和记录对设计或实施警告的确认。

# **返回值**

概括地说，每天阅读以增加你的领域知识，经常重构你的代码库，清晰地交流，并记录下来。

开发喜欢的项目，优先计划，对任何重要的事情进行测试。提高你作为软件工程师的能力，你就提高了你交付的解决方案的质量和弹性。

感谢阅读。
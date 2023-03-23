# 你应该雇佣软件工程师还是数据工程师？

> 原文：<https://betterprogramming.pub/should-you-hire-a-software-engineer-or-a-data-engineer-f89169b05cf2>

## 数据项目需要不同于软件工程项目的技能

![](img/9dd1a180439a885db1e9c795a5d13fd6.png)

来自 [Pexels](https://www.pexels.com/photo/crop-hacker-silhouette-typing-on-computer-keyboard-while-hacking-system-5240547/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) | [品牌内容披露](https://www.annageller.com/disclosure)的照片 [Anete Lusina](https://www.pexels.com/@anete-lusina?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 。

我们可以从分享显示我们每天生成的数据量增长速度的统计数据开始这篇文章。我们可以深入研究大数据和人工智能如何迅速改变我们生活的许多领域。但是我们不再需要解释它了。每个人直觉上都知道。

那么少了什么呢？拥有合适技能的合适人选。

# 数据工程不等同于软件工程或数据科学

如今，几乎每家公司都在努力变得更加数据驱动。为了实现这一点，许多公司在招聘软件工程师时都认为挑战纯粹是技术层面的。但是这种假设过于简单，并且不承认处理大规模数据项目所需的专业技能。

我从事过数据和软件工程项目，我相信它们需要有不同背景的人。这两种角色都是需要的，从更广泛的意义上说，它们是相辅相成的。

在软件工程项目中，我们需要知道如何实现结构良好的抽象和应用程序的程序员。相比之下，数据工程项目涉及大规模提取和转换大量数据(通常是实时的)，并将这些数据整合到更广泛的数据生态系统中。这两种类型的项目需要相当不同的过程、工具和技能。

# 代码很重要，但数据至关重要

不可否认，处理数据的代码必须经过彻底的测试和良好的设计。然而，软件工程师通常更重视代码质量而不是数据质量。代码是提供价值的载体，但它本身并不提供价值。最后，如果数据是错误的，因为代码的最终目标没有得到满足，那么所有的工具、重构、模块化、持续部署和全面测试覆盖都没有多大帮助。

这并不是说这些方面与数据项目无关。恰恰相反。写得不好的代码可能会导致许多问题——特别是在长期可维护性方面。但是数据质量应该优先于纯技术方面。

# 测试代码和数据

软件工程师开创了单元测试和测试驱动开发。通过利用自动化，我们可以确保我们的代码始终按照预期工作。

尽管如此，对于数据项目，单元测试是不够的。没有测试你的数据，它只涵盖了等式的一部分。想象一下，您的代码设计得很完美，并且做了它需要做的事情，但是由于生成数据的源系统出现故障，您的数据突然变得损坏、陈旧或不完整。当单元测试通过时，数据测试将失败，从而提醒您数据质量问题。此外，单元测试仅在新的构建和部署时进行评估(例如，当您提交和推送对代码的更改时)，而您的数据测试在数据管道中持续运行。

因此，我们两者都需要。单元测试防止代码和程序逻辑失败，数据测试防止错误数据进入我们的系统。

如果你想学习更多关于数据测试的知识，有一个[很棒的开源 Python 库](https://greatexpectations.io/)就是为此而建的。

# 一个项目的成果

软件工程中的部署工件可以是包、API、网站、移动应用程序或容器映像。相反，数据项目处理不同的工件。数据管道的结果可以是数据仓库中的一个表、数据湖(house)中的一个拼花文件、一个自动化报告或者一个生产化的数据科学模型。

# 保持简单

很难区分数据工程师和软件工程师的职责。有些数据工程师从事大量的软件工程工作(例如，当进行“逆向 ETL”并将处理过的数据从数据仓库加载回应用程序数据库时)。同样，在很多公司，软件工程师也负责做数据处理。

尽管划分职责很有挑战性，但仔细考虑每个项目应该雇佣或指派谁是有益的。软件工程师在处理数据项目时，有时会把事情搞得过于复杂。例如，一个数据工程师通过一个 SQL 查询就能解决的问题，如果由软件工程师来解决，可能会变成一个成熟的后端 API，带有一个 [ORM](https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping) 层。构建 API 是很棒的(API 优先的方法也是如此)，但是通常我们首先实现一个简单的解决方案会更好。

我们经常听到“使用合适的工具来完成工作”的说法但我们也需要考虑具备合适技能的合适人选。同样的例子，如果用例需要每个请求的审计跟踪、不同的用户访问权限，或者需要连接到前端，那么 API 可能是更好的解决方案。但是，如果您需要定期将数据从 A 大规模移动到 B，并在移动过程中将其转换为正确的格式，数据工程师(和 SQL)可能会更适合这项任务。

# 如何在软件工程师和数据工程师之间划分任务

想象以下场景:您需要构建一个定制的分析应用程序，该应用程序应该显示特定的可视化效果，并且需要嵌入到移动和 web 应用程序中。必须整合底层数据，因为图表需要来自各种源系统的数据的组合，并且在处理这些数据时需要应用特定的业务逻辑。

可能的解决方案是什么？您可以指派一名数据工程师在您的云数据仓库中构建一个数据集市。他们将负责提取源数据，将其转换为一组指标和维度，并通过构建确保数据保持最新的流程来按计划大规模完成所有工作。

相比之下，软件工程师可以负责构建和部署一个响应式 React 应用程序，该应用程序将被正确地设计、保护和测试，并将来自数据集市的数据呈现给最终用户。理想情况下，在数据服务层和应用程序表示层之间还应该有一个 API 层。

# 减少开发过程中的摩擦

为了最小化数据层和应用层之间的摩擦，您可以利用云原生的 [GoodData 平台](https://www.gooddata.com/developers/cloud-native-community-edition/?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-sevsde)。通过[单 Docker](https://hub.docker.com/r/gooddata/gooddata-cn-ce?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-sevsde) 容器，数据工程师可以连接到数据仓库并构建语义数据模型，而软件工程师可以通过使用 [GoodData API](https://www.gooddata.com/developers/cloud-native/doc/1.0/apidocs/?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-sevsde) 直接在 React、TypeScript 或 JavaScript 应用程序中应用这些模型。云原生平台减少了数据和应用程序设计之间的摩擦，因为它允许分离关注点并将具有适当技能的适当人员分配给手头的任务:

*   数据工程师可以构建语义数据模型，以及保持数据最新的底层数据管道。
*   软件工程师可以使用相同的语义数据模型来实现用于分析和报告的前端应用。

如果你想尝试 GoodData，有一个免费的社区版本[,你可以使用一个命令安装它，这个命令将在一个 Docker 容器中运行整个平台:](https://www.gooddata.com/developers/cloud-native-community-edition/?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-sevsde)

```
docker run --name gooddata -p 3000:3000 -p 5432:5432 \
  -e LICENSE_AND_PRIVACY_POLICY_ACCEPTED=YES gooddata/gooddata-cn-ce:latest
```

在下面的文章中，您可以找到如何利用 Docker 容器构建商业智能应用程序的分步说明:

[](https://medium.com/gooddata-developers/here-is-what-happens-if-you-decouple-your-bi-stack-cd365a97ee74) [## 如果您对 BI 堆栈进行解耦，会发生以下情况

### 如何应用软件工程原理使商业智能面向未来

medium.com](https://medium.com/gooddata-developers/here-is-what-happens-if-you-decouple-your-bi-stack-cd365a97ee74) 

如果您想将其部署到您的 Kubernetes 集群， [GoodData 提供了一个掌舵图](https://www.gooddata.com/developers/cloud-native/#plans?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-sevsde)，它在一个包中包含了该架构的所有部分(GoodData UI、Postgres 数据库、分析查询引擎和使用 Redis 的智能缓存层)。

# 结论

在本文中，我们研究了软件或数据工程师可能更适合手头任务的用例。我们研究了软件工程和数据项目之间的差异，以及我们如何通过使用 GoodData 提供的基于 API 的商业智能平台来减少它们之间的摩擦。

感谢您的阅读！
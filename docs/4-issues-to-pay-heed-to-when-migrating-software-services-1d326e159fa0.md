# 迁移软件服务时需要注意的 4 个问题

> 原文：<https://betterprogramming.pub/4-issues-to-pay-heed-to-when-migrating-software-services-1d326e159fa0>

## 传统迁移

## 规划软件服务迁移，实现零停机和零事故

![](img/330dcc1a20b0becb5796cf13bd1a6c61.png)

照片由[奎特拉·登特](https://unsplash.com/@kitera?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

## 介绍

术语`Legacy Migration`在技术世界中广泛用于描述将软件栈、基础设施或软件架构从一种状态迁移到另一种状态的过程。下面列出了一些可以用术语“传统迁移”来描述的常见使用案例:

*   从整体架构迁移到微服务架构。
*   将服务从内部迁移到云。
*   将服务迁移到容器/docker/Kubernetes。

在这篇文章中，我将强调在将软件服务从一个基础设施迁移到另一个基础设施时需要考虑的几点。例如，当将服务从 Docker Swarm 迁移到 Kubernetes 时。我将强调的一些要点并不特定于这种类型的迁移，而是可以应用于任何服务迁移。

由于迁移主题非常广泛，我将在本文中只讨论四种类型的软件服务:订户、预定任务、后台作业和 web 服务器。

# 迁移规划

在开始迁移过程之前，有必要验证迁移过程的可行性，并了解其要求和后果。例如，必须了解迁移过程是否会导致停机，或者是否会影响最终客户的体验。因此，仔细规划迁移对于避免意外事件和降低迁移风险非常重要。几乎在任何迁移过程中都需要考虑以下几点，以获得应用迁移的信心。

**开发状态**:决定是在软件服务开发过程中进行迁移，还是在迁移过程完成之前停止开发，这一点很重要。确实，当开发停止时，进行迁移要容易得多；然而，这种方法阻碍了新功能的开发和服务的发展。关于这一点的决定很大程度上取决于应用迁移过程所需的复杂性和时间。

如果迁移需要不到三个月的时间(或者一个版本)，我会说阻止软件的开发并专注于迁移过程是可以的。另一方面，如果打算在一年或几年内完成迁移，唯一的选择就是在开发过程中进行迁移。在决定在实施开发和新变化的同时完成迁移之前，应该高度考虑迁移过程及其效果的知识共享和透明度。

**基础设施**:基础设施包括构成托管环境的所有组件，从物理服务器到负载平衡器和防火墙。理解托管服务的基础设施是迁移过程中的一个关键点，因为它有助于将旧的环境和基础设施映射到新的环境和基础设施。

例如，识别服务的公共和内部接口将有助于为新环境构建这些接口，最重要的是，它将有助于避免破坏这些接口。

**软件依赖和通信**:软件服务通常有依赖关系(至少是数据库服务器)，并与其他第三方服务通信。在微服务架构服务的情况下，它们在内部相互通信。软件的所有依赖和通信都需要被识别和记录，这样在迁移过程中就不会被遗忘。

例如，软件依赖性和通信信息将使我们能够构建新的基础设施，以允许所有的软件通信并避免禁止的访问错误。

**所需的源代码变更:**软件开发反模式之一是硬编码配置，或者将配置或数据与应用程序的源代码(业务逻辑)混合。

例如，硬编码数据库服务器连接字符串、文件路径、第三方连接详细信息或基本身份验证凭证和访问列表。重要的是扫描正在迁移的应用程序的源代码，搜索所有这样的情况，并通过环境变量或任何其他类型的配置重新实现它们。

**PoC:** 迁移流程的概念验证总是需要的，它可以提供关于迁移挑战和软件边界的更多见解，这是完成迁移流程所需的步骤。

例如，如果我们试图将 Ruby on Rails 应用程序迁移到 Docker 集群上运行，Poc 将有助于确定至少以下几点:

→如何建立和管理 docker 映像。

→提供编写文档的标准

→了解定义 CI/CD 管道需要什么。

→如何管理 docker 映像并将其部署到不同的环境中。

**准备新的基础设施:**根据从上述项目中收集的信息，可以请求或建立新的基础设施。构建基础架构时需要考虑的要点是:

→支持多种测试、试运行和生产环境。

→根据服务的通信和服务的依赖性考虑周围的环境。

→考虑自动化部署并为支持的环境定义 CI/CD。

**路线图:**迁移流程的路线图在迁移的成功中起着至关重要的作用，因为它提供了迁移进度所需的透明度和控制。如果没有路线图和为每个迁移里程碑定义的截止日期，迁移将很容易超出预算，而且随着时间的推移肯定也会超出预算。路线图需要注意以下几点:

→定义迁移过程的里程碑。

→定义每个里程碑中的任务和任务相关性。

→确定路线图中每项任务的执行日期。

**知识共享:**共享迁移过程带来的进展和变化应该是一项持续的活动。它应该在迁移期间定期执行，以使所有利益相关者保持一致，并避免误解和开发问题。

例如，开发人员应该意识到，服务将在多个基础设施上运行，或者在某个时候有多个实例，以确保应用程序将在这些基础设施上按预期运行。

能够简化迁移流程并降低风险的活动之一是对要迁移的服务进行分类，并为每类服务制定迁移计划。下面我描述了四种众所周知的服务类型，并分享了一些执行这些服务迁移的技巧。

# 订户/消费者

这类服务的作用是在组成应用程序堆栈的不同服务之间提供异步通信。支持这种模式的一些知名工具有 [RabbitMQ](https://www.rabbitmq.com/) 和 [Kafka](https://kafka.apache.org/) 。

例如，消费者/订户服务将从堆栈中的其他服务读取发布的消息，并尝试相应地处理这些消息。

为了简化此类服务的迁移，需要仔细考虑以下几点:

*   订户/消费者不应执行已处理的消息；相反，他们应该将消息存储为后台作业。这将有助于我们在失败的情况下使异步通信更有弹性。后台作业将能够重试消息的执行。
*   订户/消费者应该具有最小的依赖性。我真的只依赖消息代理来读取消息，并使用另一个对数据库的依赖来存储后台作业。这将有助于将服务从一个节点移动到另一个节点，甚至移动到另一个集群，只要数据库和消息代理都可用。我们不需要担心消息的执行，因为这将由后台作业来完成。
*   如果新服务能够读取消息并将它们存储为后台作业，那么这种类型的服务的迁移就成功了。
*   一旦新服务激活并处理消息，就可以终止旧的订户实例。

# 计划任务

计划任务(也称为 corn 作业)是在预定义的时间点执行的作业。

例如，导出客户资产负债表可以是一个计划任务，它将在每个月末执行一次。有两类计划任务:

*   可以同时或在不同时间多次执行的作业。不同服务之间的信息同步就是一个例子。
*   需要在特定时间只执行一次的作业。比如为支付网关导入交易。

第一类计划任务的迁移比另一类简单，因为它可以在多个实例上执行；然而，我们仍然需要确保这些计划任务的所有依赖项在新的基础设施上可用。

另一方面，在旋转新实例以运行第二类调度任务之前，我们需要确保这些任务具有以下属性:

*   使作业幂等:这意味着我们需要确保同一任务的多次执行将产生相同的结果——并且数据不会被多次执行或处理。例如，如果我们有一个从支付网关提供的文本文件中导入用户交易的 corn 作业，我们需要确保多次导入相同的文件不会导致在数据库中多次存储相同的交易。
*   防止并发执行:在某个时间点，我们需要在两个基础设施上运行 corn 作业。为此，我们需要防止这些作业同时执行相同的任务。这可以通过为作业的执行引入锁定机制来轻松实现。例如，MySQL 数据库服务器和其他数据库提供了一个内置特性，用于定义特定资源上的锁，这个特性可以用来为调度任务实现锁。

# 后台作业

后台作业是异步服务，它将从数据库(或其他来源)读取已定义的作业，并尝试执行每个已定义的作业。通常，它们还提供了在执行失败时的重试机制。

如果没有仔细考虑迁移规划中描述的要点，迁移这种类型的服务会变得非常具有挑战性和耗时。由于后台作业将尝试执行任务，因此很难限制它们的依赖性(这取决于定义的作业)。因此，重要的是要更加注意以下几点:

*   所有作业的依赖项都需要对新服务可用。
*   没有硬编码的配置、路径等。，在源代码上。
*   检查创建新服务时可能引入的冲突。例如，如果作业正在处理和生成任何特定于环境的数据，如绝对路径。
*   两端的测试、试运行和生产环境之间的差异。

简化后台作业迁移的一个技巧是对后台作业进行分类，并分别处理它们。该过程描述如下:

*   作业需要分类，每种类型必须分配到不同的队列。
*   创建新的后台作业服务，并将其配置为仅处理特定组的作业，如“migrate-jobs”
*   将作业的队列从默认队列逐一更改为“migrate-jobs”队列。
*   一旦移动的作业执行成功，就移动到下一个作业。
*   如果由于错误或错误配置修复而导致失败，请重新部署后台作业。

# Web 服务器

这种类型的服务的作用是服务于同步 web 请求。这种服务的一个例子是 web 应用程序的前端或 web 应用程序的 API 网关。由于这些服务服务于同步请求，因此迁移它们更具挑战性，原因如下:

*   服务迁移需要零停机，以避免影响最终客户体验。
*   需要对新的基础设施进行性能测试，以确保它能够处理应用程序请求。

为了能够实现和解决上述两点，在迁移此类服务时可以采取以下措施:

*   将服务部署到测试试运行环境中。
*   执行性能测试，确保新设置能够处理应用程序流量。
*   以被动模式将服务部署到生产环境中(此时没有流量被重定向到这些实例)。
*   对新实例执行冒烟测试，以确保它们可以处理 web 请求。在这里，您可以创建临时 DNS 记录来访问新服务。
*   修复由于测试而发现的任何错误，并重新部署。
*   统一旧环境和新环境中服务的负载平衡设置。这是能够在不停机的情况下迁移服务的重要一步。
*   将新环境中的一个节点添加到服务的负载平衡器。这一步将激活新基础设施上的服务。
*   监控新实例及其日志，以发现任何异常或错误。如果发现任何问题，请从负载平衡器中删除该节点，解决问题，然后重试。
*   向负载平衡器添加更多的节点，以便向新实例发送更多的流量，并保持对服务的监控。
*   最后，开始从负载平衡器中删除旧的实例，以停用它们。

所有服务迁移期间的一个关键活动是服务的测试和监控。需要为每个迁移的服务执行此活动，以确保服务始终以健康的模式运行，并确保其他服务的迁移不会影响现有的服务。

# 结论

将服务从一个状态迁移到另一个状态是一项复杂的任务，需要小心谨慎地完成，尤其是在服务可供最终客户公开访问和消费的情况下。不同类型的服务有不同的迁移技术。异步服务更容易迁移，尤其是当它们具有有限的依赖性时。

迁移过程成功的关键是理解应用程序环境、交互和依赖关系。此外，定义迁移流程里程碑和任务的路线图对于在分配的预算和时间内完成迁移流程至关重要。
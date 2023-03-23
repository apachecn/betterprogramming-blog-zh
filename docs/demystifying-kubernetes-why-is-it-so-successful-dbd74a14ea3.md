# 揭开 Kubernetes 的神秘面纱——它如此成功的 4 个原因

> 原文：<https://betterprogramming.pub/demystifying-kubernetes-why-is-it-so-successful-dbd74a14ea3>

## #3 —可扩展的功能和组件

![](img/a5310302b8abee4c8baa687ba18407d0.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [KAL 视觉](https://unsplash.com/@kalvisuals?utm_source=medium&utm_medium=referral)拍摄的照片

这篇文章是多部分系列的第一篇。在这个系列中，我想深入研究 Kubernetes，但我不打算谈论部署、Pod 或 Kubelet、KubeProxy 等技术细节。我想谈论的是，讨论 Kubernetes 为什么重要，它与现代应用程序有什么关系，它与开发人员有什么关系，以及它最初为什么成功。

让我首先定义 Kubernetes 是什么(是的，我必须这样做，不要笑)。

Kubernetes 是一个容器编排器，或者总的来说，我们认为 Kubernetes 是一个容器编排器。这是 Kubernetes 首先存在的原因(参考资料部分有一个很棒的纪录片。还没看的话就看吧)。

它被设计成一个容器编排器，并在一次 Docker 会议上与许多其他容器编排器一起发布。我们甚至可以说是当年的 docker orchestrator(想想 docker 有多占优势)。

是什么让 Kubernetes 在 Docker Swarm、Mesos、Nomad 和许多其他容器编排器中生存下来？它不仅幸存下来，还主宰了云原生时代。大众甚至以某种方式远离了许多 Docker 产品，Kubernetes 已经成为云原生世界最大的旗舰项目。

在我看来，这四个原因是 Kubernetes 成功故事的主要部分；开源并得到大型社区的支持(再次观看纪录片了解它是如何开源的)，微服务式的分布式架构，可扩展的功能和组件，处理其目标领域中的问题的优化设计。毫无疑问，它包括了云本身的特性，这就像是现代应用程序的定义。

如今，它不仅是一个容器编制器，还是一个虚拟机编制器(参见 [Kubevirt](https://kubevirt.io/) )、webassembly 编制器(参见`[crun](https://github.com/containers/crun/blob/main/docs/wasm-wasi-example.md)`、K [rustlet](https://krustlet.dev/) )、云基础设施编制器(参见 [Google Config Connector](https://cloud.google.com/config-connector/docs) 、 [Amazon Controllers](https://github.com/aws-controllers-k8s/community) 、 [Azure Service Operator](https://github.com/Azure/azure-service-operator) 、 [Crossplane](https://crossplane.io/) 、 [Cluster Api](https://cluster-api.sigs.k8s.io/) )、持续交付编制器(参见 [Flux 【T16 无服务器应用协调器(参见](https://fluxcd.io/) [Knative](https://knative.dev/docs/) 、 [Azure Container Apps](https://docs.microsoft.com/en-us/azure/container-apps/overview) 、 [Cloud Run For Anthos](https://cloud.google.com/anthos/run) )等等。

我将尝试解释 Kubernetes 成功的四个原因。

# 开源并得到大型社区的支持

容器的出现让我们可以灵活地将应用程序部署到任何有容器运行时的环境中。有了容器，我们获得了许多能力，包括可移植性、不变性等等。

有许多文章解释了它们的好处，但这完全是另一回事。在 docker 主导生态系统的容器大爆炸之后，出现了对容器编排的需求，并且出现了许多容器编排器。Kubernetes 就是其中之一。

Kubernetes 是由乔·贝达、布伦丹·伯恩斯和克雷格·麦克卢奇**发明的。谷歌的经验首先加速了它的实现。在这个引导阶段之后，Google 决定让 Kubernetes 成为一个开源项目。**

谷歌让 Kubernetes 成为开源项目的决定获得了巨大的回报。这个决定是 Kubernetes 在其他管弦乐队中脱颖而出的原因之一。CNCF 也是在 2015 年作为 Linux 基金会项目与 Kubernetes 1.0 一起成立的，以支持容器技术。库伯内特也被捐赠给 CNCF，CNCF 成为库伯内特的决策者。

如今，除了 Kubernetes 之外，CNCF 还是许多流行的开源项目的所在地。Kubernetes 是 Github 中最活跃的项目之一。聚集在 Kubernetes 周围的社区很快解决了许多问题。上面提到的项目(如 Keda、Knative、Argo、Flux 等)都是社区驱动的项目，其中大多数是由一家公司启动并捐赠给 CNCF 的。Kubernetes 的捐赠过程已经成为新项目的一个很好的榜样，其中许多项目都遵循同样的道路。

# 微服务式分布式架构

Kubernetes 没有单一的架构。它由多个组件组成。在控制平面上，它有一个 Kube 控制器管理器、Kube 调度器、Kube API 服务器和云控制器管理器。它把 ETCD 作为州商店。在 worker 节点上，它有 Kube 代理、容器运行时和`kubelet`。这些部分之间非常和谐。

Kube API 服务器是 Kubernetes 的主要组件。所有期望的状态读、写操作都由服务器处理。

当通过`kubectl`创建一个新资源时，请求也被 API 服务器接受并存储到 ETCD。然而，Kube API 服务器不是将期望的状态应用到工作节点的服务器。这是 Kube 管理员的责任。

如果资源是部署，则由部署控制器负责。部署控制器定期检查 pod 计数是否与所需状态匹配。如果资源是`statefulset`，那么`statefulset`控制器负责。

提到的控制器负责它们的资源，但是它们也不发送 pod 创建命令给工作节点(特别是给`kubelet`)。他们将命令发送到 Kube API 服务器，Kube 调度器决定哪个工作节点适合放置 pod。这种协同工作和工作分配使每个组件都具有高度的响应能力，并防止了单点故障。即使 Kube API 服务器不可用，您的工作负载仍然可以正常工作。

由于其分布式架构，许多这些组件可以根据需求轻松地替换为不同的组件。例如，如果需要的话，Kube scheduler 可以替换为另一个具有不同调度算法的调度器(参考资料中有示例调度器的链接)。Kube API 服务器不能被另一个 API 服务器替代，但是它也可以通过定制资源和聚合层进行扩展(我将在下面讨论)。

# 可扩展的功能和组件

Kubernetes 不是一个非常固执己见的工具。它的设计是相反的。不固执己见是 Kubernetes 成功的原因之一(顺便说一句，固执己见不是一件好事也不是坏事。这是一种选择，可以用它的后果来评价)。Kubernetes 可以用来构建更高级别的平台和自以为是的产品。

扩展 Kubernetes 功能的最常见方式是扩展 Kube API 服务器和 Kube 控制器。

有两种方法可以扩展 Kubernetes API:聚合层和自定义资源定义(有关详细信息，链接在下面的参考资料中)。

这两者中最流行的是 CRDs(因为它们更容易实现和维护)。CRD 与定制控制器一起形成操作员模式。定制控制器也由 Kube 控制器管理器管理，它们也订阅 Kubernetes 事件并等待关于它们资源的事件。事件发布后，他们做他们的工作。事实上，我们上面提到的很多工具(像 Argo CD、Flux、Keda、Knative、Crossplane 等等)都是操作符。

还有许多其他不同的方法来扩展 Kubernetes 的功能。例如，要协调容器之外的工作负载，可以使用其他代理。`Krustlet`是管理 web 组装流程的一个很好的例子。`Krustlet`可以在工作节点的子集上工作，`kubelet`可以在另一个子集上运行。

ETCD 是最难被类似技术取代的地方。但是即使是 ETCD 也可以被取代，正如 K3S 团队通过使用 SQLite 为他们的轻量级 Kubernetes 发行版所实现的那样。顺便说一下，取代 ETCD 不是一个简单的过程，但是大社区可以解决大问题(见 [Kine](https://github.com/k3s-io/kine) )。

如果您需要额外的功能，您还可以部署插件，如 Kubernetes dashboard、网络策略提供商和核心 DNS。您还可以通过使用 Kubernetes 部署、`daemonsets`和 statefulsets 添加其他组件来扩展集群的功能。日志传送器、度量代理、遥测代理、API 网关、服务网格和许多其他组件也可以根据您的需求与您的工作负载一起部署。

Kubernetes 动态准入控制是扩展 Kubernetes 功能的另一种方式。Kubernetes 策略(包括 pod 安全策略、RBAC 策略和网络策略)也用于扩展 Kubernetes 功能和安全强化您的集群。

关于 Kubernetes 扩展模式的参考资料有一个很棒的文档。

# 处理目标领域问题的优化设计

Kubernetes 在某种程度上是一个抽象工具。它隐藏了容器管理操作和基础设施细节。容器编排包含管理容器的生命周期、提供从集群内部和外部对容器的可访问性、在需要时提供持久卷、提供必要的配置数据、提供部署过程等等。Kubernetes 很好地定义了该领域的问题，使用了现有的最佳实践，并提供了正确的解决方案。

Kubernetes 不做过度工程，不推极限。它将许多领域留给了采纳者和社区。举几个例子，Kubernetes 没有提供可观测性堆栈的解决方案。因此，每个云供应商都可以使用自己的解决方案。即使在 CNCF 的保护伞下，也有很多可观测性的开源替代方案。这些产品也可以和 Kubernetes 一起使用。这种方法类似于消息流产品等。

# 结论

我已经谈到了 Kubernetes 的成功故事，但是众所周知，在软件行业没有放之四海而皆准的解决方案。

Kubernetes 的成功故事是真实的，但我们在 Kubernetes 的采用故事中处于什么位置？Kubernetes 是一个复杂的工具，尽管它抽象了许多复杂的基础设施细节，但对我们许多人来说很难学习。我们所有的 it 团队有必要把它掌握到炉火纯青吗？有必要让他们所有人在日常工作中使用它吗？

我的下一篇文章将是关于处理云原生空间的复杂性，我将谈论固执己见的平台、平台工程范例、作为产品概念的基础设施、应用程序开发平台等等。正如 Kelsey Hightower 在他的 tweet 中所言,“Kubernetes 是一个搭建平台的平台。这是一个更好的起点；不是终局”。

# 参考

*   [Kubernetes 纪录片第 1 部分](https://www.youtube.com/watch?v=BE77h7dmoQU)
*   [Kubernetes 纪录片第二部](https://www.youtube.com/watch?v=318elIq37PE)
*   [CNCF 维基](https://en.wikipedia.org/wiki/Cloud_Native_Computing_Foundation)
*   [Kubernetes Github 统计数据](https://github.com/kubernetes/kubernetes/graphs/commit-activity)
*   [调度火山](https://volcano.sh/en/docs/schduler_introduction/)
*   [调度器 Kube 安全调度器](https://github.com/IBM/Kube-Safe-Scheduler)
*   [库伯内特聚集层](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/apiserver-aggregation/)
*   [Kubernetes 自定义资源定义](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/)
*   [扩展模式](https://kubernetes.io/docs/concepts/extend-kubernetes/#extension-patterns)
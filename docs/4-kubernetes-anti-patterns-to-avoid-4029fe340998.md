# 要避免的 4 个 Kubernetes 反模式

> 原文：<https://betterprogramming.pub/4-kubernetes-anti-patterns-to-avoid-4029fe340998>

## 利用入口控制器撤销以前采用的反模式的影响

![](img/d007caee521aa67fd80b6ae770f9d1df.png)

图为[巴尼·尤](https://unsplash.com/@barneyyau?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

一个*反模式*是对一个通常无效的问题的高风险解决方案。乍一看，这些类型的解决方案似乎是适当和有效的。然而，从这些模式中获得的任何收益都是短期的，结果表明反模式带来的麻烦比它们的价值更大。

当一个开发人员采用一个反模式时，通常会有一种意图，那就是回来*把它做好*直到下一步，除非其他更迫切的需求阻碍了它(而且他们总是这样)。偏离最佳实践设计模式会产生技术债务，这迟早是要付出的——或者是重构的时间和精力，或者是由于系统不可用而产生的金钱。

反模式存在于 Kubernetes 世界中。Container orchestrator 解决方案，尤其是 Kubernetes，在使用时考虑到了云原生工作负载，在采用特定的设计模式时效果最佳。

在本文中，我们将介绍一些常见的 Kubernetes 反模式，一些可以使用的设计模式，以及使用入口控制器如何帮助实现推荐的设计模式。

# 反模式示例

云原生工作负载——尤其是那些运行在 Kubernetes 上的工作负载——假定了弹性(例如，水平扩展)、容错和高度异构的环境，以便于维护和调试。不小心开发新的分布式系统或将遗留系统迁移到 Kubernetes 会导致几种反模式。以下是您应该了解的一些反模式:

# 1.缺乏健康检查

运行状况检查允许您验证服务的状态。它有助于评估关键信息，如服务可用性、系统指标或可用的数据库连接。服务可以通过健康端点如`healthz`、`livez`或`readyz`来报告其状态。

Kubernetes 支持[容器探测器](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)——即`livenessProbe`、`readinessProbe`和`startupProbe`——允许监控服务并在探测成功时采取行动。没有运行状况监控的服务无法利用 orchestrator 解决方案自动提供的大量功能。

# 2.不使用蓝色、绿色或淡黄色部署模型

大多数应用程序所有者更喜欢零宕机的变更部署。对于大多数关键任务应用程序来说，这是必需的。Kubernetes 允许您定义`Recreate`和`RollingUpdate`部署策略。`Recreate`将在创建新的 pod 之前杀死所有的 pod，而`RollingUpdate`将以滚动方式更新 pod，并允许配置`maxUnavailable`和`maxSurge`来控制进程。

虽然这些[部署策略](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)可以服务于许多用例，但是它们也有局限性。例如，`Recreate`会导致停机，而`RollingUpdate`会使回滚更加困难。这些方法都不允许对服务的新版本进行快速试验和反馈。

蓝/绿部署是一种创建服务副本的部署模型，旧版本是蓝色的，新版本是绿色的，两个服务并行运行。一旦您确信新版本(绿色)已准备好发布，您就可以将所有生产流量路由到新版本，同时保持旧版本(蓝色)正常运行。如果出现问题，您可以快速将流量路由回以前的版本，防止用户不满或停机。一段时间后，当一切都按预期运行时，您就可以删除旧版本的服务了。

Canary deployment 是一种仅将流量路由到用户子集的新服务的技术。这种模式允许在生产中引入新版本的服务，同时密切监视其行为。如果成功，您可以向更多的用户公开新版本，最终将每个人都迁移到新的代码版本。

这两种技术在云原生环境中都是必不可少的，因为它们提高了服务的可靠性，并支持快速试验和开发。

# 3.不使用断路器

运行在 Kubernetes 集群上的服务通过远程调用相互通信。因为这些服务在不同的机器上运行并不少见，所以这些远程调用更容易失败或没有响应。这可能会导致级联故障等问题。

在电子学中，断路器是一种用来保护电路免受损坏的开关。通过断路器的电流过大会导致电路断开，从而防止过载或短路。断路器的目标是在识别故障后防止故障。类似地，在软件中，断路器监视服务的故障，并且一旦被识别，就阻止对它的进一步调用。这允许系统处理故障并将请求路由到同一服务的健康实例。

如果没有这样的机制，运行分布式的、基于服务的应用程序的 Kubernetes 集群将很容易出现故障。

# 4.没有收集足够的指标

可观察性是理解系统行为的关键，有效的可观察性依赖于度量标准的正确收集。当您想知道:您的服务在做什么，它们执行得有多好，为什么出错，以及可能的话，如何调试问题时，度量提供了信息片段。在复杂的分布式系统中，度量标准与其他形式的可观察性(如跟踪)相结合，允许您通过单一窗口全面地了解您的系统。

缺少关键指标将严重限制您理解服务性能的能力，以及它们是否在期望的水平上运行。随着系统复杂性的增加，有必要从更多的端点收集更多的数据点。

# 使用入口控制器

[孔入口控制器(KIC)](https://docs.konghq.com/kubernetes-ingress-controller/) 是 Kubernetes 的一个[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)实现。它支持路由配置、健康检查和负载平衡，并支持各种提供高级功能的[插件](https://docs.konghq.com/hub/)。

KIC 可以帮助解决我们上面讨论的反模式。

## 健康检查

KIC 可以配置为[被动健康检查](https://docs.konghq.com/kubernetes-ingress-controller/2.0.x/guides/configuring-health-checks/#setup-passive-health-checking)和[主动健康检查](https://docs.konghq.com/kubernetes-ingress-controller/2.0.x/guides/configuring-health-checks/#setup-active-health-checking)。被动健康检查将根据每个请求监视您的服务，并且在一定数量的失败后，将把请求短路到失败的 pod。主动运行状况检查将按照预定义的时间间隔定期监控服务，将出现故障的 pod 标记为不可用。

被动和主动运行状况检查将通过运行定期运行状况检查和采取主动措施来提高系统的可靠性。KIC 将使用运行状况检查的信息，高效地将请求路由到运行状况良好的服务副本。

## 蓝色、绿色或淡黄色部署

代码审查和测试只能让您相信您的服务将正确工作。然而，你不能测试每一个用例或边界条件。此外，许多 bug 只有在代码处于野生状态，并且您的应用程序开始接受实际的用户流量时才会被发现。

蓝/绿和淡黄色部署通过实现快速回滚和减少意外结果的影响，降低了部署风险。

通过在 KIC 后部署服务的旧版本和新版本，您可以轻松地将流量路由到新版本，并在必要时回滚到以前的版本。同样，使用[金丝雀发布插件](https://docs.konghq.com/hub/kong-inc/canary/)可以让你以分阶段的方式推出新的变化。

## 断路器

您可以通过检查来配置 KIC [，以监控服务性能。根据结果，它可以将不健康或无响应的服务副本标记为不可用，从而防止任何进一步的请求被路由到这些服务副本。相反，后续请求将被路由到服务的健康副本。修复后，原始服务副本可以重新联机。](https://docs.konghq.com/enterprise/2.6.x/health-checks-circuit-breakers/#passive-health-checks-circuit-breakers)

## 韵律学

KIC [可以轻松地将](https://docs.konghq.com/kubernetes-ingress-controller/2.0.x/guides/prometheus-grafana/)与[普罗米修斯](https://prometheus.io/)和[格拉法纳](https://grafana.com/)这两个行业标准监控解决方案集成在一起，让您了解您的服务如何响应流量。此外，访问这些指标不需要任何服务工具。Prometheus metrics 可用于服务请求和配置更新。

# 结论

重新设计整个 Kubernetes 解决方案非常耗时，而且并不总是可行的选择。通过利用入口控制器，您将能够通过用符合 Kubernetes 最佳实践设计模式的实现来替换先前采用的反模式，从而消除它们的影响。
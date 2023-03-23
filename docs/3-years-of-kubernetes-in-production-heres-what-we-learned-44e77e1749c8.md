# 3 年的 Kubernetes 生产——以下是我们学到的东西

> 原文：<https://betterprogramming.pub/3-years-of-kubernetes-in-production-heres-what-we-learned-44e77e1749c8>

## 从我们的 Kubernetes 期刊中获得的关键信息

![](img/299c3f10fe0f52938d481c3d375c64d9.png)

杰西卡·刘易斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我们在 2017 年开始构建我们的第一个 Kubernetes 集群，版本 1.9.4。我们有两个集群，一个运行在裸机 RHEL 虚拟机上，另一个运行在 AWS EC2 上。

如今，我们的 Kubernetes 基础架构设备群由分布在多个数据中心的 400 多台虚拟机组成。该平台托管高度可用的关键任务软件应用和系统，以管理拥有近 400 万台活动设备的大型实时网络。

Kubernetes 最终让我们的生活变得更容易，但这是一个艰难的旅程，一个范式的转变。不仅仅是我们的技能和工具，还有我们的设计和思维都发生了彻底的转变。我们不得不采用多种新技术，并大规模投资来升级和提高我们的团队和基础设施。

回顾过去，在生产中运行 Kubernetes 三年后，这里是我们的日志中的关键经验。

[](https://medium.com/digital-diplomacy/how-a-faulty-visa-system-is-killing-the-technology-market-adcd8588d880) [## 一个有缺陷的签证系统是如何扼杀技术市场的

### 工作签证上的移民软件工程师的困境

medium.com](https://medium.com/digital-diplomacy/how-a-faulty-visa-system-is-killing-the-technology-market-adcd8588d880) 

# 1.Java 应用程序的奇特案例

当谈到微服务和容器化时，工程师们倾向于避免使用 Java，主要是因为它臭名昭著的内存管理。然而，现在情况发生了变化，Java 的容器兼容性在过去几年里有所提高。毕竟，像`Apache Kafka`和`Elasticsearch`这样无处不在的系统都运行在 Java 上。

回到 2017-2018 年，我们有几个运行在 Java 版本 8 上的应用程序。这些人通常很难理解像 Docker 这样的容器环境，并且因为堆内存问题和不寻常的垃圾收集趋势而崩溃。我们了解到这些都是由于 JVM 不能支持 Linux `cgroups`和`namespaces`造成的，而 Linux 和 T3 是容器化技术的核心。

然而，从那以后，Oracle 一直在不断改进 Java 在容器领域的兼容性。甚至 Java 8 的后续补丁也引入了实验性的 JVM 标志来解决这些问题，`XX:+UnlockExperimentalVMOptions`和`XX:+UseCGroupMemoryLimitForHeap`

但是，尽管有了所有的改进，不可否认的是，与 Python 或 Go 等同行相比，Java 仍然有占用内存和启动时间慢的坏名声。它主要是由 JVM 的内存管理和类加载器引起的。

今天，如果我们*有*选择 Java，我们保证它是版本 11 或以上。我们的 Kubernetes 内存限制在 JVM 最大堆内存(`-Xmx`)的基础上设置为 1GB，用于预留空间。也就是说，如果 JVM 使用 8GB 的堆内存，我们的 Kubernetes 应用程序的资源限制将是 9GB。有了它，生活变得更加美好。

[](https://medium.com/better-programming/why-java-is-dying-b02b5fd44db9) [## 为什么 Java 正在消亡

### Java 的未来会怎样？

medium.com](https://medium.com/better-programming/why-java-is-dying-b02b5fd44db9) 

# 2.Kubernetes 生命周期升级

Kubernetes 的生命周期管理，比如升级或增强，非常麻烦，尤其是如果你已经在[裸机或虚拟机](https://platform9.com/blog/where-to-install-kubernetes-bare-metal-vs-vms-vs-cloud/)上构建了自己的集群。对于升级，我们认识到最简单的方法是使用最新版本构建新的集群，并将工作负载从旧的转移到新的。投入就地节点升级的努力和规划是不值得的。

Kubernetes 有多个需要升级的移动部件。从 Docker 到 CNI 插件，如印花布或法兰绒，你需要仔细地把它们拼凑在一起才能工作。虽然像 Kubespray、Kubeone、Kops 和 Kubeaws 这样的项目使它变得更容易，但它们都有缺点。

我们在 RHEL 虚拟机上使用 Kubespray 构建了我们的集群。Kubespray 很棒，它有构建、添加和删除新节点、升级版本的剧本，以及我们在生产中操作 Kubernetes 所需的几乎所有东西。但是，升级手册附带了一个免责声明，阻止我们跳过哪怕是很小的版本。因此，必须通过所有中间版本才能到达目标版本。

要点是，如果您计划使用 Kubernetes 或者已经在使用，请考虑生命周期活动以及您的解决方案如何解决这个问题。构建和运行集群相对来说更容易，但是生命周期维护是一个全新的游戏，有多个移动部分。

# 3.构建和部署

准备好重新设计您的整个构建和部署管道。对于 Kubernetes 世界，我们的构建过程和部署必须经历一个完整的转变。不仅 Jenkins pipelines 进行了大量的重组，还使用了 helm 等新工具，制定了新的 git 流程和构建策略，标记了 docker 图像，并对 Helm 部署图进行了版本控制。

您将需要一个策略来维护不仅仅是代码，还有 Kubernetes 部署文件、Docker 文件、Docker 图像、舵图，并设计一种方法将它们链接在一起。

经过几次反复，我们决定采用以下设计。

*   应用程序代码和它的舵图驻留在单独的 git 存储库中。这使得我们可以分别对它们进行版本控制。([语义版本化](https://semver.org/))
*   然后，我们将图表版本与应用程序版本一起保存，并使用它来跟踪发布。所以比如`app-1.2.0`和`charts-1.1.0`一起部署。如果只有舵值文件改变，那么只有图表的补丁版本会改变。(例如从`1.1.0`到`1.1.1`)。所有这些版本都是由每个存储库中的发行说明规定的，`RELEASE.txt`。
*   像 Apache Kafka 或 Redis 这样的系统应用程序的代码不是我们构建或修改的，它们的工作方式不同。也就是说，我们没有两个 git 存储库，因为 Docker 标签只是 Helm chart 版本控制的一部分。如果我们为了升级而更改了 docker 标签，我们会在图表的标签中增加主要版本。

# 4.活跃性和就绪性调查(双刃剑)

Kubernetes 的活跃性和就绪性探测是自主解决系统问题的优秀特性。它们可以在出现故障时重启容器，并转移不健康实例的流量。但是在某些故障条件下，这些探测可能成为一把双刃剑，影响应用程序的启动和恢复，特别是像消息传递平台或数据库这样的有状态应用程序。

我们的卡夫卡体系就是这种情况的受害者。我们用一个`replicationFactor of 3` 和一个`minInSyncReplica of 2.`运行了一个`3 Broker 3 Zookeeper`状态集。当 Kafka 在意外的系统故障或崩溃后启动时，就会出现这个问题。这导致它在启动时运行额外的脚本来修复损坏的索引，根据严重性，这需要 10 到 30 分钟。由于这一额外的时间，活动性探针将不断失败，向 Kafka 发出终止信号以重新启动。这阻止了卡夫卡去修正指数，一起开始。

唯一的解决方案是在活动性探测器设置中配置`**initialDelaySeconds**`，以在容器启动后延迟探测器评估。但问题是，当然，这很难确定。有些恢复甚至需要一个小时，我们需要提供足够的空间来解决这个问题。但是你越增加`**initialDelaySeconds**`，你的恢复能力就越慢，因为在启动失败时，Kubernetes 需要更长的时间来重新启动你的容器。

因此，折中的办法是评估`**initialDelaySeconds**`字段的值，以便它更好地平衡您在 Kubernetes 中寻求的弹性和应用程序在所有故障条件下(磁盘故障、网络故障、系统崩溃等)成功启动所需的时间。)

> ***更新*** *:如果您使用的是最近几个最新版本，* [*Kubernetes 引入了第三种探针类型，称为“启动探针”，来解决这个问题*](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) *。从* `*alpha from 1.16*` *到* `*beta from 1.18*` *都有。*
> 
> *启动探测器禁用就绪性和活动性检查，直到容器启动，确保应用程序的启动不被中断。*

# 5.暴露外部 IP

我们了解到，使用静态外部 IP 公开服务会对内核的连接跟踪机制产生巨大的影响。除非计划周密，否则它会大规模崩溃。

我们的集群运行在`Calico for CNI` 和`BGP`上，作为我们在 Kubernetes 内部的路由协议，并与边缘路由器对等。对于 Kubeproxy，我们使用`IP Tables`模式。我们在通过外部 IP 公开的 Kubernetes 中托管大量服务，每天处理数百万个连接。由于来自软件定义网络的所有 SNAT 和伪装，Kubernetes 需要一种机制来跟踪所有这些逻辑流。为了实现这一点，它使用内核的`Conntrack and netfilter`工具来管理这些到静态 IP 的外部连接，静态 IP 然后转换为内部服务 IP，再转换为您的 pod IP。这都是通过`conntrack`表和 IP 表完成的。

然而，这张表有其局限性。一旦达到极限，您的 Kubernetes 集群(底层的操作系统内核)将不再能够接受新的连接。在 RHEL，你可以这样检查。

```
$  sysctl net.netfilter.nf_conntrack_count net.netfilter.nf_conntrack_maxnet.netfilter.nf_conntrack_count = 167012
net.netfilter.nf_conntrack_max = 262144
```

解决这一问题的一些方法是使用边缘路由器对等多个节点，这样到静态 IP 的传入连接就会分散到整个集群。因此，如果您的集群有一大群机器，累积起来，您可能有一个大的`conntrack`表来处理大量的传入连接。

当我们在 2017 年开始时，这完全让我们感到困惑，但最近，Calico 在 2019 年发表了一项关于这一点的详细研究，题为“[为什么 conntrack 不再是你的朋友](https://www.projectcalico.org/when-linux-conntrack-is-no-longer-your-friend/)”

[](https://medium.com/better-programming/4-simple-kubernetes-terminal-customizations-to-boost-your-productivity-deda60a19924) [## 4 个简单的 Kubernetes 终端定制来提高您的生产力

### 这是我在生产中用于管理大规模 Kubernetes 集群的工具

medium.com](https://medium.com/better-programming/4-simple-kubernetes-terminal-customizations-to-boost-your-productivity-deda60a19924) 

# 你真的需要 Kubernetes 吗？

三年过去了，我们仍然每天都在发现和学习新的东西。这是一个复杂的平台，有其自身的一系列挑战，特别是构建和维护环境的开销。它将改变你的设计、思维和架构，并要求你的团队提升技能和规模以适应这种转变。

然而，如果你在云上，并且能够使用 Kubernetes 作为“服务”，它可以减轻你的大部分平台维护开销，比如“我如何扩展我的内部网络 CIDR？”或者“我如何升级我的 Kubernetes 版本？”

今天，我们意识到你需要问自己的第一个问题是“你绝对需要 Kubernetes 吗？”这有助于评估你的问题，以及 Kubernetes 解决问题的重要程度。

Kubernetes 转型并不便宜。您为它支付的价格必须真正证明“您的”用例以及它如何利用平台。如果是这样，那么 Kubernetes 可以极大地提高你的生产力。

> 记住，为了技术而技术是没有意义的。
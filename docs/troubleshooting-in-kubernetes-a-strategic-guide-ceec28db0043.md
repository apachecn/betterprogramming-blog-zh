# Kubernetes 故障排除:战略指南

> 原文：<https://betterprogramming.pub/troubleshooting-in-kubernetes-a-strategic-guide-ceec28db0043>

## 当出现问题时，如何在 Kubernetes 中调试问题

![](img/3f9ef221ac7c672292fb749c7930bcac.png)

Tobias Tullius 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Kubernetes 可能是一个棘手的平台，尤其是在调试和故障排除问题时。这种困难大部分来自于缺乏详细的错误消息和系统的复杂性。此外，容器编排流程中移动部分的绝对数量——以及表示该流程的少数几个状态——增加了挑战。例如，正如你将看到的，至少有六个合理的原因可以解释为什么`Pod`会停留在`ContainerCreating`或`CrashLoppBackOff`状态。

[在严格使用 Kubernetes 三年多之后](https://medium.com/better-programming/3-years-of-kubernetes-in-production-heres-what-we-learned-44e77e1749c8)，我们遇到了一长串同样微妙和复杂的问题，尽管其中大多数主要属于以下三种情况:

1.  吊舱卡在`ContainerCreating`状态
2.  `CrashLoopBackOff`和周期性重启
3.  网络连接问题

你会发现，每一个问题的背后都有许多原因，这篇文章将帮助你理解这些原因以及背后的*为什么*。我们的目标不是给你一个命令或工具的列表(只能解决一组**)，而是提升你的理解，帮助你建立一种直觉，当 Kubernetes 中出现问题时，这种直觉可以帮助你解决任何问题。**

**对于大多数 K8s 问题，首先要查看的是事件日志(`kubectl get events -n <NAMESPACE>`):它捕获了 Kubernetes 编排中每个步骤的细节，这些细节对于任何诊断都是关键的。**

# **1.荚卡在`ContainerCreating State`**

**理解`ContainerCreating`——或任何 pod 状态的主要概念是敏锐地知道它在 k8s 编排管道中的位置。这些信息将帮助我们识别和排除堆栈中的其他组件。例如，`ContainerCreating`意味着`kube-scheduler`已经为容器分配了一个工作节点，并指示`Docker`守护进程启动工作负载。但是，请注意，在此阶段并未调配网络，也就是说，工作负载没有 IP 地址。**

**让我们来看看你的`Pod`可能会陷入`ContainerCreating`阶段的一些常见原因:**

## **1.1 IP 地址分配失败**

**`KubeControlPlane`从 CNI(比如说 Calico)请求 IP，当它不能分配 IP 时，`ControlPlane`将只是无休止地等待一个 IP，表面上没有日志跟踪或错误——从您的`kubectl`终端也看不到任何东西。不过，这只是`kubelet`**/`system`**日志中捕获的。******

******原因:******

1.  ****在您的`CNI`中配置的`IPPool`中缺少 IPs****
2.  ****`kubelet`和您的`CNI`之间的通信错误****
3.  ****`CNI`中的配置错误****

## ****1.2 安装配置映射失败****

****`Configmaps`包含在运行时挂载到容器的(虚拟化)文件系统上的文件。业务流程中的这一阶段记录在事件日志中。****

******原因:******

1.  ****节点上的`/var/lib/docker`填满:这将阻止节点上的`Docker`守护进程正常运行。****
2.  ****从吊舱部署引用时`Configmap`的名称中的错误/错别字。****

## ****1.3 未能申请 PVs****

****依赖于状态信息的容器——如数据库或消息平台——如果无法通过`PersistentVolumeClaim`挂载它们的`PersistentVolume`,也会在`ContainerCreating`阶段被卡住。这可以从事件日志中看出，更详细的信息可以在系统日志中找到。****

******原因:******

1.  ****Kubernetes 的`CSI`插件与云提供商(Vsphere、AWS EBS 等)之间的通信出错。)****
2.  ****当工作负载在集群中的节点间移动时，`ControlPlane`执行`attach`和`detach`例程来移动磁盘——由于装载和卸载操作超时，此过程有时会花费更长时间。****

# ****`2\. CrashLoopBackOff & Periodic Restarts`****

****`CrashLoopBackOff`主要归因于容器代码或软件本身的故障。当容器中的`entrypoint`命令被触发后错误退出时，就会出现这种情况。这种故障可能有多种原因，但通常可以归结为以下原因:****

## ****2.1 启动脚本或 InitContainers 中的错误****

****除了容器本身的软件错误之外，注入环境变量、挂载配置、机密或引用其他 K8s 对象的外部故障也可能导致启动失败，从而导致`CrashLoopBackOff`。****

## ****2.2 超出内存限制****

****如果容器在启动时——或者在其生命周期的后期——超过了`Pod`设置的内存限制，Kubernetes 将向正在运行的进程发出一个中断信号。如果这种情况经常发生，您可能会看到`Pod`重启计数逐渐增加。****

## ****2.3 磁盘/存储空间故障****

****节点上的容器可能会因为两个区域缺少存储空间而出现故障:****

1.  ****`Pod`的`PersistentVolume`本身，它可以影响容器进程****
2.  ****调度`Pod`的 worker 节点上的`/var/lib/docker` (Docker 的`OverlayFS`)****

## ****2.4 活动性探测****

****Kubernetes 中的活动性探测机制允许`ControlPlane`自动重启失败的容器以获得弹性。但是，与此同时，如果配置过于激进，或者只是错误配置而没有考虑延迟恢复启动，这些探测器可能会影响稳定性:****

1.  ******咄咄逼人的** `**Liveliness**` **探测器**会迫使 Kubernetes 执行频繁的检查。但是如果容器正在进行一个关键的 GC 事件(对于 Java 来说)，那么活动性检查很可能会失败——最终会重启容器。****
2.  ******变更/延迟恢复启动:**有时，您的容器的启动可能会因为试图修复崩溃故障而延长。这经常出现在有状态的应用程序中，这些应用程序可能会运行恢复例程来处理诸如文件系统损坏之类的不利情况。因此，如果您的活动性探测设置中没有足够的余地来考虑这一点，您的容器可能会陷入无休止的重启循环中。****

# ****4.网络连接问题****

****Kubernetes 的软件定义网络非常复杂，在调试连接问题时可能会特别令人生畏。例如，一个组件的故障，尤其是在网络堆栈中，可能会加剧集群中的连接问题。最后，它会让你关注多个地方，因此一个好的方法是从战略性地关注关键地方开始，缩小你的目标。****

****当谈到调试内部 Kubernetes 网络问题时，以下是需要磨练的基本领域。****

## ****4.1 Kube-代理和 IP 表****

****`kube-proxy`的主要职责是促进`Service IP`与其后端(`Pod IP`)端点之间的通信。因此，如果您的`Pod`位于一个难以连接到服务 IP 的节点上——出现连接超时或连接被拒绝的错误消息——那么简单地重启`kube-proxy`在大多数情况下可以解决问题，因为就像任何其他工作负载一样，您的`kube-proxy`容器可能在该节点上崩溃或失败。****

****内核`IP table`是服务和后端 pod 之间路由流量的核心组件，包括通过`node-ports`的通信。`Kube-proxy`使用 IP 表来设置规则，以促进负载平衡和伪装，因此，当您的`Pod`可以到达其他`Pod`IP 而不能到达`Service`IP 时，IP 表是需要查看的关键区域之一。****

## ****4.2 Kube-DNS****

****在名称解析失败的情况下，首先要查看 K8s 集群中设置的内部 DNS(默认 DNS 服务是`kubedns`)——并确保该服务可以通过其`Cluster`或`Pod` IP 到达。****

****其次，确保您的 pod 和 worker 节点中的`resolv.conf`包含必要的 DNS 域和用于查找的名称服务器。****

## ****4.3 Conntrack****

****`Conntrack`中的问题会导致连接中断和网络流量不一致。简而言之，`conntrack`是 Linux 内核维护系统中连接状态和逻辑流的方式。也就是说，如果您的应用程序公开了一个外部 IP，并且有数百万个连接到这个 IP，那么这些连接和逻辑流的状态将在内核的`conntrack`表中被跟踪。足够合理的是，这些`conntrack`表带有硬限制，当这些限制被突破时，网络就会出错。****

****在 RHEL 上，`conntrack`连接限制可以这样检查:****

```
****$  sysctl net.netfilter.nf_conntrack_count net.netfilter.nf_conntrack_maxnet.netfilter.nf_conntrack_count = 167012
net.netfilter.nf_conntrack_max = 262144****
```

## ****4.5 CNI(容器网络接口)和路由表****

****当您的 CNI `daemonset`(比如说`calico`)在任何节点崩溃时，它会中断 K8s 集群中的路由和网络连接，通常局限于受影响的节点。****

****与`kube-proxy`类似，重启该节点上的`calico`容器或`calico`控制器可以解决问题。就像集群中的任何其他工作负载一样，CNI `Pods`也容易受到中断的影响。****

# ****摘要****

****Kubernetes 是一个复杂的平台，调试一个问题绝非易事。例如，我们已经看到，像`ContainerCreating`这样的一个冻结状态是如何与几种不同的原因联系在一起的——从 IP 分配失败到磁盘装载问题。它主要源于平台中大量的移动部件，以及它们在事物的大计划中是如何相互连接的。****

****我们研究了以下三个类别，以及众多问题是如何归因于它们的:****

1.  ****pod 卡在`ContainerCreating`状态****
2.  ****`CrashLoopBackOff`周期性重启****
3.  ****网络连接问题****

****它让我们对内部工作有了深入的了解，并帮助我们建立直觉，知道当事情出错时该去哪里找；在库伯内特人的世界里，他们经常这样做。****
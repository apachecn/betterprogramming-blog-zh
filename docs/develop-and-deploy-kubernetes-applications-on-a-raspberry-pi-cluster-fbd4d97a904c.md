# 在 Raspberry Pi 集群上开发和部署 Kubernetes 应用程序

> 原文：<https://betterprogramming.pub/develop-and-deploy-kubernetes-applications-on-a-raspberry-pi-cluster-fbd4d97a904c>

## **如何在 Pi 集群上创建全功能的 Kubernetes 应用**

![](img/80db57c53948a28c0b4ba3002a488a42.png)

我的树莓派集群

本文是我在 Raspberry Pi 集群上开发和部署 Kubernetes 和一个示例微服务风格应用程序的系列文章的第一篇。

本系列的部分内容旨在成为在 Raspberry Pi 集群上开发和部署 Kubernetes 应用程序的元资源。在 Raspberry Pi 上部署操作系统等常见任务在其他资源中也很容易找到。我不会在这个系列中复制信息。本系列将提供其他资料中没有的细节。

本系列中没有在 Raspberry Pi 上执行的任务(例如初始设置和各种管理任务)是在运行 Catalina 的 MacBook Pro 上执行的。

# 我为什么要这么做？

我一直在寻找一个有趣的项目来做。我的日常工作就是在 Kubernetes 集群上开发和部署应用程序。然而，我只是一个更大团队的一部分。虽然团队熟悉应用程序中使用的所有技术，但我们每个人都倾向于承担更专业的角色。特别是，我的角色侧重于应用程序。我觉得我可以“驾驭”Kubernetes，因为我知道如何完成与应用程序的管理和故障排除相关的某些操作。也就是说，我当然没有我所说的对 Kubernetes 的深入了解。

为了更加熟悉 Kubernetes，我决定从头开始创建一个 Kubernetes 集群。由于我无法访问更大的计算机集群，并且我对 Raspberry Pis 有兴趣，所以我决定使用 Raspberry Pis 创建我的集群。

为了进一步磨练我的 Kubernetes 技能，我需要创建一个由多个微服务组成的应用程序，在集群上运行。此外，我工作过的所有应用程序都必须解决上市时间和最佳实践之间的权衡，例如，将日志记录到应用程序中，还是稍后再连接上。因此，为了让事情变得更有趣，我决定创建一个相对简单但现实的应用程序，它可以展示我认为的最佳实践。

# 系列文章

当前可用的文章有:

*   [设置一个 Raspberry Pi 集群](https://medium.com/better-programming/setup-a-raspberry-pi-cluster-ff484a1c6be9):描述如何设置和配置一组 Raspberry Pi 到一个集群中。这包括定义群集中的网络以及到外部网络的网络。
*   I [在 Raspberry Pi 集群上安装 Kubernetes](https://medium.com/better-programming/install-kubernetes-on-a-raspberry-pi-cluster-49ad9a762d08):这包括安装&配置 Kubernetes 以及构建和部署一个简单的服务。后一步涉及一个最小的应用程序，可用于测试 Kubernetes 集群以及对应用程序的简单外部网络访问。
*   [在 Raspberry Pi 集群上安装 Kubernetes Ingress](https://medium.com/@RichYoungkin/install-kubernetes-ingress-on-a-raspberry-pi-cluster-e8d5086c5009):Ingress 是一种支持外部访问集群内部服务的有用方式。在这篇文章中，我将描述如何安装、配置和测试作为入口控制器 [Traefik](https://docs.traefik.io/) 。本文还包括安装和配置 Helm。头盔将用于部署 Traefik。Helm 也将在本系列的后续文章中使用。
*   [Kubernetes 在 Raspberry Pi 集群上的应用程序监控](https://medium.com/better-programming/kubernetes-application-monitoring-on-a-raspberry-pi-cluster-fa8f2762b00c) —安装 Prometheus、Grafana 和 EFK(elastic search/Fluentd/ki Bana)作为监控堆栈。还包括在集群上安装 Telegraf，以监控集群上主机的机器级别指标。例如 CPU 负载和温度、磁盘使用
*   [如何在一个树莓 Pi 上安装 Maria db](https://medium.com/better-programming/how-to-install-mysql-on-a-raspberry-pi-ad3f69b4a094)—标题说明了一切。需要注意的一点是，MariaDB 是 MySQL 的替代产品

下面列出了未来文章中涉及的主题。

*   开发应用程序。这个系列的高潮。这将利用上面介绍的所有准备步骤，但也将展示如何安装、升级和管理这样一个应用程序的完整画面。这个应用程序可以部署到我们将在本系列中构建的 Raspberry Pi 集群，但是它也可以部署到任何 Kubernetes 集群。[目前在 GitHub 上已经有了](https://github.com/youngkin/mockvideo/tree/master)。

# 更多细节

虽然本系列的重点是 Kubernetes 上的一个 Raspberry Pi 集群，但是许多与应用程序相关的内容可以应用于任何 Kubernetes 环境。例如:

*   使用 [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) 进行集群监控。
*   使用 [Grafana](https://grafana.com/) 实现指标可视化。
*   使用 [Traefik](https://docs.traefik.io/) 的集群[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)。
*   用[掌舵](https://github.com/helm/helm)部署和升级应用。
*   使用 [Prometheus](https://prometheus.io/docs/introduction/overview/) 检测应用程序。
*   使用 [MySQL](https://www.mysql.com/) 的应用数据库支持。
*   使用一个 [ELK](https://www.elastic.co/what-is/elk-stack) (实际上是 EFK)堆栈进行日志捕获、搜索和可视化。
*   应用程序开发最佳实践。

# 最后

我概述了一个雄心勃勃的计划。如上所述，并不是所有上述主题都写好了。随着我在这项工作中的进展，我会添加一些文章来描述如何完成每一步，以及一些细节背后的原因。

我希望在这次旅程中你会和我在一起。欢迎评论、建议和问题。我们可以一起学习。
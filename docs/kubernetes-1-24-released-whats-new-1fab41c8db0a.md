# Kubernetes 1.24 发布:有什么新内容？

> 原文：<https://betterprogramming.pub/kubernetes-1-24-released-whats-new-1fab41c8db0a>

## 浏览四个主要功能

![](img/22cc8ebbc3b7795b88fd4657211fb576.png)

由[菲利斯](https://unsplash.com/@miniminion?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

最新的 Kubernetes 版本 1.24“Stargazer”为自动化容器化应用程序的部署、伸缩和管理的开源系统带来了很多变化。

14 项功能在测试阶段后永久可用，15 项创新处于测试阶段，13 项改进正在测试阶段。

此外，有两个功能被弃用，另外两个功能被永久删除。此版本的主要主题包括:

*   [将 Dockershim 从 kubelet 移除](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#dockershim-removed-from-kubelet)
*   [默认情况下，测试版 API 不再有效](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#beta-apis-off-by-default)
*   [分享发布神器](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#signing-release-artifacts)
*   [OpenAPI v3](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#openapi-v3)
*   [存储容量跟踪和卷扩展](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#storage-capacity-and-volume-expansion-are-generally-available)
*   [测试中不再出现非优先优先级](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#nonpreemptingpriority-to-stable)
*   [存储插件的迁移](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#storage-plugin-migration)
*   [gRPC 探针功能](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#grpc-probes-graduate-to-beta)和 [Kubelet 凭证提供者](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#kubelet-credential-provider-graduates-to-beta)处于测试阶段。
*   [在将 IP 地址与服务相关联时避免重复](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#avoiding-collisions-in-ip-allocation-to-services)
*   [从库伯莱移除动态库伯莱配置](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#dynamic-kubelet-configuration-is-removed-from-the-kubelet)

# 观星者

Kubernetes 开发团队为 1.24 版本起了一个特别的名字:[观星者](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#release-theme-and-logo)。

根据它自己的说法，这个名字旨在指凝视星星可以激发科学探究和创造力，并有助于在未知领域导航。

该标志也是富有想象力的设计:它显示了一个风格化的星空前面的望远镜。

显示的星座是昴宿星，在希腊神话中也被称为七姐妹。它向 Kubernetes 最初的内部项目名称致敬:Project 7。

![](img/47fcb667e21aeeb8b05c79a6fe2c1291.png)

Kubernetes 版本 1.24 Stargazer 的标志( [Kubernetes](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#release-theme-and-logo) )

# 码头工人被从库伯莱除名

Dockershim 是 Kubelet 和 Docker 守护进程之间的 CRI 兼容层。在 1.20 版本中被弃用后， [Dockershim 组件现在已经从 kubelet](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#dockershim-removed-from-kubelet) 中永久删除。

从版本 1.24 开始，要么使用其他受支持的运行时(例如 containerd 或 CRI-O)，要么如果您仍然希望依赖 Docker 引擎，则必须使用 [cri-dockerd](https://github.com/Mirantis/cri-dockerd) 。Kubernetes 在[指南](https://kubernetes.io/blog/2022/03/31/ready-for-dockershim-removal/)中提供了因移除 Dockershim 而可能需要的预防措施的更多信息。

# 默认情况下，新的测试版 API 不再有效

Kubernetes 不再默认启用新的测试 API。然而，默认情况下，现有的测试 API 和现有测试 API 的新版本仍将在船上。关于这个话题的更多信息可以在 [GitHub](https://github.com/kubernetes/enhancements/issues/3136) 找到。

# alpha 阶段的上下文日志记录

[上下文日志](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#contextual-logging-in-alpha)特性是在 Kubernetes 版本 1.24 中引入的。它允许函数的调用者控制日志记录的所有方面，比如输出格式、详细程度、附加值和名称。

# 附加说明

关于[进一步更新](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/#other-updates)的信息可以在 Kubernetes 网站上找到。完整的[发布说明](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.24.md)可以在 GitHub 上查看。Kubernetes 1.24 发布背后的团队将于太平洋时间 2022 年 5 月 24 日上午 9:45 至 11:00 举办发布网络研讨会。注册选项和计划可在[活动网站](https://community.cncf.io/events/details/cncf-cncf-online-programs-presents-cncf-live-webinar-kubernetes-124-release-webinar/)上找到。

Kubernetes 1.24 可以在 GitHub 上[下载](https://github.com/kubernetes/kubernetes/releases/tag/v1.24.0)。还有一个[互动教程](https://kubernetes.io/docs/tutorials/)供 Kubernetes 入门使用。Kubernetes [发布公告中提供了所有发布信息。](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/)
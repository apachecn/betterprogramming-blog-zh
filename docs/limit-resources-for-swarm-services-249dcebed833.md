# 如何限制 Docker 群服务的资源

> 原文：<https://betterprogramming.pub/limit-resources-for-swarm-services-249dcebed833>

## **开始控制 Docker 服务和容器的资源消耗**

![](img/39f172bf994a9884406f2bf58d8c5b85.png)

由[亨宁·维茨勒](https://unsplash.com/@henning?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/limit?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在 Linux 系统上，OOM killer(内存不足)将杀死任何试图消耗资源(如 RAM)超过主机可用资源或分配给进程的资源的进程。

这种行为可能会对正在运行的服务产生非常危险的影响，不仅是在生产环境中，而且对于试运行和测试环境也是如此，原因很简单，因为这意味着服务将由于需要更多资源而停止运行。

这种行为的另一个缺点是，如果允许某个服务消耗主机上的所有资源，则存在影响同一主机上运行的其他服务的风险。

OMM killer 进程正在监视 Linux 系统上运行的所有进程或服务，包括 Docker 守护进程和容器。

通过定义 Docker 守护进程和服务的资源约束，可以降低一个服务或进程消耗所有节点资源并影响同一主机上其他服务的风险。

有了这个想法，Docker 守护进程和 Docker 容器都将有自己的限制。OMM killer 会遵守这些限制，当进程超出限制时，它会终止进程。

虽然根据官方[文档](https://docs.docker.com/config/containers/resource_constraints/#limit-a-containers-access-to-memory)也有可能限制 Docker 运行时的资源，但在这篇文章中，我将重点关注在单个 Docker 群服务上设置资源限制。

Docker Compose 提供了两种方法来约束和限制为每个单独的群服务和容器分配的资源。下面是对这些方法的简要描述。

# **预订**

该方法将在创建期间为 Docker 任务保留分配的资源量。这意味着，如果您有一台 16gb RAM 的主机，并且您为每个服务任务分配了 1GB，那么您在该主机上最多只能运行 16 个任务。

如果您尝试将该主机上的服务部署或扩展到 16 个以上的任务，所有新任务都将被拒绝，并且不会创建新的容器，因为主机上没有可用的资源。即使 16 个正在运行的容器只消耗 5 GB，情况也是如此。

这种行为背后的原因是 Docker 将在预定的时间为每个任务保留 1 GB 的 RAM，主机上的任何其他进程都不能使用它。

预留的限制值应该尽可能小，以保证服务能够在预定的主机上按预期启动和运行。

另一方面，预留值不应阻塞主机上不需要的资源，也不应影响 Docker Swarm 集群上其他服务的调度。

例如，对于平均消耗 500 MB 的服务，不建议将预留值设置为 5 GB。在这种情况下，很明显，我们将主机资源锁定在特定的服务上是在浪费资源。

# **极限**

这种方法在单个基础上设置 Docker 服务的上限。

为所有服务设置此限制将保证没有服务或容器会超出其自身的资源限制，消耗主机上的大部分资源，从而影响其他服务的性能。

根据 Docker Compose [引用](https://docs.docker.com/compose/compose-file/)，可以通过以下方式为各个服务设置`cpu`和`memory`资源约束:

```
version: "3.7"
services:
  redis:
    image: redis:alpine
    deploy:
      resources:
        limits:
          cpus: '0.50'
          memory: 50M
        reservations:
          cpus: '0.25'
          memory: 20M
```

# **结论**

从资源的角度来看，为群服务设置资源限制有助于将群服务彼此隔离。因此，没有任何服务能够支配主机服务器上的所有资源。
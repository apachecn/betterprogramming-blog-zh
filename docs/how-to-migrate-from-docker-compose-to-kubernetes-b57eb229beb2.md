# 如何从 Docker Compose 迁移到 Kubernetes

> 原文：<https://betterprogramming.pub/how-to-migrate-from-docker-compose-to-kubernetes-b57eb229beb2>

## 将您的服务从 docker-compose 文件移动到 Kubernetes 资源并部署它们

![](img/f9ce4b90cbcf7221487dce637fe2d25d.png)

安德鲁·鲁伊斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

Docker Compose 是一个很棒的工具，可以用来在开发环境中管理 Docker 容器的创建和部署。但是，它并不是将容器部署到生产环境中的最佳选择，因为它缺少生产部署所需的许多功能，例如构建容器集群以支持容器的高可用性和支持零停机部署。

另一方面，Marathon、Nomad、Docker Swarm 和 Kubernetes 等容器编排器是为管理生产环境中的容器部署而构建的。这些系统旨在处理:

*   大量运行在容器中的微服务。
*   使用群集和服务复制以高可用性模式托管服务。
*   服务的自动恢复、服务发现、资源管理以及许多其他功能。

如果您希望托管高度可用和可靠的服务并避免服务停机，则必须选择其中一种工具来管理生产环境。

本文将逐步描述从 Docker Compose 到 Kubernetes 的迁移过程。

# Docker 撰写文件

下面的要点包含三种服务(MySQL、Rails 后端和 Nginx 反向代理服务)的定义，只需使用下面的命令就可以部署这三种服务:

```
$> docker-compose up -d
```

我们将使用上面的组合文件，并尝试将已定义的服务迁移到 Kubernetes 对象，并将它们部署在 Kubernetes 集群中。

# Kubernetes 概述

[Kubernetes](https://kubernetes.io/) 是世界上许多个人和公司广泛使用的著名容器编排器之一。它最初是由谷歌开发的，目前是一个由 CNCF 管理的开源项目。

Kubernetes 支持与管理容器生命周期相关的各种特性，并且还受到一个庞大社区的支持，该社区包括个人以及大型公司，如 IBM、Google、Microsoft 和 Red Hat。

与 Docker Compose 不同，Kubernetes 更加灵活和复杂(同时)。例如，Kubernetes 提供了几个对象、资源和解决方案来处理容器部署，而 Compose 只提供服务定义(如上面的文件所示)。此外，它们之间的术语也不同。例如，`service`对于这些工具来说意味着完全不同的东西。

# 库伯内特资源公司

如上所述，Kubernetes 提供了广泛的资源列表。可以使用下面的命令查看这些资源的完整列表:

```
$>  kubectl api-resources
```

根据需要部署的堆栈，可以消耗和创建这些资源。下面列出了大多数情况下所需的基本资源，并对每种资源进行了简要描述:

*   `PersistenntVolume` —集群中的一个调配存储(例如，可以是 AWS 上的本地或远程存储)。
*   `PersistenntVolumeClaim` —用户或服务对`PersistentVolume`的请求。
*   `Pod` — Kubernetes 不支持直接创建容器，Kubernetes 中的基本执行对象是 Pod，它可能包含一个或多个容器。
*   `ReplicaSet` —负责维护给定应用程序副本的稳定状态的资源。
*   `Deployment` —管理容器部署周期事件，如滚动更新、撤消更改、暂停和恢复更改。
*   `Service` —负责实现部署的应用程序之间的通信。
*   `Ingress` —向外部世界公开内部服务。

这些资源的定义包括四个主要部分，如下所示:

*   apiVersion —资源的 API 版本。
*   种类—资源的类型。
*   元数据—应附加到资源的元数据，如标签和名称。
*   规范—定义已定义资源的规范和配置。

# 部署 MySQL

为了能够将 MySQL 应用程序部署到 Kubernetes，我们需要创建以下 Kubernetes 资源:

*   `PersistentVolume`:由于 MySQL 服务需要在磁盘上保存数据，我们需要使用与 Compose 相同的卷。否则，数据将存储在容器中，一旦容器被重新创建，数据将会丢失。下面的 YAML 文件定义了 1Gi 容量的`hostPath`和`PersistentVolume`资源。将在主机上创建数据卷。不建议将数据直接存储在主机上，尤其是如果将 pod 配置为在集群上的不同节点上进行调度。在这种情况下，应使用其他[卷插件](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#types-of-persistent-volumes)。

*   `PersistentVolumeClaim`:创造一个`PersistentVolume`不足以能够使用它。还需要请求使用创建的卷，这个请求可以使用`PersistentVolumeClaim`来完成。根据下面的文件，我们请求使用 500 毫升的`PersistentVolume`容量。

*   `Deployment`:`Deployment`资源将负责创建应用程序的`Replicaset`，应用程序的容器将由创建的`ReplicaSet`对象管理。`Deployment`资源的定义应包含以下部分:

1.  副本:应该从应用程序创建的副本数量。
2.  选择器:用于选择由部署管理的单元的条件。如下面的代码片段所示，部署将管理所有带有标签`app=mysqldb-pod`的 pod。
3.  模板:将用于创建和管理窗格的窗格模板。如下所示，该模板包括 pod 标签、容器规格、附件等。

*   `Service`:需要`Service`资源才能与 MySQL pods 通信。可以直接在其 IP 上访问服务单元，但在复制的情况下，更容易拥有一个服务 IP 来平衡服务单元之间的负载。服务资源的规范必须包括以下部分:

1.  类型:服务的类型。Kubernetes 支持几种[服务类型](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)来管理内部和外部通信。外部服务不应访问 MySQL 服务。因此，我们将使用一个`ClusterIP`类型。该服务将在集群级别公开，并且可以由集群中的任何 pod 访问。
2.  选择器:选择服务管理的 pod 的条件。
3.  端口:定义服务端和容器端端口的端口映射。

# **部署后端应用**

部署 Rails 应用程序可以用与 MySQL 应用程序相同的方式完成，除了我们不需要`PersistentVolume`资源，因为它不持久存储任何数据。下面是部署 Rails 应用程序所需的资源:

*   `Deployment`:`Deployment`对象将定义后端应用程序容器模板，并传递将应用程序连接到 MySQL 数据库所需的环境变量。注意，我们使用上面定义的 MySQL 服务名从后端应用程序连接到数据库。Kubernetes 中的服务发现服务将负责把这个名称翻译成服务 IP。

*   服务:可以使用`NodePort`或`Loadbalancer`服务在主机上直接公开 Rails 服务(例如，在端口 80 上)。但是，使用这种方法，不可能在同一个端口上使用其他服务，并且很难用这种方法管理大量服务。因此，建议使用`ClusterIP`管理服务，使用`Ingress`控制器管理外部通信。下面是 Rails 服务定义文件:

# 部署入口控制器

构建一个`Ingress`控制器并向外界公开内部服务包括以下步骤:

*   创建默认的后端服务:当请求的 URL 不能映射到任何已定义的服务时，`Ingress`控制器将使用默认的后端服务。默认的后端服务可以是任何服务，只要它在根路由上响应 404 错误，在`healthz`端点上响应 200 错误。

*   `Ingress`控制器是负责处理传入请求并将请求代理到各自服务的应用程序。如下面的代码片段所示，`Ingress`控制器被配置为使用上面定义的服务作为默认的后端服务:

*   `Ingress`服务:使用`LoadBalancer`服务在主机上暴露`Ingress`控制器是很重要的(也可以使用`NodePort`来完成)。下面的服务定义公开了端口 80 和 443 上的`Ingress`控制器:

*   `Ingress`规则:这个资源是配置`Ingress`控制器所需要的信息，以便将请求代理给 Rails 应用程序。从下面的`Ingress`定义中，我们可以看到主机`rails.lvh.me`的请求将被重定向到端口 8080 上的 Rails 服务:

可以使用以下命令之一将上面创建的资源部署到 Kubernetes 集群:

```
$>  kubectl apply -f ${filename}
$>  kubectl create -f ${filename}
```

# 结论

部署到 Kubernetes 集群比使用 Docker Compose 部署更复杂。然而，由于其灵活性、可靠性和特性，Kubernetes 是将容器部署到生产环境中最常用的编排工具之一。

本文中展示的部署的完整实现可以在 GitHub 上找到[。](https://github.com/wshihadeh/migratetoK8s)
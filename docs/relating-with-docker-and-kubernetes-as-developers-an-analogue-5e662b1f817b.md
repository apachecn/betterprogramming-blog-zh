# 与作为开发者的 Docker 和 Kubernetes 相关联——一个类比

> 原文：<https://betterprogramming.pub/relating-with-docker-and-kubernetes-as-developers-an-analogue-5e662b1f817b>

## 我在任何地方都找不到有趣的 K8s 速成班，所以我做了一个

![](img/284300dbb89b9f26605d0dc7a4633059.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的气候现实项目[拍摄的照片](https://unsplash.com/@climatereality?utm_source=medium&utm_medium=referral)

对于那些还没有机会，但希望得到一个关于 **K** (ubernete)(8) **s** 的好主意，和/或想亲自动手的开发人员。

作为软件行业的新人，Kubernetes 对我来说是陌生的。这与我所做的软件开发没有直接关系，或者说我是这样认为的。进入这个行业一年后，我对它有了初步的了解，但我仍然没有太多的互动。当然，理论上我知道 Kubernetes 的基本知识，但不知何故，我读的所有文章都太难懂，课程太长。

使用 Kubernetes 有了更多的互动，现在我发现它与我作为开发人员的经历非常相似。我在任何地方都找不到吸引人的、有关联的课程，所以我做了一个。

# 旅程开始了！

这是一个冒险的新生第一天上班的故事。这也是一篇关于 K8s 的文章，大家多多包涵。

## 应用程序:向涉众提供预定义服务的开发人员

> 应用程序(简称为 app 或应用程序)是一种计算机程序，设计用于执行通常由最终用户使用的特定任务。

在某种程度上，他们与执行任务来开发软件以满足最终用户需求的开发人员有关。

# 我们如何让每一个开发者独立而有效？

## 容器:开发人员的便携式工作空间工具包

容器是操作系统虚拟化的一种形式。它们是软件可执行单元，其中应用程序代码及其依赖关系和配置以可以在任何环境下运行的方式打包。

它们提供了进程隔离并优化了资源利用。它们不包含操作系统映像。因此，它们更轻、更便携、开销更少。

> 想想像工作机器/笔记本电脑这样的容器，它们可以使员工独立工作并有效地提供服务。它们是便携式的，使员工能够在家或办公室工作，并拥有员工完成任务所需的所有资源。它们也是隔离的，因此员工不会受到其他人在他们的机器上运行的内容的影响。

# 它始于一件事…入职指南

## 图片:使用工作区套件的入门指南/蓝图

为了理解 Kubernetes 是如何工作的，我们必须熟悉图像。

> 这些图像是一组用于构建容器的可执行命令集。它们是不可变的文件，是容器的快照，使它们成为容器的指南或模板。

图像是其他图像的层，每个图像都源自前一层，但在某些方面有所不同。当您启动一个映像时，您实际上正在运行它的一个容器。同一个图像可以有多个运行的容器。

图像可以与用于启动工作机器的指南或手册相关联。或者，把它想象成最初创建工作机器的蓝图:)。

# Docker 图像和容器入门

Docker 是一个众所周知的运行时环境，用于在容器内创建和构建应用程序。它使用 Docker 映像在不同的环境中部署容器化的应用程序或软件，从开发到测试和生产。

## 创建一个样例 Go 应用程序

让我们创建一个基本的 Go 应用程序，我们将使用它部署在我们的`minikube`集群上。

创建一个目录 my-app，其中包含下面的 main.go 文件。从命令行运行以下命令:

```
go mod init 
go mod tidy
```

main.go

## 为 Go 应用程序创建 docker 文件

为了给我们的应用程序创建 dockerfile，我们将参考[这个](https://docs.docker.com/language/golang/build-images/):

Dockerfile 文件

## 构建 docker 映像并运行容器

要构建 docker 映像，请在终端中运行以下命令:

```
docker build --tag myapp .
docker images
```

您应该能够在列表中看到新创建的`myapp`图像。

现在让我们尝试[将图像作为容器](https://docs.docker.com/language/golang/run-containers/)运行。因为容器是独立运行的，所以我们需要将容器内部的端口暴露给我们的主机端口。

> 为了发布容器的端口，我们将在 docker run 命令中使用`--publish`标志(简称为`-p`)。`--publish`命令的格式是`[host_port]:[container_port]`。因此，如果我们想将容器内部的端口`8080`暴露给容器外部的端口`3000`，我们可以将`3000:8080`传递给`--publish`标志。

```
docker run -d -p 8080:10000 myapp
```

在终端中打开 localhost:8080；它应该说

```
Welcome to myapp!
```

现在我们对 docker 容器和生成它们的模板(即图像)有了一个概念。我们来看看容器调度器，这就是 K8s。

# 为什么我们需要容器调度程序？为什么 K8s 胜过集装箱/码头工人？

我们看到了容器对于在任何环境下以隔离的方式运行应用程序是多么有用。然而，在生产中，可能需要成百上千个不同的容器。

像 Docker 这样的容器运行时系统受益于使用额外的技术来编排或管理所有使用中的容器。

# 我们如何指导所有开发人员同步工作以提供最终产品？

## Kubernetes:工程经理

> Kubernetes 是一个开源的容器管理和部署平台。它协调虚拟机集群，并根据可用的计算资源和容器的资源需求，调度容器在这些虚拟机上运行。他们可能与经理有关，经理计划和协调开发人员交付项目，并确保他们拥有完成项目所需的所有资源。

我们可以使用 K8s 来部署我们的服务，在不停机的情况下推出新的版本，以及扩展这些服务。它是可移植的、可扩展的、自我修复的和高度可用的。

## 什么是豆荚？

> Kubernetes [*pod*](https://kubernetes.io/docs/concepts/workloads/pods/) 是一组一个或多个容器，为了管理和联网的目的而连接在一起。

在我们的工作区类比中，pods 可以是一对高级和初级开发人员，他们一起完成相关的任务。它也可以是一个开发人员，他熟悉这个系统，并且习惯于独自处理任务。

## 节点和集群:为开发人员提供资源的工作站

考虑上面员工工作区的例子。每位员工都需要显示器、椅子、桌子等资源。，才能正常工作。经理需要给每个员工分配这些资源。

> 一个节点可以被认为是提供这些资源的工作站，以及将这些工作站分配给员工的管理器 kubernetes。

在 Kubernetes 中，节点是一台工作计算机，可以是虚拟的，也可以是物理的。一个节点可以有许多 pod，Kubernetes 自动处理集群节点之间的 pod 调度。节点是计算的最小单位。为了建立集群，节点共享它们的资源。

# Kubectl 和 Minikube 入门

Kubectl 是 Kubernetes 的 CLI，用于与 Kubernetes API 交互来创建和管理集群。要安装 kubectl，请运行以下命令:

```
brew install kubectl
```

## Minikube 是什么？

> Minikube 是一个让你在本地尝试 Kubernetes 的工具。它是本地机器上的单节点 K8s 集群，用于开发或试用 K8s。Minikube 工具包括一组内置的[插件](https://kubernetes.io/docs/concepts/cluster-administration/addons/)，可以在本地 Kubernetes 环境中启用、禁用和打开。

对于[安装 minikube](https://formulae.brew.sh/formula/minikube) 并在 MacOS 上启动，运行以下命令:

```
brew install minikube
minikube start
```

# 向 Kubernetes 部署您的服务

首先，使用以下命令将您构建的本地 docker 映像推入 minikube 缓存:

```
minikube cache add myapp:latest
```

## 部署:年初定义的团队目标和结构

一个*部署*为[pod](https://kubernetes.io/docs/concepts/workloads/pods/)和[复制集](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)提供声明性更新。

> 在部署中，我们定义一个期望的状态，部署控制器逐渐将当前状态转换为期望的状态。部署可用于构建新的副本集或删除当前部署并用新部署替换它们。

看看下面的部署文件:

部署. yaml

上面的部署文件就像是 pods 和`replicasets`的声明性模板。上面的部署在`{metadata.name}`中命名为`myapp`，创建一个`replicaset`来调出两个`myapp`吊舱。现在,{spec.containers.image}给出了要提取的图像，以运行由`{template.metadata.labels}` `app:myapp`给出的 pod 中的容器。部署通过`{spec.selector.matchlabels} app:myapp`知道要管理哪些 pod。

## 什么是复制集？

副本集的目标是始终保持一组一致的副本盒运行。因此，它经常被用来确保一定数量的相同豆荚的可用性。

## 通用部署策略

重新创建:终止所有现有的 pod，然后生成新的 pod

滚动:单元是以滚动的方式创建的，慢慢增加直到所有新单元都在运行

## 创建 K8s 部署

```
kubectl apply -f deployment.yaml
```

## 检查 minikube 集群中的跑步记录

```
kubectl get pods
```

现在尝试进入 pod 并检查应用程序是否正在运行:

```
kubectl exec -it <pod-name> sh
apk update
apk add curl
curl localhost:10000
```

## 检查 Kubernetes 的自愈能力

```
kubectl delete pod <pod-name>
kubectl get pods
```

我们应该能看到两个舱还在运行。由于我们删除了一个 pod，`replicaset`控制器检测到它并启动另一个 pod 以保持两个 pod 的期望状态。

# 外部团队如何相互沟通？

## Kubernetes 服务:将相关的外部通信路由到开发人员的 SPOC 团队

豆荚是短暂的资源。部署可以动态生成和销毁吊舱。因为 pod 是不稳定的、短暂的和易变的，我们不能相信应用程序总是可以通过 pod 的 IP 到达。

我们需要一个永久的地址，这个地址会将请求发送到当时活动的任何 pod。

> 在 Kubernetes 中，服务是一种抽象，它定义了一组逻辑单元和访问它们的策略。服务所针对的 pod 集合通常由一个[选择器](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)决定。Kubernetes 服务提供地址，通过这些地址可以访问相关的 pod。

看看下面的 service.yaml 文件:

service.yaml

这指定了以标签为`app:myapp`的 pod 的端口 10000 为目标的服务。

# 创建 Kubernetes 服务

```
kubectl apply -f service.yaml
```

`minikube` tunnel 命令可用于公开负载平衡器服务。要保持负载平衡器处于活动状态，它必须在单独的终端窗口中运行。

```
minikube tunnel 
```

`minikube`隧道作为主机上的一个进程运行，并使用群集的 IP 地址作为网关，创建一个到群集的服务 CIDR 的网络路由。tunnel 命令允许主机操作系统上的任何应用程序立即访问外部 IP 地址。

```
kubectl get service myapp
```

你应该能看到外部 IP；早些时候它会一直悬而未决。

现在，您可以使用此 ip 在浏览器中打开服务:

```
minikube service --url myapp
```

您应该能够在您的浏览器中查看它。

```
Welcome to myapp!
```

> 如果没有与开发人员生活的类比，关于服务的部分是不完整的。想象一个外部团队不确定或者困惑于如何使用开发团队开发的特性。解决问题的一个方法是直接联系已知的开发人员。当然，这是可行的，但是万一开发人员已经转移到不同的团队并且丢失了上下文呢？在这种情况下，团队 SPOC 来帮忙在团队内部路由查询。

# 这是一篇很长的文章…我希望下次能告诉你更多！

说到这里，我们就到了关于 K8s 和新生第一天的冒险的文章的结尾。然而，还有更多的东西要学习，更多的冒险要跟随，所以请保持关注！

# 参考

*   [Kubernetes 服务](https://kubernetes.io/docs/concepts/services-networking/service/)
*   [Kubernetes 部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
*   [关于 Kubernetes 组件的读取模式](https://kubernetes.io/docs/concepts/overview/components/)
*   [码头集装箱](https://www.docker.com/resources/what-container)
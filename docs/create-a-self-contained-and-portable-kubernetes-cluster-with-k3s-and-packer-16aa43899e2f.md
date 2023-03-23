# 使用 K3S 和 Packer 创建一个独立的可移植 Kubernetes 集群

> 原文：<https://betterprogramming.pub/create-a-self-contained-and-portable-kubernetes-cluster-with-k3s-and-packer-16aa43899e2f>

## 用于开发或遗留基础设施的可移植 Kubernetes 集群

![](img/997be2d6a874035478ed79061cc40305.png)

安特·罗泽茨基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

在本文中，我们将构建一个托管单节点 Kubernetes 集群的虚拟机。

让我们从一个基本问题开始:为什么您希望 Kubernetes 集群在 VM 中运行？我认为有两个原因。

首先，这是一种在几秒钟内提供一个共享的低成本集群作为开发环境的简单方法。你只需要启动虚拟机，瞧，你有一个功能齐全的集群。对于这种使用情形，您可能不想向云提供商支付昂贵的托管控制平面。

如果集群不是共享的，并且为了完整性，开发人员可以在他们的笔记本电脑上运行本地 K3D:

[](https://codeburst.io/creating-a-local-development-kubernetes-cluster-with-k3s-and-traefik-proxy-7a5033cb1c2d) [## 使用 k3s 和 Traefik 代理创建本地开发 Kubernetes 集群

### 也被称为穷人的 Kubernetes

codeburst.io](https://codeburst.io/creating-a-local-development-kubernetes-cluster-with-k3s-and-traefik-proxy-7a5033cb1c2d) 

一个更加扭曲和真实的动机是为那些只运行传统本地虚拟机并且不准备使用该技术的客户启用 Kubernetes。如果您已经构建了一个考虑到 Kubernetes 的产品，那么您肯定不希望脱离您的部署策略，为在您的客户数据中心或云提供商中运行的 VM 创建一个特定的设置。但是通过在一个虚拟机上运行整个 Kube 集群，现在只需做一些小的调整，就可以部署为 Kubernetes 开发的产品。

# 我们选择的工具

在本演练中，我们将使用:

*   [Packer](https://www.packer.io/) 由 HashiCorp 创建我们的虚拟机映像。
*   Rancher 的 K3S 用于高性能和可配置的集群。
*   [以 Oracle 的 VirtualBox](https://www.virtualbox.org/) 为例。

# 写入打包程序映像

## 决定虚拟机的特征

第一步是选择您喜欢的 Linux 操作系统。我们将以 Ubuntu 服务器为例。您还需要决定这个虚拟机的容量:它的 RAM、CPU 和磁盘大小。

当你下定决心后，创建一个定义了几个变量的 Packer `vm.json`文件。但是，请务必选择一个更好的密码:

## 配置操作系统安装程序

Ubuntu 基于 Debian，[使用一个预置文件](https://help.ubuntu.com/lts/installation-guide/s390x/apb.html)来配置安装程序。该文件基本上取代了安装程序的交互式提示。

首先，用下面的`preseed.cfg`文件创建一个新的`http/`文件夹:

Ubuntu 安装程序可以配置为使用 HTTP 下载该文件。幸运的是，Packer 允许我们通过为我们启动一个 web 服务器，用 HTTP 向 VM 公开文件。服务器主机名和端口作为特殊的模板变量`.HTTPIP` 和`.HTTPPort`可用。

我们想把我们的虚拟机打包成一个虚拟的 ISO 文件，所以我们使用 Packer 的匹配的[构建器](https://www.packer.io/docs/builders/virtualbox/iso)。为了让我们的生活更容易，我们还将 [VirtualBox 的附加功能](https://www.virtualbox.org/manual/ch04.html)复制到虚拟机中。

把这些放在一起，我们就有了这个`vm.json`文件:

## 编写安装脚本

我们现在有了一个基本的 Ubuntu 服务器和裸操作系统。我们需要添加一些脚本来安装这些组件:

*   VirtualBox 来宾添加
*   Docker 守护进程和相关的 systemd 服务配置文件
*   Kubectl 和 Helm 实用程序
*   当然是 K3S
*   在维护的情况下为 Kubectl 定制一些生活质量用户

我们将所有这些脚本放在一个新的`scripts`文件夹下。

我们从创建一个`virtualbox.sh`文件开始安装 VirtualBox guest additions:

我们继续处理 Docker 服务文件，并将其保存为`docker.service`:

`docker.sh`设置脚本直接来自 Docker 文档:

我们继续为 Kubectl 和 Helm 编写新的`k8s.sh`脚本:

电阻片是 K3S。查看[可用选项](https://rancher.com/docs/k3s/latest/en/installation/install-options/server-config/)的文档。例如，我们禁用捆绑的 Traefik 入口控制器。下面是`k3s.sh`脚本:

我们通过创建一个`user.sh`脚本来改进 Kubectl 的命令行，从而结束这个脚本会话:

## 添加置备程序

让我们回到我们的 Packer `vm.json`文件。我们现在需要:

*   将之前的脚本复制到虚拟机中。
*   运行它们以完成虚拟机设置。

我们使用[包装商的供应器](https://www.packer.io/docs/templates/provisioners)来达到预期的结果:

我们现在完成了！

# 构建和运行虚拟机

您应该有以下目录组织:

```
packer
├── docker.service
├── http
│   └── preseed.cfg
├── scripts
│   ├── docker.sh
│   ├── k3s.sh
│   ├── k8s.sh
│   ├── user.sh
│   └── virtualbox.sh
└── vm.json
```

只需运行命令`packer build vm.json`,您的虚拟机就会作为 OVF 文件创建在已配置的输出文件夹中。你现在可以在 VirtualBox 中运行这个文件，并启动你闪亮的新虚拟机！

不要忘记，您可以轻松地使用您的主机创建共享文件夹，并设置虚拟桥或任何其他联网可能性，以实现您的主机和来宾 VM 之间的通信。

我希望你会喜欢你的新 Kubernetes 集群！
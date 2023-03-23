# 从零开始

> 原文：<https://betterprogramming.pub/kubernetes-from-scratch-4691283e3995>

## 不含 Minikube 或 Microk8s 的 Kubernetes

![](img/bf1860c18539402b3bace5b67c44644e.png)

由[马塞罗·根纳里](https://unsplash.com/@marcello54?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/lego?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我已经安装了几个预配置的 Kubernetes 版本，比如 Minikube 和 Microk8s，它们在创建 Kubernetes 沙盒方面表现很好。今天，我将尝试从头安装，以深入了解幕后情况。

Kubernetes 不是一个整体，它是许多组件一起工作，其中许多现在看起来像魔术。但我总是想知道魔术师的戏法，所以我要看看是什么样的戏法让一切都变得可行。

为了跟进，您将必须有一个虚拟机供应(我将在后面详细讨论)。您还需要掌握关于 Linux、Linux 命令行和网络的中等程度的高级知识。你也需要极大的耐心，就像任何使用计算机的努力一样。

首先，我将为一台安装了 Ubuntu 18.04 的虚拟机配置两个虚拟 CPU、4G 内存和 50G 硬盘。我在裸机上使用 KVM。下面是我在主机上设置它的步骤模板:

我将我的虚拟机命名为“kube1”(假设以后会有更多)，并将 RAM 更改为 4096，将磁盘大小更改为 50，将 vCPUs 更改为 2。用默认值或合理值回答提示。当 OpenSSH 服务器询问您想要预装什么软件时，请确保您安装的是 OpenSSH 服务器，而不是其他任何东西。完成后，您就有了 IP 地址，您可以将它添加到您的`/etc/hosts`文件中，这样您就可以通过名称来引用它。如果你想了解更多关于 KVM 的知识以及如何安装，可以参考我的文章[玩 VMs 和 Kubernetes](https://medium.com/better-programming/playing-with-vms-and-kubernetes-26ef93019c22) 。

为了简单起见，我将运行 KVM 的主机称为*主主机*，将您的开发主机(很可能是您正在阅读本文的笔记本电脑或台式机)称为*本地计算机*。如果您使用的是云调配的虚拟机，您将没有主机。你将不得不弄清楚在你的云控制面板或者作为主要主机的本地计算机上的命令行中需要发生什么(比如用于谷歌云平台的`gcloud`命令)。

首先需要的是`kubeadm/kubectl/kubelet`命令。我将继续关注[官方安装](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)，但特别是针对 Ubuntu 18.04 VM，并添加任何需要澄清或不太适用的内容。

要准备新虚拟机进行安装，首先要确保 swap 处于关闭状态:

如果要创建多个节点，您必须确保创建的虚拟机具有唯一的 MAC 地址和产品 UUIDs。KVM 为您做到了这一点。可能大多数虚拟机都是唯一的，除非您克隆现有的虚拟机。然后通过在您的`/etc/modules-load.d/modules.conf`文件中添加几行来启用桥接和覆盖:

```
overlay
br_netfilter
```

并在您的`/etc/sysctl.conf`文件中添加几行:

```
# added for kubernetes bridge
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-iptables  = 1
```

然后重新启动你的虚拟机，使其生效。

您的新虚拟机应该没有`iptables`规则，您可以使用`sudo iptables -L`命令来验证这一点，该命令应该列出一个空的规则集:

```
Chain INPUT (policy ACCEPT)
target     prot opt source               destinationChain FORWARD (policy ACCEPT)
target     prot opt source               destinationChain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

如果你有妄想症，你可以将端口限制在官方安装中列出的端口，但这是一个地雷，我不会踩在上面。

因为 Kubernetes 是一个容器编排系统，我们需要一个它可以编排的容器系统。您可以使用许多容器系统，但是我们将使用 [containerd](https://containerd.io/) 。这是 Ubuntu 18.04 的两行安装:

```
sudo apt-get update 
sudo apt-get install containerd
```

这应该会产生`/var/run/containerd/containerd.sock`。

现在可以安装`kubeadm/kubectl/kubelet`了。

`kubeadm`包是 Kubernetes 的主要 API。`kubectl`包是 Kubernetes API 的命令行接口。并且`kubelet`包与容器系统接口以运行 pod。

关于术语的几句话。Kubernetes 中的整个实体被称为*集群*。每个集群可以有一个或多个*节点*。有两种类型的节点，*控制平面，*和*工作者*。必须有且只有一个控制平面节点。(实际上，在一个 HA 设置中，负载均衡器前面可以有多个控制平面。)控制平面节点就是我们现在正在研究的。如果我们稍后添加工作节点，所有这些步骤都必须重复。

为了测试`kubeadm`可以访问我们之前安装的`containerd`，我们可以运行`sudo kubeadm config images pull`。它会花一些时间提取一些它需要的图像，我们知道它可以和`containerd`对话。

我们需要提前考虑一下。Kubernetes 需要一个容器网络接口，或`CNI`，这样所有的 pod 就可以互相通信。但是为了初始化集群，我们需要传递一些关于它将如何利用`CNI`的信息。所以我们需要决定使用哪一个`CNI`，因为有好几个。我打算选法兰绒，因为我住在西雅图。默认情况下，法兰绒使用 CIDR 10 . 244 . 0 . 0/16，所以我们必须将它传递到`init`命令中。让我们试一试，看看会发生什么。

```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

成功！但现在不是沾沾自喜的时候。对于一个节点，我们还有一些步骤要做。在`init`命令的末尾有一些重要的步骤。如果您计划稍后创建 worker 节点，请务必复制打印的`join`命令，以便稍后参考。列出的令牌仅持续 24 小时，因此如果您想在此之后创建一个新节点，您必须使用命令`kubeadm token create`获得一个新令牌。

让我们把配置放在一个公开的地方供`kubectl`使用。

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

这将创建`kubectl`使用的默认配置文件。要进行验证，运行`kubectl config view`。

来自`init`命令的输出还指示您部署您的`CNI`，我们已经决定它将是法兰绒。它们有你在 GitHub 上需要的配置，所以现在就应用它们:

```
kubectl apply -f [https://raw.githubusercontent.com/coreos/flannel/2140ac876ef134e0ed5af15c65e414cf26827915/Documentation/kube-flannel.yml](https://raw.githubusercontent.com/coreos/flannel/2140ac876ef134e0ed5af15c65e414cf26827915/Documentation/kube-flannel.yml)
```

现在，看看您能否列出使用`kubectl get all --all-namespaces`运行的所有内容。你应该得到一份通常嫌疑人的名单。将配置文件复制到主要主机，您应该能够在那里访问您的新集群。我将把它复制到`~/.kube/kube1config`文件并设置`KUBECONFIG`环境变量，这样我就不必与现有的配置文件合并。现在在主机上运行`kubectl get all --all-namespaces`应该会给出相同的清单。

现在我将重复本文中的所有步骤，直到`kubeadm init`。我做的时候你可以喝杯咖啡。

![](img/9d0840848df313d1308ce23b257451a3.png)

香榭丽舍大街上的浓咖啡，作者

我回来了。我创建了一个名为“kube2”的新虚拟机，并安装了`contianerd`、`kubeadm`、`kubectl`和`kubelet`，和以前一样。现在我们不用`kubeadm init`命令，而是使用之前`kubeadm init`中保存的`kubeadm join`命令。

现在让我们检查一下，用`kubectl get nodes`列出主机上的节点。

```
NAME    STATUS   ROLES    AGE   VERSION
kube1   Ready    master   73m   v1.18.1
kube2   Ready    <none>   65s   v1.18.1
```

再次成功！

我们还有几块拼图需要添加。当你通过像 Minikube 这样的一体化安装程序安装时，所有这些组件都包括在内，但我们必须手动完成。我们需要一些存储、容器注册表和某种形式的入口或负载平衡器。我将在下一篇文章中解决这些问题。
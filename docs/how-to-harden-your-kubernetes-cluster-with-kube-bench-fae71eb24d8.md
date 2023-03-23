# 如何使用 Kube Bench 强化您的 Kubernetes 集群

> 原文：<https://betterprogramming.pub/how-to-harden-your-kubernetes-cluster-with-kube-bench-fae71eb24d8>

## 针对您的 Kubernetes 集群的全面 CIS 基准测试

![](img/a24691d5e16b4718ae7ac53cf40b38da.png)

照片由[多尔萨·马斯加蒂](https://unsplash.com/@dorsamasghati?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/concrete?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

[Kubernetes](https://kubernetes.io/) 无疑是最受欢迎的容器编排平台。它的成功部分是由于它提供的灵活性和特性数量。虽然它是一个开源工具，但它拥有来自全球领先科技公司的庞大开发者群体，包括谷歌。

Kubernetes 的安全性是最近的一个关键话题。由于对 Kubernetes 越来越感兴趣，而且越来越多的组织采用 Kubernetes 来运行他们的生产应用程序，网络罪犯正在寻找利用 Kubernetes 的一些常见错误配置的方法。

配置 Kubernetes 安全性是一项任务。有多种配置 Kubernetes 的方法，它提供了很大的灵活性，允许组织根据自己的安全需求进行调整。然而，随着时间的推移，Kubernetes 安全性方面的一些标准和最佳实践也发生了变化。

互联网安全中心(CIS)为安全软件开发和维护制定了指导方针和基准。他们还为保护 Kubernetes 创建了指导方针和基准测试，并为您的集群实现了一定程度的强化。

[Kube Bench](https://github.com/aquasecurity/kube-bench) 是一个开源的 Go 应用程序，它在您的集群上运行 [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes/) 测试，以确保它符合 CIS 安全指南。

如果您在本地或虚拟机上使用普通的 Kubernetes 安装，这将特别有帮助。了解您的托管集群(如 AKS、EKS 或 GKE)中的节点安全性也很有用。

托管的 Kubernetes 集群提供了一个已经就位的 CIS 强化级别，它将一些设置委托给用户。当您想要了解您的工作负载和工作节点是否被适当地设置来实现这些准则时，它最有效。

在接下来的部分中，让我们通过动手练习来了解 Kube 长凳。

# 先决条件

您将需要以下内容:

*   正在运行的 Kubernetes 集群——如果您没有集群，那么尝试使用 Terraform 和一些 shell 脚本来安装集群，如“[如何确保 Kubernetes 的安全”指南中所述。](https://medium.com/better-programming/how-to-secure-kubernetes-the-hard-way-9b421b36aba4)
*   Kubernetes 集群的集群管理员访问权限
*   访问 Kubernetes 主服务器以运行主服务器测试——不可能访问托管 Kubernetes 服务的主服务器。如果您正在使用一个，那么您不能在主节点上执行任何测试。这很好，因为您的供应商负责主节点的安全性，并且大多数主节点已经通过 CIS-benchmark 认证。
*   访问 Kubernetes 节点来运行节点测试——您还可以使用容器通过 Kubernetes 作业访问 worker 节点。Kube Bench 恰当地记录了这种方法。

# 安装 Kube 工作台

安装 Kube 长凳很容易。您可以选择在节点中直接安装二进制文件，或者决定运行一个容器来在主机上安装 Kube Bench 并运行测试。在动手练习中，让我们使用二进制方法。

转到[发布页面](https://github.com/aquasecurity/kube-bench/releases)并选择最新的包。然后在所有节点上运行下面的。

```
$ wget [https://github.com/aquasecurity/kube-bench/releases/download/v0.3.0/kube-bench_0.3.0_linux_amd64.tar.gz](https://github.com/aquasecurity/kube-bench/releases/download/v0.3.0/kube-bench_0.3.0_linux_amd64.tar.gz)
$ tar -xvf kube-bench_0.3.0_linux_amd64.tar.gz
```

`cfg`目录包含运行测试所需的所有配置。`.yaml`文件采用标准的目录结构。如果使用非标准目录，您可能需要根据您的设置编辑`cfg/config.yaml`文件。

# 跑 Kube 长凳

因为我们已经在主机上直接安装了 Kube Bench，所以我们需要 root 权限来执行 Kube Bench。这是必需的，因为 Kube Bench 需要访问只有 root 才能访问的配置文件。

Kube Bench 自动检测 Kubernetes 版本并应用适当的 CIS 基准。然而，由于 OpenShift 和 GKE 有一个稍微定制的 Kubernetes 版本，Kube Bench 不能自动识别该版本。因此，当您运行测试时，您可能需要传递 CIS 版本。您可以用 Kubernetes 版本指定`--version`标志，或者用 CIS 基准版本指定`--benchmark`标志，但不能两者都指定。

以 root 用户身份登录到主节点，并运行下面的代码。

在测试结果中，Kube Bench 记录了它运行了哪些测试，它们是通过了还是失败了，以及您需要做什么来修复这些失败。

按照结果中概述的步骤解决问题，然后重新运行 Kube Bench。

您可以类似地在 worker 节点上运行测试。您还需要对 worker 节点拥有 root 访问权限。

登录到 worker 节点，并以 root 用户身份运行以下命令。

这里也有类似的输出。您可以遵循测试结果的指导方针来修复您的设置，并重新运行测试来验证。

# 在托管集群上运行 Kube Bench

运行托管群集时，获取对工作节点的 root 访问权限可能是一个问题。为了克服这个问题，您可以选择在 pod 内部运行 Kube Bench。

它需要能够访问主机的 PID 名称空间，并且能够访问主机配置文件目录。

幸运的是，Kube Bench 提供了一个在批处理作业中运行测试的`job.yaml`文件。这是一种相当轻量级和 Kubernetes-native 的做事方式。

要运行作业，应用`job.yaml`文件:

```
$ kubectl apply -f job.yaml
job.batch/kube-bench created$ kubectl get pods
NAME                      READY   STATUS      RESTARTS   AGE
kube-bench-k87t0   0/1     Completed   0          11s
```

要获取结果，请获取 Kube 工作台容器的日志。

```
$ kubectl logs kube-bench-j76s9
[INFO] 1 Master Node Security Configuration
[INFO] 1.1 API Server
...
```

Kube Bench 还提供了特定于平台的作业清单，您可以将其用于您的集群，例如`job-gke.yaml`、`job-aks.yaml`和`job-eks.yaml`。

# 结论

感谢阅读！我希望你喜欢这篇文章。

Kube Bench 为我们提供了一种强有力的方法来确保我们的 Kubernetes 集群得到正确设置和适当保护。因为它是一个 Go 应用程序，所以很快就能给你带来结果。如果您想在生产环境中运行集群，这是一个必备的工具。

最好的部分是，标准会不时地被修改，并且必须将 Kube Bench test 作为标准操作过程添加到您的回归包中。
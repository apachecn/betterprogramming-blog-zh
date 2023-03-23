# 在 Kubernetes 上安全地构建容器图像

> 原文：<https://betterprogramming.pub/securely-building-container-images-on-kubernetes-cef0c4c3cc24>

## 运行容器和提取容器图像—不再需要 Docker

![](img/e4fac765ee3f3f9a755782c9546a21e2.png)

*梅萨维德国家公园，古普韦布洛住宅，CO
由 Kurt Madel 2019* 拍摄

回到 2013 年，[在 Kubernetes 出现之前](https://kubernetes.io/blog/2015/04/borg-predecessor-to-kubernetes/)，Docker 正在让 Linux 容器(LXC)变得更容易使用。基于 Docker 的容器开始流行起来(Docker 很快放弃了 LXC 作为他们自己的容器运行时的默认执行引擎)。

与此同时，持续集成(CI)作为一种最佳实践和高效软件交付的必要条件正在迅速成熟。将 Docker 容器与 CI 结合使用很快成为管理 CI 工具(编译器、测试工具、安全扫描等)的最佳方式。但它是新的，没有多少最佳实践被定义——人们的态度是“去搞清楚”。

在早期，将容器用于 CI/CD 的一个重要方面是使用它们来构建容器映像，并将这些映像推送到容器注册中心。但是，这一切都是非常新的——许多人并不真正知道他们在做什么。没有一个*为假人构建容器图像*。

几年后的 2015 年 9 月，杰罗姆·佩塔佐尼发表了一篇名为[“为您的 CI 或测试环境使用 Docker-in-Docker？三思而后行。”](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)本文描述了使用 [Docker-in-Docker (DinD)](https://github.com/jpetazzo/dind) 对于 CI/CD 工作负载来说是多么糟糕的选择，原因有很多。绝对还是值得一读的。Petazzoni 提出了在运行 Docker 守护进程的主机上安装 Docker 套接字，并使用主机 Docker 守护进程在 CI/CD 作业中执行 Docker 命令的概念。将`/var/run/docker.sock`文件作为一个卷安装在 Docker 容器中允许您完成与 DinD 相似的功能，但是没有 DinD 的一些缺点——包括层缓存和需要在启用`[--privileged](https://blog.docker.com/2013/09/docker-can-now-run-within-docker/)` [模式](https://blog.docker.com/2013/09/docker-can-now-run-within-docker/)的情况下运行 DinD 容器。

在关于本地 Kubernetes 连续交付的本系列文章的第 5 部分中，我们将探讨为什么使用这两种方法中的任何一种作为本地 Kubernetes 连续交付管道的一部分来构建和推送容器映像不再是最佳实践。我们将从两个不同的角度来看这个问题:安全性和性能。最后，我们将着眼于安全性和性能，看看另一种方法。

在我们开始在 Kubernetes 上安全地构建和推送容器图像之前，我想分享一些关于容器术语的想法。我通常把在 Kubernetes Pod 中作为容器运行的图像称为**容器图像**，而不是 **Docker 图像**。

早在 2015 年，Docker 就好心地[将 Docker 图像格式](https://blog.docker.com/2017/07/oci-release-of-v1-0-runtime-and-image-format-specifications/)捐赠给了当时新成立的[开放容器倡议(OCI)](https://www.opencontainers.org/) 。除了容器[映像规范](https://github.com/opencontainers/image-spec/blob/master/spec.md)，OCI 还为容器执行维护了一个开放的[运行时规范](https://github.com/opencontainers/runtime-spec/blob/master/spec.md)。这意味着 *Docker 是* *不再需要*来运行容器和提取容器图像——正如你将在本文后面看到的，甚至是构建和推送容器图像。

为了理解使用 DinD 构建和推送容器映像的缺点，我再次推荐您阅读[jérme Petazzoni 的文章](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)。下面是一个快速总结:

*   安全性 DinD 容器必须在启用了`--privileged`标志的情况下运行，从而导致针对底层 Docker 主机的不良攻击。
*   性能—当使用临时 DinD 容器时，层缓存不会在构建之间共享。每次使用新的 DinD 容器时，都必须提取所有映像的所有层，这导致容器映像构建非常慢。

因此，我们已经确定，将 DinD 用于 CI/CD，尤其是用于构建和推送容器映像，是一个坏主意。但是对于 Kubernetes CD，如果您要为 CI/CD 安装 Docker 插座，包括构建和推送容器映像，您也应该三思。

# 表演

让我们暂时把安全放在一边。事实证明，在基于 Kubernetes 的 CD 环境中安装 Docker 插座有很大的问题。

Kubernetes 集群由一个或多个工作节点组成，Kubernetes 正是在这些工作节点上调度和运行 [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 。当您将 Docker 插座安装到 Pod 时，您正在将`/var/run/docker.sock`文件安装到组成 Pod 的每个容器中。当这些容器针对该套接字运行 Docker 命令时，它们实际上是由 Docker 守护进程直接执行的，该守护进程作为 **root** 在调度 Pod 的 worker 节点上运行。Kubernetes 调度程序无法跟踪这些其他容器的运行情况——它们不是由 Kubernetes 管理的，而是由运行在调度 Pod 的节点上的 Docker 守护进程管理的。这可能会导致严重的 Kubernetes 调度问题，尤其是在繁忙的 CD 集群上。首先，使用 Kubernetes 的一个主要原因是因为其强大的容器编排和调度功能——那么，为什么要在 CI/CD 中规避这一点呢？

另一个性能问题是容器影像图层缓存。如果您依赖于 Docker 守护进程提供的内置缓存，那么您可能会在不同的 K8s 节点上完成同一容器映像的不同构建，或者共享层的其他容器映像，从而否定 Docker 守护进程在特定 K8s worker 节点上提供的缓存。

此外，存储在主机上的任何层，以及通过 Docker 套接字运行的容器生成的任何日志，都不会自动为您清除。

# 安全性

简而言之，提高安全性与减少攻击面或攻击媒介密切相关。和 Kubernetes 上的 CD 没什么区别。如果 Docker 套接字暴露给一个 CD 作业，那么好奇/恶意的开发人员可以修改该作业，以运行 Docker 命令作为构建步骤，有可能成为该作业所在节点上的`root`。如果他们以`root`的身份获得对底层主机的访问权限，有相当简单的方法来提升权限并获得对整个 Kubernetes 集群的访问权限。许多集群操作员没有适当的监控来正确地检测这种活动，并将其与合法的 CD 作业运行分开，因此这种利用可能暂时不会被注意到。

**DinD** 一直[要求运行 DinD](https://blog.docker.com/2013/09/docker-can-now-run-within-docker/) 的容器启用 `[--privileged](https://blog.docker.com/2013/09/docker-can-now-run-within-docker/)` [标志——而这一直被认为是不安全的。但是安装 Docker 套接字从来没有这么安全过，它依赖于使用专用的 Docker 守护进程实例来将 CI/CD 工作负载与其他容器工作负载(例如生产应用程序)隔离开来。](https://blog.docker.com/2013/09/docker-can-now-run-within-docker/)

虽然这对于仅执行 CI/CD 的单用途集群来说是很高的，但是对于具有多个应该具有隔离的工作负载的 K8s 集群来说，这是非常高的——这种隔离应该来自 K8s 上运行的容器。例如，您的**生产**容器可能只是运行 CD 作业的名称空间的一个名称空间。

如果沿着这条路走下去，最终结果是*任何可以修改 CD 作业的人都有办法成为整个集群的 root*。

对于一些人来说，在 Kubernetes 上安装 CD Docker 插座的缺点非常明显，以至于他们[建议回到 DinD](https://applatix.com/case-docker-docker-kubernetes-part-2/) 。但是我们已经放弃了在 Kubernetes 上安装 CD 的方法。在我们寻找替代方案之前，我们将探索 K8s 的一些特性，这些特性允许您阻止在 K8s 集群(或集群的一部分)上运行的所有容器使用 DinD 和安装 Docker 套接字。

通过将 Kubernetes 用于动态短暂 CD 执行器，您可以在专门构建的容器中直接运行您的每个 CD 步骤，并单独用于执行该步骤，所有这些都在由 Kubernetes 管理的 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 中。除了协调这些 CD Pods 的调度和运行，K8s 还允许管理其生命周期的其他方面，以包括 pod 规范的安全敏感方面，从而实现 pod 创建和更新的细粒度授权。

用于管理 pod 安全性的本地 K8s 解决方案是 [Pod 安全策略准入控制器](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#podsecuritypolicy)。Pod 安全策略(PSP)可以以这样的方式配置，即如果使用正确配置的策略在 Pod 中运行的容器被配置为安装 Docker 套接字，则不会被调度，并且不允许作为`--privileged`容器运行，从而禁用 DinD。

# 使用 Pod 安全策略阻止 DinD 并安装 Docker 插座

Pod 安全策略准入控制器是增强 K8s 集群安全性的[关键特性。Pod 安全策略准入控制器允许您指定 Pod 安全策略，以限制允许容器执行的操作。如果 Pod 中的容器被配置为执行 Pod 安全策略不允许的操作，那么 K8s 将不会调度该 Pod。](https://kubernetes.io/blog/2018/07/18/11-ways-not-to-get-hacked/#6-use-linux-security-features-and-podsecuritypolicies)

[*来自 Kubernetes 官方文档*](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy) *:*

> pod 安全策略是控制 Pod 规范的安全敏感方面的群集级资源。PodSecurityPolicy 对象定义了一组条件，pod 必须在这些条件下运行才能被系统接受，还定义了相关字段的默认值

让我们来看看一些具体的设置，它们将大大减少*攻击*面，从而降低风险:

*   `privileged`:设置为`false`将禁止使用 DinD。
*   `runAsUser`:将此项设置为`MustRunAsNonRoot`，这样容器就不能以`ROOT`用户的身份运行。

*注意:您将需要允许* `*USER root*` *使用 Kaniko 做任何有意义的事情来构建和推送容器映像，因此您很可能需要将* `*runAsUser*` *设置为* `*RunAsAny*` *。Kaniko PSP 的目标是减少其他可用的攻击媒介。*

*   `allowPrivilegeEscalation`:禁用特权提升，这样容器的子进程就不会获得比其父进程更多的特权。
*   `volumes`:通过指定[特定卷类型](https://kubernetes.io/docs/concepts/storage/volumes/)和不允许任何 CD 容器的`hostPath`卷，不允许将主机目录/文件挂载为卷。这将禁用安装 Docker 插座的功能。
*   PSP `annotations`:通过`seccomp.security.alpha.kubernetes.io/defaultProfileName`注释将所有 Pod 容器限制在`runtime/default` **seccomp** 配置文件中，并且不要设置`seccomp.security.alpha.kubernetes.io/allowedProfileNames`，这样默认设置就无法更改。

这里有一个限制性 **PSP** 的例子，它不允许**安装 Docker 插座**，也不允许 **DinD** :

```
apiVersion: policy/v1beta1kind: PodSecurityPolicymetadata:name: cd-restrictedannotations:seccomp.security.alpha.kubernetes.io/defaultProfileName:  'runtime/default'apparmor.security.beta.kubernetes.io/defaultProfileName:  'runtime/default'spec:privileged: false# Required to prevent escalations to root.allowPrivilegeEscalation: false# This is redundant with non-root + disallow privilege escalation,# but we can provide it for defense in depth.requiredDropCapabilities:- ALL# Allow core volume types. But more specifically, don't allow mounting host volumes to include the Docker socket - '/var/run/docker.sock'volumes:- 'configMap'- 'emptyDir'- 'projected'- 'secret'- 'downwardAPI'# Assume that persistentVolumes set up by the cluster admin are safe to use.- 'persistentVolumeClaim'hostNetwork: falsehostIPC: falsehostPID: falserunAsUser:# Don't allow containers to run as ROOTrule: 'MustRunAsNonRoot'seLinux:# This policy assumes the nodes are using AppArmor rather than SELinux.rule: 'RunAsAny'supplementalGroups:rule: 'MustRunAs'ranges:# Forbid adding the root group.- min: 1max: 65535fsGroup:rule: 'MustRunAs'ranges:# Forbid adding the root group.- min: 1max: 65535readOnlyRootFilesystem: false
```

# **不要运行 Docker**

**不允许 DinD 或安装 Docker 套接字的另一个令人信服的方法是不要将 Docker 用作 K8s 集群的容器运行时。**

**[**containerd**](https://containerd.io/) 是上面提到的 OCI 映像运行时的一个实现(也是由 Docker 捐赠给 CNCF 的[),并且(在撰写本文时)](https://blog.docker.com/2017/03/docker-donates-containerd-to-cncf/)[由 Google Kubernetes 引擎(GKE)](https://cloud.google.com/kubernetes-engine/docs/concepts/using-containerd) — *支持，但没有其他主要的云提供商*。通过使用由 **containerd** 提供的 OCI 运行规范，您实际上并不需要 Docker——并且您实际上会看到带有 **containerd** 的 K8s 容器有更好的性能，因为 Docker 实际上在幕后使用了**container d**—导致了额外的守护进程和不必要的通信开销。**

****containerd** 的一个有趣的方面是它只是容器的运行时——它不支持构建容器映像。但是除了更好的性能之外， **containerd** 的一大优点是，它使使用 **DinD** 或安装 Docker 套接字变得不可能——因此提供了更安全的容器运行时。**

**但是，如果没有 Docker，如何构建容器映像呢？**

**或者更具体地说，在没有 Docker **守护进程**的情况下构建容器映像——没有 **DinD** ，没有 Docker 套接字。我们仍然希望利用 K8s 托管的 CD 容器，包括构建和推送容器映像。我们如何做到这一点？**

# **进入 Kaniko**

**Kaniko 是一个能够在没有 Docker 守护进程的情况下构建和推送容器映像的工具，但从安全角度来看，它有一个主要缺点:要轻松构建任何有用的东西，您必须在 Kaniko 容器中作为`root` `USER`运行。**

**[***来自 Kaniko 的文档***](https://github.com/GoogleContainerTools/kaniko#security) *:***

> ***如果你有一个最小的基础映像(SCRATCH 或者类似的)不需要权限就可以解包，并且你的 Dockerfile 没有以 root 用户的身份执行任何命令，那么你可以在没有 root 权限的情况下运行 Kaniko。需要注意的是，Docker 默认以 root 身份运行，所以您仍然需要(在某种意义上)特权来使用 Kaniko。***
> 
> ***通过在 PodSecurityPolicy 上设置带注释的 seccomp 配置文件来创建或更新集群上的安全策略，您可以实现 Docker 在 Pod 中使用的相同默认 seccomp 配置文件。***

**如上所述，以`root`身份运行是一种攻击媒介，许多人认为这是一个不可接受的安全漏洞——但使用 Pod 安全策略将减少作为 K8s Pod 一部分运行的 Kaniko 容器的攻击面，并提供比我们已经放弃的基于 Docker 的方法更高的安全性。**

**下面的配置假设您已经启动并运行了一个 K8 集群，并且您可以访问`kubectl`来添加和修改 K8s 资源。**

1.  ****容器注册中心**:我建议 Kaniko 建立一个暂存或沙盒容器注册中心，Kaniko 不能将图像推送到这个注册中心。一旦针对容器映像成功运行了所需的测试/扫描/策略，应使用单独管理和安全的 CD 作业将容器映像从暂存提升到生产。当然，Kaniko 容器映像本身(以及 CD 作业使用的任何其他容器映像)应该总是从生产容器注册表中取出。**
2.  **`PodSecurityPolicy`:一旦启用了 PodSecurityPolicy 许可控制器，您将需要至少两个 PodSecurityPolicy(实际上，您将希望在启用许可控制器之前定义并应用所有的 Pod 安全策略，否则将会阻止在集群中创建任何 Pod)。**
3.  **使用 Kaniko 执行构建和推送的最严格的策略——上面对 PSP 的唯一更改是将`runAsUser`更改为`RunAsAny`,以允许在 Kaniko 构建的 Dockerfiles 中使用 ROOT 用户。**
4.  **一个[特权策略](https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/policy/privileged-psp.yaml),对于使用 Pod 安全准入控制器的 Pod，它相当于不使用 Pod 安全准入控制器。**
5.  **`Namespace`、`Service Account`、`Role`、`RoleBinding`:PodSecurityPolicy 应用于 K8s `Role`，K8s`Role`通过`RoleBinding`绑定到`ServiceAccount`。我建议专门为 Kaniko/CD 作业创建一个绑定到具有限制性 PodSecurityPolicy 的`ServiceAccount`。**

**Kaniko 是 Google 赞助的一个项目，因此在 GKE 集群中使用 Kaniko 自然会得到很好的支持。我[遵从 GKE + GCR](https://github.com/GoogleContainerTools/kaniko#running-kaniko-in-a-kubernetes-cluster) 的 Kaniko 指令。**

**[对 GKE 使用 Pod 安全策略。](https://cloud.google.com/kubernetes-engine/docs/how-to/pod-security-policies)**

**Kaniko 指令告诉您用您的`~/.aws/credentials`创建一个 Kubernetes 秘密，将容器图像推送到 ECR，但是大多数组织不允许您以这种方式使用 AWS 凭证。**

**这里是 [Kaniko 推送到 AWS ECR](https://github.com/GoogleContainerTools/kaniko#pushing-to-amazon-ecr) 的指令。**

**将 Kaniko 与 EKS 和 ECR 一起安全使用的另一种方法是将 worker node IAM 角色与专用于 Kaniko/CD 的实例组一起使用。**

**[对 EKS 使用 Pod 安全策略。](https://aws.amazon.com/blogs/opensource/using-pod-security-policies-amazon-eks-clusters/)**

**在这篇文章发表的时候，Kaniko 还没有对 Azure 容器注册中心(ACR)的官方支持——但这并不意味着这是不可能的。Kaniko GitHub 资源库中有一个[未解决的问题](https://github.com/GoogleContainerTools/kaniko/issues/425)，其中包括一些关于从 Kaniko 向 ACR 推送容器图像的技巧。**

**[通过 AK 使用 Pod 安全策略。](https://docs.microsoft.com/en-us/azure/aks/use-pod-security-policies)**

**Jenkins X 允许您[启用 Kaniko 作为默认方式，为您的所有 Jenkins X CD 作业构建和推送容器映像](https://jenkins-x.io/getting-started/create-cluster/#the-jx-create-cluster-gke-process)，并将自动配置为推送至您安装 Jenkins X 和 Kaniko 缓存的云的默认容器注册表。这导致快速、安全的容器映像构建被推送到默认的 Jenkins X 容器注册表。**

****重要提示:**根据[此 GitHub 问题](https://github.com/jenkins-x/jx/issues/1074)的追踪，Jenkins X 没有 OOTB 对 Pod 安全策略的支持。在我的下一篇文章中，我们将看看在 Jenkins X 上使用 Pod 安全策略——但不仅仅是针对 Kaniko，因为一旦您启用了 Pod 安全策略，每个 K8s `Role` / `ClusterRole` *都必须有一个与之相关联的 Pod 安全策略。***

*   **需要以`ROOT`的身份运行 Kaniko 容器来执行大多数容器构建**
*   **无法与所有`Dockerfiles`一起工作，但会不断改进**
*   **比以前的`docker build`稍微复杂一点**

**强制使用特定的容器注册中心——例如，不允许从 DockerHub 这样的公共容器注册中心获取或向其推送。您应该维护自己的容器映像和容器注册表，包括至少有两个不同的容器注册表，一个用于 CD 容器映像(以及中间应用程序容器映像)，另一个用于生产批准的应用程序容器映像。实际上，我会更进一步，使用第三个容器注册表(类似于沙箱),它允许 CD 特定的容器映像和中间应用程序容器映像，这些映像尚未被批准用于更安全的环境，如生产环境，但可以在不太安全的测试环境中运行。**

**在使用容器图像之前，请对其进行扫描。**

**这里有一篇关于使用带有 Jenkins 管道的 Anchore 的博客文章。它的设置非常简单，因此没有理由不扫描您的容器图像，并将扫描作为提升到更安全的容器注册的大门。**

**其他解决方案允许您使用基于 Kubernetes Pod 的容器构建和推送容器映像，并且不依赖于 Docker 守护进程:**

*   **[img](https://github.com/genuinetools/img) : **img** 才 0.5.7 版本(2019 . 5 . 3 发布)，但是这个项目很有前景。 **img** 需要[上游补丁](https://github.com/genuinetools/img#upstream-patches)到不同的项目，最终应该使它成为从 K8s Pod 容器构建和推送容器映像的最安全(和快速)的方式。**
*   **[jib](https://github.com/GoogleContainerTools/jib) —另一个 Google 项目， **jib** 支持在没有 Docker 守护进程的情况下构建和推送容器图像。**

**如果你还没有，开始用 Kubernetes 上的 Kaniko 构建你的容器映像——使用 Pod 安全策略。**

**同样，如果你想让 Kaniko 快速、轻松地运行，那么你应该签出 Jenkins X. Jenkins X 将自动设置 Kaniko，并进行必要的配置，以将容器映像推送到你选择的 Docker 注册表(GCR、ECR、Docker Hub 等)。使用 Jenkins X，您可以在几分钟内从 GitHub 中的 docker 文件转换为注册表中的容器图像。**
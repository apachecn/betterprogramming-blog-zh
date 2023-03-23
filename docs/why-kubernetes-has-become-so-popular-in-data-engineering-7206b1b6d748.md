# 为什么 Kubernetes 在数据工程领域如此受欢迎

> 原文：<https://betterprogramming.pub/why-kubernetes-has-become-so-popular-in-data-engineering-7206b1b6d748>

## 这是一件好事吗？

![](img/ff72ff13c8a78f1c96eeb41ae944124c.png)

照片由 [Lukas Hartmann](https://www.pexels.com/@lukas-hartmann-304281?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 从 [Pexels](https://www.pexels.com/photo/ship-with-lights-on-concrete-platform-1719475/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) | [品牌内容披露](https://www.annageller.com/disclosure)拍摄。

容器已经成为将数据项目转移到生产中的事实上的标准。不再有依赖性管理的噩梦——在本地机器上开发的项目可以毫无意外地“运送”到登台和生产集群。数据管道和 ML 模型最终是可复制的，并且可以以相同的方式在任何地方运行。

然而，随着容器化数据工作负载数量的不断增长，编排平台变得越来越重要。

# 1.编排容器

如果您想运行可复制的数据管道和可以在任何地方运行的 ML 模型，您可能知道 Docker 映像是一种不错的选择。从软件工程师到 DevOps 和 SREs，每个人都喜欢容器图像。

过去，挑战在于大规模运行集装箱。有了 Kubernetes——尤其是部署到弹性云服务时——大规模管理容器的执行变得不那么痛苦了。

# 2.团队之间的沟通

尽管我们中的许多人喜欢构建端到端的数据产品，但实际上，许多企业在谁构建数据产品和谁运行数据产品之间划分了责任。构建数据管道后，数据科学家和工程师通常必须将这些数据交给 DevOps 和 SREs 进行部署和监控。容器映像使得这个移交过程更加容易。

同时，容器促进了数据工程师、数据科学家和分析师之间的代码共享和协作，他们可以在同一个可再现的环境中工作。

# 3.声明性定义

如今，数据工程工作负载本质上是动态的。假设您将数据管道部署到一个 Kubernetes 集群，现在由于内存不足而失败。解决这个问题可能需要增加声明性部署文件中的内存大小，并将更改应用到集群。

# 4.证明文件

工作流和配置作为代码的一个很好的“副作用”是一切都被记录下来——不仅仅是业务逻辑，还有执行细节，比如事情在哪里运行以及它们消耗了哪些资源。当其他工程师需要处理您的代码时，不需要猜测和来回沟通就能让他们跟上您的数据工作负载。

# 5.版本控制

声明式工作流和环境定义的另一个好处是一切都可以进行版本控制。如果出现问题，您可以恢复到以前的版本。您可以跟踪一段时间内对环境所做的更改，并提供合规性审核日志。GitOps 和 MLOps 使这种方法流行起来，但是容器和编排平台有效地使之成为可能。

# 6.维护执行层的健康

Kubernetes 最突出的优点之一是，它总是试图维护所需的状态，并在资源耗尽时重启或重新创建资源。当然，Kubernetes 的自我修复“能力”不会修复您的所有问题(如业务逻辑中断)，但至少当由于临时网络问题而发生错误时，您不需要干预，该问题可以通过重启或重新部署到新的 pod 来解决。

# 7.随着数据的增长，无缝扩展您的执行平台

如今，在一台服务器上运行数据管道可能无法满足您的需求。随着数据量的增长，管理数据处理变得越来越困难——不管我们是将代码打包成容器映像还是在单个本地进程中运行它。

如果您需要跨多个节点扩展您的工作负载，Kubernetes(尤其是与 Helm charts 结合使用)可以更轻松地在计算集群上安装 Dask 或 Spark，从而跨多个节点分布数据处理。大多数云提供商提供自动扩展服务，甚至提供完全无服务器的数据平面(法盖特的[AWS EKS](/serverless-kubernetes-cluster-on-aws-with-eks-on-fargate-a7545cf179be)和 [GCP GKE 自动驾驶](https://cloud.google.com/blog/products/containers-kubernetes/introducing-gke-autopilot))。这些云供应商负责在需要时横向扩展工作节点，从而完全消除了猜测所需容量的需要。

尽管有这么多好处，Kubernetes 不会让您立即看到数据工作负载中发生了什么，但它会让您更容易安装分布式计算引擎，并无缝地将其扩展到多个节点。

# 8.开发和生产环境对等

构建数据产品时的一个常见挑战是，开发环境通常与生产环境中的运行方式大相径庭。容器化的工作负载使这种转变变得更加容易。对于登台和生产环境，您可能有不同的集群或名称空间。它们之间的切换应该是无缝的。

# 9.迭代更快

迭代对于构建数据工作负载至关重要。大部分数据产品一开始都是不好的。您可能需要几个周期来清理和转换数据，测试各种分类器和超参数，或者用新数据丰富模型。Kubernetes 部署允许您实现 A/B 测试，或者运行同一个 ML 培训作业的多个实例，但是使用不同的超参数。

真正证明利用 Kubernetes 可以让你迭代得更快的是当你把 Kubernetes 和抽象出底层细节的工具结合起来的时候。例如:

*   为了构建和扩展你的数据流，你可以在 Kubernetes 上利用[perfect](https://www.prefect.io/)和 [Dask。](https://kubernetes.dask.org/en/latest/)
*   为了服务 ML 模型或者执行 A/B 测试，你可以利用[塞尔顿](https://docs.seldon.io/projects/seldon-core/en/latest/)。
*   要构建可视化、洞察力、度量和 KPI，您可以使用[GoodData.CN](https://www.gooddata.com/developers/cloud-native-community-edition/?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-k8s)。

# 10.安装几乎任何软件与舵图表和进一步的功能

Kubernetes 已经变得如此普遍流行，以至于已经构建或重新设计了许多工具来处理 K8s 集群，并且您通常可以使用 Helm chart 存储库来安装它们。在上面的列表中，我们只是触及了表面。它应该只展示您可以利用这个容器编排平台来构建所有的数据工作负载——执行 ETL 作业、训练和服务 ML 模型，甚至构建和托管可视化。

![](img/6d19c90dd743ada0eda1bac685e7aec4.png)

照片由 [Pexels](https://www.pexels.com/photo/photo-of-person-pointing-on-laptop-3183171/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的[faxels](https://www.pexels.com/@fauxels?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄。

# 共同的主题

上述各点的共同点是，容器(和容器编排平台)允许我们:

*   构建可重复的代码，这些代码可以在任何地方、任何规模下以相同的方式运行。
*   减少不同团队之间交接时的摩擦。
*   将现代软件工程实践应用于数据工作负载，包括 GitOps、DevOps 和 MLOps。

如果你想让你的数据可视化遵循这种范式，GoodData 最近推出了云原生平台[GoodData.CN](https://www.gooddata.com/developers/cloud-native-community-edition/?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-k8s)，你可以安装到你的 Kubernetes 集群。您可以从运行一个单映像 Docker 容器来本地开发仪表板、指标和 KPI 开始(以一种[干法](https://towardsdatascience.com/can-we-apply-the-dry-principle-to-business-intelligence-68c4cfe25669)！):

```
docker run --name gooddata -p 3000:3000 -p 5432:5432 \\
  -e LICENSE_AND_PRIVACY_POLICY_ACCEPTED=YES gooddata/gooddata-cn-ce:latest
```

一旦您在本地开发了您的仪表板、指标和洞察，您就可以导出您的声明性定义并将它们部署到生产 Kubernetes 集群。GoodData 团队向[提供了详细的文档](https://www.gooddata.com/developers/cloud-native/doc/1.1/installation/k8s/?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-k8s)，展示了如何在各种场景下安装他们的软件:

*   [采用 RDS Postgres 数据库和 ElastiCache for Redis 的 AWS EKS](https://www.gooddata.com/developers/cloud-native/doc/1.1/installation/k8s/environment/aws/?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-k8s)，
*   [GCP GKE 为 Postgres 提供云 SQL，为 Redis 提供 MemoryStore】，](https://www.gooddata.com/developers/cloud-native/doc/1.1/installation/k8s/environment/gcp/?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-k8s)
*   [Azure AKS 用 Azure 数据库做 Postgres，用 Azure 缓存做 Redis](https://www.gooddata.com/developers/cloud-native/doc/1.1/installation/k8s/environment/azure/?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-k8s) ，
*   [内部部署](https://www.gooddata.com/developers/cloud-native/doc/1.1/installation/k8s/environment/on-premise/?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-k8s)，
*   以及许多关于如何[管理组织、工作空间和数据源的有用提示](https://www.gooddata.com/developers/cloud-native/doc/1.1/administration/?utm_source=mediumcom&utm_medium=referral&utm_campaign=gdcn&utm_content=anna-k8s)。

# 结论

在本文中，我们研究了 Kubernetes(以及一般的容器编排平台)的特性，这些特性使得容器在数据团队中如此普遍地流行。与 K8s 集成的工具数量之多，以及它在每个云平台中的存在，使其成为可再生和可扩展数据工作负载的全面执行层。

感谢您的阅读！
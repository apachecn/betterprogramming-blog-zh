# AWS 上的无服务器 Kubernetes 集群和 Fargate 上的 EKS

> 原文：<https://betterprogramming.pub/serverless-kubernetes-cluster-on-aws-with-eks-on-fargate-a7545cf179be>

## 为什么它会改变游戏规则，以及如何使用它

![](img/b2e1872b610e1489863213926063a78e.png)

照片由[像素](https://www.pexels.com/photo/abstract-art-blur-bright-373543/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[皮克斯拜](https://www.pexels.com/@pixabay?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

容器化的应用程序越来越受欢迎。集装箱技术公司(a.o. Kubernetes)预计 2022 年的市场容量将超过 40 亿美元，2017 年至 2022 年的年增长率为 30%[6]。

最流行的大规模运行容器的平台之一是 Kubernetes，
,这是一个功能丰富的开源编排系统，允许自动化容器的整个生命周期，包括应用程序部署和监控它们的健康状况。尽管有这些好处，Kubernetes 可能会增加额外的复杂性，因为它需要专用的 DevOps 资源来保持集群健康并确保其可伸缩性。

AWS 多次证明了他们是以客户为中心的，他们希望改善开发者的体验。随着无服务器 Kubernetes 服务 Fargate 上 AWS EKS 的推出，他们再次证明了这一点，同时推出了一项服务，该服务在大规模运行容器化应用程序方面改变了游戏规则。

# 法尔盖特和库伯内特背后的故事

直到最近，一直有许多尝试将无服务器应用程序引入 Kubernetes，但我见过的大多数框架都专注于将无服务器功能(功能即服务)部署到现有的 Kubernetes 集群，而不是提供一个云服务来自动提供 Kubernetes 工作节点(数据平面)以运行无服务器容器。

2019 年 12 月，AWS 推出了一项新服务:Fargate 上的 EKS，为 Kubernetes 集群提供无服务器数据平面。理论上，它不被认为是一个独立的服务，而是两个现有服务的混合，EKS(Kubernetes 的 AWS 实现)和 ECS Fargate(无服务器 AWS 特定容器编排平台)。

在 Fargate 上的 EKS 之前，弹性 Kubernetes 服务(EKS)让我们享受到了 Kubernetes 的好处，但它仍然需要额外的工作来维护数据平面，即在 Amazon EC2 上运行的工作节点。相比之下，Fargate 是完全无服务器的，它提供了一个抽象层，允许部署容器化的工作负载，而无需我们维护任何工作节点。

## 法盖特的 ECS vs 法盖特的 EKS

虽然 Fargate 的抽象由于完全管理的无服务器架构的性质而节省了大量时间，但它需要学习特定于 AWS 的词汇，a.o. `task definition`和`service definition`。这些概念(主观上)不像 Kubernetes 对象的声明性语言那样直观。

此外，当在 Fargate 上使用纯 ECS 时，我们在某种程度上变得依赖 AWS，因为这项服务只能由 AWS 提供，这种依赖单一提供商的模式通常被称为供应商锁定。

相比之下，当在 Fargate 上使用 EKS 时，我们可以很容易地切换到任何其他云提供商或内部 Kubernetes 集群，因为我们的 pod 和部署在任何 Kubernetes 上都以相同的方式工作。每个云供应商使用的存储类别或负载平衡器有所不同，但总的来说，Kubernetes 就是 Kubernetes，不管您在哪里运行 pods。此外，目前市场上更多的人了解如何使用 Kubernetes，而不是那些了解 Fargate 的人。

使用 Kubernetes-native API 监控和管理容器化工作负载的健康和状态同样更加容易。这可能是我的主观看法，但是通过`kubectl`命令，直接从终端与 Kubernetes API 交互，似乎比通过 AWS 管理控制台或 AWS CLI 与 Fargate 交互更方便。

最后，Kubernetes 通过 helm charts 打开了整个可部署应用程序的世界，例如部署 Dask 分布式集群，这在纯 ECS Fargate 中要困难得多(如果不是不可能的话)。

这就是为什么 AWS 将两种服务混合在一起，让我们享受两个世界的好处，这是一个巨大的改变:

*   完全管理的控制平面，让我们通过`kubectl`和部署与舵图打包的应用程序与 Kubernetes API 进行交互
*   完全托管的无服务器数据平面，允许快速部署我们的容器化工作负载，而无需管理和扩展底层计算能力。

# EKS 对法盖特有何启示？

让 Fargate 不仅作为 ECS 的指挥，而且作为 EKS 的指挥，改变了我们所认为的基本工作单位和责任单位[1]。对于纯 EKS，我们的工作单位是 EC2 实例，收费单位是每小时的实例价格。相比之下，Fargate 上的 EKS 使用 pod 作为工作单位，并将 pod 使用的 vCPU 和内存量作为收费单位。我们在 AWS 上运行 Kubernetes 控制平面的费用仅为每小时 0.10 美元，除此之外，我们只为我们的 pods 消耗的 CPU 和内存资源付费。

这对于各种计费场景特别有吸引力[5]。假设你是一名自由开发人员，同时为三个不同的客户工作。您可以为这些项目使用不同的名称空间或不同的标签选择器，AWS 会为您提供详细的账单信息，以便您可以轻松地分析您在每个项目上的花费。如果您的公司想要跟踪特定项目的成本，这同样适用。

这种设置对那些无法提前猜测所需计算能力增长速度的初创公司也很有吸引力。他们有时可能没有任何工作负载在运行，这需要为 Kubernetes 的正常设置支付闲置资源的费用。

## 其他好处

上面提到的一个明显的好处是成本。就成本而言，我指的不仅仅是每个计算资源的成本，还包括维护 Kubernetes 集群所需的专业员工的成本。在撰写本文时，DevOps 资源很少。许多公司没有足够的工程师来做这类工作，他们也很难找到有经验的人来做。

关于计算资源，您可以额外考虑 AWS 计算节约计划，该计划允许您在一至三年内承诺使用 Fargate、EC2 或 Lambda 时进一步削减高达 52%的计算成本[5]。

拥有一个无服务器的 Kubernetes 集群意味着我们不再需要维护工作节点，并且我们不必配置任何自动扩展组来扩展我们的工作节点，Fargate 为我们处理所有这些，我们可以将我们稀缺的 DevOps 资源集中在其他问题上。

这意味着我们可以花更多的时间来增加我们的业务价值，即通过利用`kubectl`和通用 YAML 语法来配置容器和部署，来编程和部署我们的容器化应用。

从安全角度来看，一个额外的好处是每个 pod 最终运行在一个单独的微型 VM 中。这样，如果有人未经授权访问我们的一个吊舱，他们就无法访问任何其他吊舱，因为部署在 Fargate [3]上的吊舱之间完全隔离。由于这种隔离，每个 pod 都有自己的弹性网络接口(ENI)和自己的安全组(SG)。相比之下，部署在传统 EC2 数据平面上的所有 pod 共享工作节点的 ENI。

## 它打开的用例

到目前为止，运行一个 Kubernetes 集群需要大量的知识，而且非常困难。现在，通过一个简单的`eksctl`命令就可以启动整个生产就绪集群(可以自动扩展), Kubernetes 可以用于:

*   **数据科学:**使用 Dask 并行运行几个实验，
*   **数据工程:**在工作流管理平台中用作执行层，如 Prefect 或 Apache Airflow
*   **Web 开发:** AWS EKS 与应用程序负载平衡器集成良好
*   **计算资源的** **分离:**针对不同的团队和项目。

## 无服务器数据平面的缺点是什么？

使用几乎任何无服务器平台的一个缺点是，在容器(或您的 K8s pod)进入运行状态之前，与编排引擎需要分配和准备计算资源(例如，将最新版本的映像从容器注册表拉至分配的工作节点并构建映像)的时间相关的潜在延迟问题。

如果您的工作负载无法接受这种延迟，那么使用亚马逊 EKS 集群和传统数据平面可能更适合您的使用案例。但是，你可以两者兼得！

## 混合无服务器数据平面和传统 EC2 工作节点

AWS 允许混合这两种选择！通过这种方式，我们可以在同一个集群中为某些用例提供无服务器数据平台，并为低延迟工作负载持续运行 Kubernetes 工作节点。

这种无服务器和非无服务器工作节点的分离是通过使用不同的名称空间来实现的，并且还可以选择为两者使用不同的标签。

*   您的无服务器工作负载将使用 Farate 配置文件中定义的 Kubernetes 名称空间，该配置文件默认指定在默认名称空间中创建的所有资源都将被部署到 Fargate 调度程序。
*   如果您的 pod 规范定义的名称空间不同于 Fargate 配置文件中指定的名称空间，EKS API 将安排 pod 在非无服务器工作机(即 EC2 实例)上运行。

混合使用这两种选项的可能性表明，AWS 构建这种服务时非常有远见，可以节省客户的时间、金钱、挫折和维护工作。如果您有一些需要在 GPU 上运行的 pods，例如许多数据科学用例，这将非常有用。在撰写本文时，Fargate 不支持 GPU，这就是为什么两种 worker 节点类型的组合非常有用。

控制平面不仅检查名称空间，还检查分配的标签选择器，然后决定在哪里部署特定的容器。这意味着，当您为与 Fargate 配置文件中定义的名称空间和标签不匹配的 pod 创建部署时，它将被调度到您维护的 EC2 工作节点，这些节点将能够开始运行容器，而没有与供应微虚拟机相关的延迟。

# 演示时间到了！

如果您想继续操作，请确保您拥有一个具有管理员访问权限的 AWS 帐户，或者拥有创建 ECR、EKS 和 ECS 资源的 IAM 权限的用户。此外，您应该下载并安装 [AWS CLI](https://www.notion.so/Distributed-data-pipelines-made-easy-with-AWS-and-Prefect-82c31104b9bd4009af527f3392fbb2fb) ，并使用您的 AWS 凭证(AWS 密钥+ AWS 秘密密钥)对其进行配置。最后，要使用 EKS，你要安装`eksctl`，这里介绍: [AWS docs](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html) 。

## 将您的 Docker 映像部署到 ECR

Fargate 上的 EKS 提供了与称为弹性容器注册中心(ECR)的特定于 AWS 的容器注册中心的无缝集成，它用于托管您的 Docker 映像，类似于 Dockerhub。要使用您的 ECR 帐户验证本地终端会话，请运行:

*   如果您使用新的 AWS CLI v2:

```
aws ecr get-login-password --region <YOUR_AWS_REGION> | docker login --username AWS --password-stdin <YOUR_ECR_REGISTRY_ID>.dkr.ecr.<YOUR_AWS_REGION>.amazonaws.com
```

*   如果您使用旧的 AWS CLI v1:

```
$(aws ecr get-login --no-include-email --region <YOUR_AWS_REGION>)
```

**注:** `<YOUR_AWS_REGION>`可能是 ex。美国东部-1，欧盟中部-1，以及更多[。要检查您拥有的 AWS CLI 版本，请使用`aws --version`。](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.RegionsAndAvailabilityZones.html)

如果您收到登录成功的消息，您可以创建您的 ECR 存储库，并将您的自定义图像推送到注册表。要创建新的存储库:

```
aws ecr create-repository --repository-name <YOUR_REPOSITORY_NAME>
```

现在，您只需要构建 Docker 容器，并将其推送到刚刚创建的 ECR 存储库中:

```
export REPO_URI=<YOUR_ACCOUNT_ID>.dkr.ecr.<YOUR_AWS_REGION>.amazonaws.com/<YOUR_REPOSITORY_NAME> # For instance:
REPO_URI=123456789.dkr.ecr.eu-central-1.amazonaws.com/dockerized-app# build the image locally
docker build -t $REPO_URI .# push the image to ECR
docker push $REPO_URI
```

## 用一个命令在 AWS 上创建一个无服务器的 Kubernetes 集群

现在，要实际创建集群，您只需运行一个`eksctl` 命令，这将在您的 VPC 中创建以下资源:

*   一架 Kubernetes 控制飞机，
*   用于 pod 执行的 IAM 角色，
*   法盖特侧写。

```
eksctl create cluster --name fargate-eks --region <YOUR_AWS_REGION> --fargate
```

我用`fargate-eks`这个名字来命名这个集群，你可以随意命名。
`--fargate`标志确保我们创建一个新的 Fargate 配置文件用于该集群，以便我们使用 Fargate 作为数据平面的协调器。如果您想自己创建概要文件，您可以跳过`--fargate`标志，在集群创建完成后，在管理控制台中自己创建概要文件。或者，您可以从终端完成(但是如果您使用了`--fargate` flag，就不必这么做了):

```
eksctl create fargateprofile \
  --cluster fargate-eks \
  --name my-default-fargate-profile \
  --namespace my-custom-namespace
```

创建集群和 Fargate 配置文件的过程可能需要几分钟。完成后，您应该会看到类似的输出，确认我们的集群已经准备好了。

然后，如果我们检查我们的上下文:`kubectl config current-context`，我们应该得到类似的输出:

```
<YOUR_AWS_USER_NAME>@fargate-eks.<YOUR_AWS_REGION>.eksctl.io
```

这证实了我们连接到了运行在 AWS Fargate 和 EKS 上的 Kubernetes 集群！为了进一步证明这一点，运行`kubectl get nodes`——它应该显示至少一个 Fargate 节点等待我们的 pod 部署。

```
➜  ~ kubectl get nodes
NAME                                                       STATUS   ROLES    AGE   VERSION
fargate-ip-192-168-163-163.eu-central-1.compute.internal   Ready    <none>   15m   v1.17.9-eks-a84824
fargate-ip-192-168-180-51.eu-central-1.compute.internal    Ready    <none>   15m   v1.17.9-eks-a84824
```

**注意:**这些节点在我们的 VPC 内部运行，但是在 EC2 仪表板中不可见。您不能通过 SSH 访问这些节点，因为它们完全由 Fargate 以无服务器的方式管理和部署。然而，您可以使用`kubectl exec -it <pod_name> /bin/bash`SSH 到一个特定的 pod。

为了将一些容器部署到这个集群，我们可以使用默认的`nginx`映像:

```
kubectl create deployment first-container --image=nginx
```

# 法盖特的 EKS 是如何在幕后工作的？

您可能会问:我们怎么可能将资源部署到尚未调配的节点上？AWS Fargate 向控制平面添加了额外的逻辑，以确保我们不必修改 pod 定义就能让它工作[3]。Fargate 概要文件通过使用验证和变更 webhooks，动态地为部署到 Fargate 名称空间中的任何 pod 修改 pod 定义。

如果这些 webhooks 发现 pod 中指定的名称空间和标签与 Fargate 配置文件中的名称空间和标签相匹配，那么 pod 将被发送到 Fargate 调度程序。相比之下，如果 webhooks 找不到任何匹配，pod 将被发送到标准 Kubernetes 调度程序，然后该调度程序将 pod 发送到基于 EC2 的非服务器数据平面[1]。

## 每个 pod 的资源分配

当我们定义 pod 时，我们指定容器需要多少 vCPU 和内存。这是确保适当分配资源的重要一步。我们不需要对此过于明确，但是确定我们想要设置什么限制是很有用的，这样我们的 pod 将被部署到一个具有所需 vCPU 和内存级别的实例。例如，如果您指定 4GB 内存的限制，您的应用程序最终可能会使用较少的资源，但是这样您可以确保这些 4GB 内存在微型虚拟机上可用。

> 由于群集中没有可用的工作节点，因此单元本身将调整并决定所需的底层容量[5]

确定资源分配的最简单方法是首先分配多一点的资源，然后利用以下工具监控每个 pod 使用了多少资源:

*   [Datadog](https://www.datadoghq.com/blog/eks-fargate-monitoring/) 在 Fargate 上为 EKS 提供支持，
*   开源技术，如 Prometheus 或 Grafana，如 AWS 博客文章中的[所示。](https://aws.amazon.com/blogs/containers/monitoring-amazon-eks-on-aws-fargate-using-prometheus-and-grafana/)

有关 vCPU 和内存分配的更多信息，请参见 [AWS 文档](https://docs.aws.amazon.com/eks/latest/userguide/fargate-pod-configuration.html)或观看 [re:Invent 分组会议](https://www.youtube.com/watch?v=m-3tMXmWWQw&feature=youtu.be&t=2247)的这一部分。

# 结论

在这篇博文中，我们讨论了 Fargate 上的 EKS，这是一种让我们可以在 AWS 上运行无服务器 Kubernetes 集群的服务。我们讨论了 ECS 和 EKS 在 Fargate 上的差异及其影响。我们还研究了以无服务器方式运行 Kubernetes pods 的好处以及它带来的用例。

之后，我们研究了与 Fargate 相关的可能缺点，以及 EKS 上无服务器和非无服务器数据平面的混合如何减轻这些缺点。

我们还进行了一个演示，展示了如何在 AWS 上创建一个无服务器的 Kubernetes 集群，以及如何将图像推送到 ECR 以使我们的定制图像可以被 Kubernetes API 访问。最后，我们看了这个服务是如何工作的，以及如何为每个 pod 分配资源。

我希望它能帮助您确定 Fargate 上的 EKS 是否能为您的用例工作，并开始使用它。感谢您的阅读！

# 参考

[1]在 re:Invent 2019 发布:[https://www.youtube.com/watch?v=m-3tMXmWWQw&t = 3s](https://www.youtube.com/watch?v=m-3tMXmWWQw&t=3s)

[2] AWS 博客:[https://AWS . Amazon . com/blogs/AWS/Amazon-eks-on-AWS-fargate-now-generally-available/](https://aws.amazon.com/blogs/aws/amazon-eks-on-aws-fargate-now-generally-available/)

[3] EKS 工作坊:[https://www . eks workshop . com/beginner/180 _ fargate/creating-profile/](https://www.eksworkshop.com/beginner/180_fargate/creating-profile/)

[4]Fargate 个人资料上的 AWS 文档:[https://Docs . AWS . Amazon . com/eks/latest/user guide/Fargate-profile . html](https://docs.aws.amazon.com/eks/latest/userguide/fargate-profile.html)

[5] AWS 在 Fargate 上关于计算节省计划、安全性等方面的博客:[https://AWS . Amazon . com/blogs/containers/saving-money-pod-at-time-with-eks-Fargate-and-AWS-compute-savings-plans/](https://aws.amazon.com/blogs/containers/saving-money-pod-at-time-with-eks-fargate-and-aws-compute-savings-plans/)

[6]凯文·凯西的文章:[https://enterprisesproject . com/Article/2019/7/kubernetes-statistics-13-引人注目](https://enterprisersproject.com/article/2019/7/kubernetes-statistics-13-compelling)
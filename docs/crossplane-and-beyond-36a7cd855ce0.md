# 交叉平面:作为代码的基础设施的下一件大事

> 原文：<https://betterprogramming.pub/crossplane-and-beyond-36a7cd855ce0>

## 交叉平面入门

![](img/faeeb6538048184be8b3a2dad19b570f.png)

照片由 [Philip Myrtorp](https://unsplash.com/@philipmyr?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

作为代码的基础设施已经存在很长时间了。

它来解决一个问题，并不断发展新的功能。

我们已经看到了`CFEngine`的日子。经历了厨师，木偶，Ansible 和生活在 Terraform 和普鲁米时代。

交叉平面是这一发展的下一个阶段。它带来了新的东西:检测和纠正漂移的能力。

# 一些先决条件

在我们进一步了解 crossplane 的真正功能之前，让我们先来看看如何开始使用它。

## 步骤 1:设置初始集群

Crossplane 运行在 Kubernetes 集群上，因此第一步，您需要创建一个集群。这可以小到`minikube`或 kind，因为它只是为交叉板提供接口。

这一步，在你的系统上安装`kubectl`、`helm`和`kind`。并运行:

```
kind create cluster — image kindest/node:v1.23.0 — wait 5m
```

## 步骤 2:安装交叉板

像所有 Kubernetes 托管的应用程序一样，首先创建一个单独的名称空间:

```
kubectl create namespace crossplane-system
```

接下来，安装`crossplane`图表

```
helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update
helm install crossplane --namespace crossplane-system crossplane-stable/crossplane
```

使用`crossplane-system`命名空间中的`helm list`或`kubectl get`命令检查安装是否成功。

## 步骤 3:安装交叉面板 cli

Crossplane 或多或少是`kubectl` CLI 的一个插件/包。只需使用以下工具安装即可:

```
curl -sL https://raw.githubusercontent.com/crossplane/crossplane/master/install.sh | sh
```

现在你可以像使用`kubectl crossplane ...`一样安装任何配置包。

## 步骤 4:为您想要的提供者安装配置包

在本例中，我们将使用以下命令安装 AWS 配置包:

```
kubectl crossplane install configuration registry.upbound.io/xp/getting-started-with-aws:v1.8.1
```

此配置使用默认的 VPC。如果您希望为集群创建新的 VPC，请使用`registry.upbound.io/xp/getting-started-with-aws-with-vpc:v1.8.1`。

Crossplane 与云无关，因此您可以分别对 GCP 和 Azure 使用`registry.upbound.io/xp/getting-started-with-gcp:v1.8.1`或`registry.upbound.io/xp/getting-started-with-azure:v1.8.1`。

如果你选择 Azure 或 GCP，进一步的步骤会有所不同，但你可以在这里找到它们。

## 步骤 5:配置凭据

我们已经在本地系统上准备好了`crossplane`，现在我们只需要为我们选择的提供者传递凭证，这样`crossplane`就可以与它对话。

对于 AWS，可以这样做:

```
AWS_PROFILE=default && echo -e "[default]\naws_access_key_id = $**(**aws configure get aws_access_key_id --profile $AWS_PROFILE**)**\naws_secret_access_key **=** $(aws configure get aws_secret_access_key --profile $AWS_PROFILE)" > creds.conf
```

请注意，这与您在执行 AWS CLI 登录时获得的配置类型相同。

接下来，我们将从该文件中创建一个秘密，如下所示:

```
kubectl create secret generic aws-creds -n crossplane-system --from-file=creds=./creds.conf
```

并把它传到我们的`ProviderConfig`

# 让我们开始创造

准备好置备程序后，只需应用以下清单:

并使用以下命令检查其状态:

```
kubectl get postgresqlinstance my-db
```

瞧啊。您已经准备好了一个 Postgres 实例，就这样！

从这一点来看，主要有两种方法来创建资源，使用定制资源(CRs)或复合资源(XRs)。

自定义资源方法允许您从 AWS 配置包中提供的资源定义创建资源。这是我们刚刚看到的过程。你可以在这里看到它们的定义和语法。

另一方面，复合资源旨在让您用自己的观点和 API 构建自己的平台，而无需从头开始编写 Kubernetes 控制器。

相反，当有人创建您定义的 XR 时，您定义您的 XR 的模式并教导交叉平面应该组成(即创建)哪些受管资源。你可以在这里阅读更多信息[。](https://crossplane.io/docs/v1.8/reference/composition.html)

AWS 在这里提供了这些 xr 的蓝图。让我们来看一个来自同一个回购的例子。比方说你想创建你自己的 VPC，你可以通过运行[来应用这里显示的](https://github.com/aws-samples/crossplane-aws-blueprints/tree/main/compositions/aws-provider/vpc)的内容

```
kubectl apply -f compositions/aws-provider/vpc
```

然后应用 XR:

# 基础设施的实际“管理”

这很好，我们使用基础设施作为代码(IaC)创建了一个 VPC，就像任何其他 IaC 工具一样。crossplane 有什么特别之处？此外，为什么它首先需要一个 kubernetes 集群来运行，为什么不只是一个像 Pulumi 或 Terraform(至少是开源版本)这样的 CLI 呢？

这是因为`crossplane`不仅仅是创建基础设施，它还会以这种方式维护它。

让我们通过创建另一个资源来看看它是如何做到的。这一次，EKS 集群

使用相同的 [crossplane-aws-blueprints 存储库](https://github.com/aws-samples/crossplane-aws-blueprints)并运行这些命令(您可能希望更新一些细节，如子网 id)

```
kubectl apply -f compositions/aws-provider/ekskubectl apply -f examples/aws-provider/composite-resources/eks/eks-claim.yaml
```

我们在这里部署的配置是这样的

这里，受管节点组被设置为至少有 2 个节点。尝试直接从 AWS 控制台将其更新为 3。

您将看到它创建了第三个节点，但很快配置又回到 2，第三个节点被终止。

交叉平面不允许对资源进行任何手动更改。如果进行了任何更改，它会将其恢复到原始/所需的配置。

# 释放全部力量

如果我说您可以在集群管理交叉板上再安装一个工具，这将使您的生活更加轻松，那会怎么样？

Crossplane 与我在之前的一篇博客中提到的另一个工具密切相关

[](https://rajputvaibhav.medium.com/argo-cd-vs-flux-cd-right-gitops-tool-for-your-kubernetes-cluster-c71cff489d26) [## Argo CD 与 Flux CD —适合 Kubernetes 集群的 GitOps 工具

### 采用 GitOps 架构需要正确的工具，这个故事将指导您做出最佳选择

rajputvaibhav.medium.com](https://rajputvaibhav.medium.com/argo-cd-vs-flux-cd-right-gitops-tool-for-your-kubernetes-cluster-c71cff489d26) 

将 Crossplane(它使您的基础设施与代码保持同步)与 ArgoCD(它跟踪您的 Git repo)这样的 GitOps enabler 结合起来，意味着您提交到存储库的任何更改都会反映到您的基础设施上。

> **有趣的事实**
> 也有一个安装 argocd 的交叉面板提供商。点击查看[。](https://github.com/crossplane-contrib/provider-argocd)

# 这不是它

有更多的工具可以插入到这个架构中。以`KubeVela`为例。

![](img/fcb70ed8f84e9f85b78c2449e66d722e.png)

总而言之，KubeVela 允许您将部署计划声明为工作流，使用任何 CI/CD 或 GitOps 系统自动运行它，并使用 CUE 扩展或重新编程工作流步骤。

虽然这是对 KubeVela 能力的低估，但这应该是一个好的开始。

作为运营成员，您可以使用 Crossplane 为基础架构定义供应器，并使用 KubeVela 设置 CI/CD 流程。

最后，创建一个 Git 存储库和一个 ArgoCD 应用程序来跟踪它。

现在，您的开发人员能够调配基础架构并在其上部署他们的应用程序，只需对几乎所有组件进行抽象即可。

他们不需要知道 IaC 或 Kubernetes 是如何工作的，他们只需要理解一个单独的`YAML`文件的语法。

# Kubernetes 友好

我在这篇博客中添加 ArgoCD 和 KubeVela 的原因只是为了帮助您理解`crossplane`并不只是带来漂移检测和自动同步。

如果您能很好地设计它，并将其与该领域中可用的其他工具结合起来，您将会理解 Crossplane 的真正价值。

# 离别赠言

交叉平面带来了 IaC 的新发展。

到目前为止，只有通过定制代码和内部框架才能实现不仅仅是创建，而是保持基础设施配置的能力。

现在，每个人都可以使用它，我希望它能很快成为其他 IaC 遵循的市场标准。
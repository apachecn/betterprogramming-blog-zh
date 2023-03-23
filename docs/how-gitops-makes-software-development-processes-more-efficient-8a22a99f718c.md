# GitOps 如何提高软件开发过程的效率

> 原文：<https://betterprogramming.pub/how-gitops-makes-software-development-processes-more-efficient-8a22a99f718c>

## 强大的 GitOps 概念通过自动化加速开发

![](img/5c17ff6262f8d883a45a68b93ad57be0.png)

[杰克单桅帆船](https://unsplash.com/@jacksloop?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

根据 Puppet 的"【2020 年 DevOps 报告状态，"超过 40%的高级 DevOps 公司已经在使用 [GitOps](http://seekoapp.io/61583362efc5be000997fbc8) 概念来启用"自助式开发团队"

新方法的使用提高了 IT 运营的效率并加快了流程。值得注意的是，DevOps 方法代表了一个重大进步，因为它允许开发人员和 It 运营人员更紧密地合作。这种方法只会影响企业文化。只有几个具体的例子。[持续集成](http://seekoapp.io/61583360efc5be000997fbc4) (CI)或[持续交付](http://seekoapp.io/61583361efc5be000997fbc6) (CD)是自动化流程的两个最佳实践。

GitOps 是一种完全不同的方法，用于自动化软件应用程序的操作，使 IT 操作的任务对开发团队来说很容易获得。

## 什么是 GitOps？

这种方法依赖于目标状态的声明性描述，通过特殊软件不断与实际状态进行比较。

这样，就有可能自动纠正差异和错误以产生目标状态。

原则上，没有 DevOps 的 GitOps 是可能的，没有 GitOps 的 DevOps 也是可能的，但这两种方法完美地互补，一起使用是有意义的。

简单来说，GitOps 是一套独特的管理 Kubernetes 和类似平台的方法。亚历克西斯·理查森创造了“GitOps”这个术语。他是 [Weaveworks](http://seekoapp.io/61582ee1d803b60008b3c961) 的联合创始人兼 CEO。

Weaveworks 将 GitOps 描述为 Kubernetes 和云原生开发中使用的其他技术的操作模型。根据声明，GitOps 提供了一套统一部署、管理和监控容器集群和应用程序的最佳实践。

我以一种开发人员友好的方式对其进行总结，以管理应用程序，并为运营和开发实现端到端的 CI/CD 管道和 g it 工作流。

# GitOps 对 CIOps

![](img/46f428f155cbb9b0e1284780d16a436a.png)

[雅万萨胡](https://unsplash.com/@yawan_sahu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

术语 GitOps 源自版本管理软件 Git。

与 DevOps 方法相比，Ops 是“操作”的缩写

GitOps 的目标是使开发人员能够高效地执行 IT 运营领域的任务。与 CI/CD 管道的经典实现相比，这一概念得到了最好的说明，现在称为 CIOps，以便更好地将其与 GitOps 区分开来。

在 CIOps 管道中，CI 服务器执行到操作环境的部署。

不同阶段的实现可以通过使用 Git 中的分支来完成，其中开发分支包含集成开发状态，主分支包含生产版本。

在一个简单的 CD 策略中，每次推进开发都会导致部署准备阶段，而推进主阶段会导致生产阶段。因此，试运行中的最后一个集成版本可以用于测试，然后 main 上的一个 pull 请求可以开始将该版本部署到生产环境中。

这个程序还有改进的余地。CI 服务器必须为每个部署运行构建。这意味着一个版本必须在所描述的方法中构建两次。

然而，相同的工件可以用于所有阶段。这就是 GitOps 的用武之地，它允许 CI 服务器执行构建，存储工件，然后用于不同阶段的部署。

GitOps 操作者不断地将目标阶段的状态与 Git 存储库中描述的实际阶段进行比较。不匹配将被自动纠正。

# 实际实施 GitOps 的工具

![](img/16a875a2d5701902bccdda3161222d87.png)

照片由[尼古拉斯·霍伊泽](https://unsplash.com/@nhoizey?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

GitOps 起源于 Kubernetes 环境，这是它被最广泛采用的地方，尽管现在也有在其他环境中使用该应用程序的方法。

在 Kubernetes 中，GitOps 的实现是通过上面提到的操作符来完成的，这些操作符也称为定制控制器。GitOps 操作符在一个 [K8s 集群](http://seekoapp.io/6158335cefc5be000997fbc0)上运行，并执行一个协调循环，其中描述的目标状态与集群的实际状态永久协调并同步。这确保了系统对应于 Git 中所示的状态。

[Flux](http://seekoapp.io/6158335cefc5be000997fbbe) 和 [Argo](http://seekoapp.io/6158335aefc5be000997fbbc) CD 是最流行和最广泛使用的[云原生计算基础](http://seekoapp.io/6158335aefc5be000997fbba) (CNCF)实现。CNCF 的最终用户社区将 Flux 2020 评为“可部署”，因此推荐它。

GitOps 上下文中的其他操作符包括 [Fleet](http://seekoapp.io/61583359efc5be000997fbb8) 、 [Jenkins-X](http://seekoapp.io/61583359efc5be000997fbb6) 和 [werf](http://seekoapp.io/61583358efc5be000997fbb4) 。

*   Fleet 旨在能够管理不止一个集群，而是整个集群。
*   Jenkins-X 是一个完整的 CI/CD 应用程序。与 Flux 和 ArgoCD 不同，它不需要额外的 CI 服务器，而且其功能要丰富得多，因此不太灵活。
*   相比之下，`werf`代表了 GitOps 操作符和经典 CIOps 方法之间的一种折衷。该工具在集群之外运行，但仍然可以像操作者一样将来自 Git 的 Kubernetes 资源应用到集群。但是，这不允许 pull 原则在集群中实现，从 Git 中拉出它的目标状态。

通过声明性地描述 GitOps 中的状态，有些东西不能直接实现，这就需要额外的操作符。这些用所谓的[定制资源定义](http://seekoapp.io/61583357efc5be000997fbb2) (CRDs)扩展了 Kubernetes API 服务器，并提供了[定制资源](http://seekoapp.io/61583356efc5be000997fbb0) (CRs)，支持状态的声明性描述。

模板工具 [Helm](http://seekoapp.io/61583355efc5be000997fbae) 和 [Kustomize](http://seekoapp.io/61583354efc5be000997fbac) 仍然可以通过 CR 使用，即使是声明性描述，例如使用 Flux。

将 CR 应用到集群时，Helm 或 Kustomize 操作符处理模板和覆盖。此外，还有其他 GitOps 运营商用于实现不同的部署策略，如蓝/绿部署、金丝雀发布或 A/B 测试，概括为所谓的“渐进交付”

例如，这里的解决方案是 [flagger](http://seekoapp.io/61583353efc5be000997fbaa) 或 [Argo rollouts](http://seekoapp.io/61583352efc5be000997fba8) 。

# GitOps 更安全

![](img/3894348eaf068a27be554998ea2e49bc.png)

照片由[弗兰克](https://unsplash.com/@franckinjapan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

其他运营商也可以克服在安全性方面存在的挑战。

通过将整个状态存储在 Git 中，需要加密和解密的秘密也随之结束。例如，Bitnami 的 [Sealed Secrets 操作员管理集群中的密钥材料，其中存在用于加密的命令行，这需要连接到集群。](http://seekoapp.io/61583351efc5be000997fba6)

使用 Mozilla 开发的 [SOPS](http://seekoapp.io/61583350efc5be000997fba4) ，密钥材料来自自我管理的 PGP 密钥、云提供商的[密钥管理](http://seekoapp.io/61583350efc5be000997fba2)系统(KMS)或其自己的 [HashiCorp 保险库](http://seekoapp.io/6158334fefc5be000997fba0)。

SOPS 不包含操作符，但是可以使用 Flux v2 之类的插件将它集成到 GitOps 中。

相比之下，Kamus 提供一个运营商，从提供商的 KMS 获得密钥材料或管理它。

# GitOps 优势

总的来说，GitOps 对于开发团队来说相对容易使用，因为他们可以使用他们已经知道的工具，除了操作员，他们必须学习新的方法和软件。

他们还可以直接从代码库中管理软件产品。

因为 GitOps 是从集群内部运行的，所以从外部对集群的写访问很少与集群自动与 Git 同步相关联。

此外，不需要在 CI 服务器上存储任何凭证，因为它不需要访问群集。

一般来说，访问 Git 比访问 API 服务器要容易得多，因为您不需要激活防火墙。

# 试试 GitOps

您可能很难理解 GitOps 原理及其许多不同的操作符。但我能给你的最好建议是——尝试一下。

这里有一个在[cloud ogu/GitOps-playground](http://seekoapp.io/6158334fefc5be000997fb9e)实现 GitOps 的示例。

此 repo 包含一个完整的可复制 GitOps 基础架构。它使用一个 Kubernetes 集群、一个 CI 服务器(Jenkins)、一个源代码管理(SCM manager)和不同的 GitOps 操作符(Flux 和 Argo CD)，在这些组件上已经预先配置了示例应用程序。
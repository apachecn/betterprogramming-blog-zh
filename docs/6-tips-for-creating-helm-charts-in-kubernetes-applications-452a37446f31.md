# 在 Kubernetes 应用程序中创建舵图的 6 个技巧

> 原文：<https://betterprogramming.pub/6-tips-for-creating-helm-charts-in-kubernetes-applications-452a37446f31>

## 构建、维护和控制舵图发布，减少错误和代码问题

![](img/29c6ccc7732d0dcfd6efa4f82fa15974.png)

威廉·艾文在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

[Kubernetes](https://kubernetes.io/) 是一个编排工具，用于运行、自动化和管理容器化应用程序的生命周期。Kubernetes 管理的所有资源都需要以 YAML 或 JSON 格式定义，并由 Kubernetes API 服务器处理。

用 Kubernetes 部署应用程序通常需要定义多个资源(这高度依赖于应用程序本身)，比如配置映射、秘密部署和服务资源。这些资源都需要使用 Kubernetes API 服务器来创建。此外，有些资源需要按照特定的顺序创建，或者需要等待其他资源创建成功。

Kubernetes 没有为应用程序的资源提供打包解决方案。幸运的是，一个叫做 [Helm](https://helm.sh/) 的工具提供了开箱即用的特性。

您可以考虑 Helm Kubernetes 应用程序的包管理器。Helm 将部署给定应用程序所需的 Kubernetes 资源打包到一个称为图表的单元中。Helm chart 不仅封装了 Kubernetes 资源，还可以明确定义这些资源的创建顺序。

以下是使用 Helm 部署 Kubernetes 服务的主要优势:

*   它降低了部署微服务的复杂性。
*   它简化了部署脚本和文件(都是在 YAML 定义的)。
*   它提高了部署和回滚的效率。
*   它提供了一种将 Kubernetes 资源作为一个单元进行版本控制的方法。

为了能够使用 Helm 将应用程序部署到 Kubernetes 集群，您需要为应用程序创建 Helm 图表，并确保该图表对 Kubernetes 集群可用。可以使用 Helm 命令行引导新图表，如下所示:

```
$> helm create service-x
```

定义的图表将用于部署您的应用程序。因此，确保所有图表版本都具有一定程度的质量(例如，图表没有语法错误)是很重要的。

在本文中，我将强调在开发和创建容器化应用程序的舵图时需要考虑的六个主题。

# **1。为每个已定义的资源定义单元测试**

确保 Helm 图表的一致性和健壮性是很重要的——特别是如果该图表是为外部使用而开发的，比如 MySQL 和 NGINX 的公共图表。幸运的是，通过简单地为舵图及其所有资源定义单元测试，实现这一点是一件简单的任务。

helm-unittest 是一个 helm 插件，它使我们能够为 Helm 图表定义单元测试，并使用 YAML 格式的测试文件进行测试。该插件提供的主要功能有:

1.  单元测试是用纯 YAML 语法定义的(测试易于阅读和理解)。
2.  它不需要 Kubernetes 集群，可以在本地执行。
3.  它测试图表值和发布配置。
4.  它支持快照来控制资源文件中的更改。

要开始为您的图表开发测试用例，您必须完成以下两项任务:

*   安装头盔插件:

```
$ helm plugin install https://github.com/lrills/helm-unittest
```

*   开始创建和编写测试用例。测试用例需要存储在你的图表的根目录下(即`$YOUR_CHART/tests/deployment_test.yaml`)。

测试用例以纯 YAML 格式定义。例如，下面的 YAML 文件代表了一个测试用例，它验证了一个部署资源的属性，比如副本数量。

同样，您可以为其他 Kubernetes 资源定义测试用例，比如服务、配置映射和 pods。关于支持的语法的更多信息可以在 GitHub 上找到[。](https://github.com/lrills/helm-unittest/blob/master/DOCUMENT.md)

运行和执行已定义的测试用例可以通过执行以下命令来完成:

```
$> helm unittest $YOUR_CHART_NAME
```

# **2。集成图表的持续集成管道**

[helm-unittest](https://github.com/lrills/helm-unittest) 提供了一种编写和定义 Helm chart 单元测试的方法，但是它没有自动执行这些单元测试，也没有涵盖我们需要考虑的其他方面，例如:

1.  检查/林挺图表资源。
2.  测试图表安装。

一个名为 ct 的工具是为基于拉取请求的测试和林挺掌舵图而设计的。这个工具将使用户能够确保一个干净和绿色的舵轮图的主要分支。在合并源代码变更之前，应该发现所有的错误和语法问题。

ct 基本上是一个命令行应用。但是，它也支持通过环境变量或配置文件定义命令行标志。它还支持混合的 CLI 标志、环境变量和配置文件。在这种情况下，以下优先顺序适用:

1.  CLI 标志
2.  环境变量
3.  配置文件

将 ct 命令行与在 pull 请求上执行的 CI 管道集成起来是一项简单的任务。完成这个任务的一个方法是利用下面的 [GitHub 动作](https://github.com/helm/chart-testing-action)。下面是一个 GitHub 动作工作流程的例子，可以用于林挺和测试舵图:

# **3。自动化图表发布**

每当舵轮图发生变化时，自动发布海图是很重要的。图表发布的自动化将实现不同环境中应用程序部署的自动化，以及端到端集成测试的自动化。幸运的是，这个任务也很简单，我们有几个选项来完成这个任务。

选择在哪里发布图表时，一个重要的问题是图表是需要公开访问还是只需要内部访问。

这三个选项可用于发布舵图:

1.  运行您自己的 [chartmuseum](https://chartmuseum.com/) 实例——chart museum 是一个开源的 Helm chart 服务器。如果您希望能够灵活地推动舵图，并且拥有运行服务器所需的资源，请使用此选项来托管您的图表。chartmuseum 支持几个本地特性，比如在 AWS 中存储图表和身份验证。
2.  使用其中一个托管服务来托管 Helm charts —如果您不想自己管理 charts 服务器，但仍然希望拥有私有图表，您可以选择使用主机 charts 服务器的可用选项之一，例如 [JFrog](https://jfrog.com/) 平台(JFrog 也可以选择自托管)。使用 JFrog，您可以构建 Helm chart 存储库，并在几分钟内开始向其发布图表。此外，只需执行一个`curl`命令就可以将图表推送到存储库。
3.  使用 Git-Pages 在 GitHub 上发布舵图——有几个选项可以在集中式图表服务器上发布公共舵图，如 [chartcenter](http://chartcenter) 。然而，我更喜欢简单地用 GitHub 页面发布 Helm 公共图表。该选项仅在图表应该是公共的情况下才适用，因为 GitHub 没有提供实现 GitHub 页面认证的本机方式。通过将下面的 [GitHub 动作](https://github.com/helm/chart-releaser-action)与为应用程序实现的 GitHub 工作流集成，可以完成这项任务。

# **4。处理图表机密**

Helm charts 封装了所有的 Kubernetes 资源，包括存储应用程序配置的配置图。一些配置通常被视为敏感数据，应受到保护，不得公开共享或提交给图表的源代码。因此，在构建舵图时，处理以下两点非常重要:

1.  将配置与秘密分开——Kubernetes 提供了一个名为*秘密*的资源，其工作方式与`configmap`完全相同，只是它以编码的形式存储数据。应该使用这种资源类型而不是配置映射来存储和管理所有应用程序机密，例如数据库连接信息或 API 密钥和机密。通过这一更改，我们可以确保秘密不会以纯文本的形式存储在 Kubernetes 集群上。
2.  使用 [helm-secrets](https://github.com/jkroepke/helm-secrets) 插件来管理图表机密——第一点仅涉及机密如何存储在 Kubernetes 集群上。我们仍然可以在`values.yaml`文件中添加秘密的值，并以纯文本形式将它们传递给舵图。更危险的是，我们可以将`valus.yaml`文件提交给 Git 存储库。谢天谢地，我们可以使用 helm-secrets 插件来处理和管理加密格式的秘密。这个插件支持两个存储秘密的选项。第一个也是默认的是 [Mozilla/sops](https://github.com/mozilla/sops/) 后端，第二个是 [Hashicorp Vault](http://vaultproject.io/) 。此外，它同时支持多个密钥管理解决方案，如 PGP、AWS KMS 和 GCP KMS。

# **5。设置图表相关性**

有些应用程序不是作为独立服务运行的，它们依赖于其他服务或第三方工具。例如，需要将数据保存在数据库中的应用程序依赖于数据库，并且应该在部署应用程序本身之前就可以使用数据库。

如果这些依赖项都是由 Helm 部署和管理的，那么如果定义了每个图表的依赖项，就有可能简化部署。Helm 将确保首先部署依赖项并且这些依赖项可用，然后部署应用程序。当试图自动化这个步骤时，不需要手动部署这些依赖项，甚至不需要重新发明轮子。图表的依赖关系可以在`Chart.yaml`文件中定义，如下图所示:

# **6。注意特定于 Kubernetes 的配置**

由于 Helm chart 用于呈现和创建所需的 Kubernetes 资源，因此需要确保创建的资源支持以最佳方式运行应用程序。例如，图表应该支持在零停机时间推出新资源。以下三个主题有助于提高舵图的质量:

1.  选择运行工作负载容器的资源——Kubernetes 为创建和管理工作负载容器提供了多种资源，例如部署、StatefulSets 和 DaemonSets。根据应用程序的性质，需要选择工作负载资源类型。例如，如果应用程序需要在每个 Kubernetes 节点上运行，那么 DaemonSet 资源就是所需的资源类型。另一方面，如果应用程序持久保存数据并维护其身份，则 StatefulSet 是这种情况下使用的资源类型。有关受支持工作负载的更多信息，可在文档中找到[。](https://kubernetes.io/docs/concepts/workloads/)
2.  维护 Kubernetes 配置以支持零停机部署。Kubernetes 提供就绪性和活性探针配置，以便能够确定正在运行的 pod 的健康状况。该功能使 Kubernetes 能够在 pod 被识别为不健康时自动从崩溃中恢复，并使 Kubernetes 能够在将更新滚动到零时最大限度地减少停机时间。
3.  公开服务——对于为 API 或 web 接口提供 HTTP 服务器的应用程序来说，考虑如何使用这些接口是很重要的。是仅供内部使用还是准备公开曝光？关于这个问题，Kubernetes 提供了服务对象，使我们能够公开集群内部(集群 IP)或集群外部(节点端口或负载平衡器)的服务。入口控制器也可以用于在集群外部公开服务。

# 结论

Helm 是 Kubernetes resources 的软件包经理。使用 Helm 图表，您可以将几个 Kubernetes 资源分组，并将它们作为一个单元应用到 Kubernetes 集群上，以部署给定的应用程序。Helm charts 简化了 Kubernetes 集群上的部署过程。然而，维护一个稳定和强大的掌舵图需要的不仅仅是将 Kubernetes 资源分组到一个包中。
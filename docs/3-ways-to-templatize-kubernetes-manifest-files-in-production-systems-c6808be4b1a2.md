# 在生产系统中模板化 Kubernetes 清单文件的 3 种方法

> 原文：<https://betterprogramming.pub/3-ways-to-templatize-kubernetes-manifest-files-in-production-systems-c6808be4b1a2>

## 基于两年来对 Kubernetes 的研究

![](img/186a336f7bf6e6b3f2fdfe64dad4dc59.png)

[伊恩·泰勒](https://unsplash.com/@carrier_lost?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/container?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照。

最初由 Google 开发的, [Kubernetes](https://kubernetes.io/) 是一个开源容器编排平台，旨在自动化容器化应用程序的部署、扩展和管理。写这篇文章的时候，Kubernetes 已经七岁左右了，它是最受人喜爱的平台之一。一份 [RightScale 报告](https://www.flexera.com/about-us/press-center/rightscale-2019-state-of-the-cloud-report-from-flexera-identifies-cloud-adoption-trends.html)显示，集装箱采用率从 2018 年的 49%增长到 2019 年的 57%。包括微软 Azure、谷歌云和亚马逊 AWS 在内的许多云提供商也推出了容器。

Kubernetes 使用形式为`.yaml`的清单文件来创建、修改和删除 Kubernetes 资源，比如 pod、部署、服务等。但是 Kubernetes 没有提供任何自己的模板机制，因为部署的清单应该是静态的 YAML 文件。这意味着，如果您希望对生产、转移和开发使用不同的配置集，默认情况下将不可用。

让我们考虑一个例子:

现在，您可能希望有不同数量的副本用于转移和 QA。同样，您使用的映像可能因环境而异。如果您硬编码这些值，可能很难在不同的环境中使用它们。

这就是模板化派上用场的时候。这是创建您自己的清单文件或更新和编辑现有文件的一种方式。

让我们来看看完成这项工作的不同方法。

# 用代码生成清单文件

您可以通过编写代码以编程方式生成 Kubernetes YAML 文件。Kubernetes 有几个[客户端库](https://kubernetes.io/docs/reference/using-api/client-libraries/)来帮助创建对象，比如部署和 Pod 代码。

下面，我将展示一个 Java 例子来说明如何轻松地做到这一点:

你可以在环境中传递给这个函数，它会为你创建一个 pod YAML。

```
Pod pod = *createPod*(environment);
```

这是由此产生的 YAML:

然后，您可以使用此 YAML 在整个集群中应用。你可以在 GitHub 上找到完整的代码库[。](https://github.com/learnk8s/templating-kubernetes)

编写代码的好处是您不必担心替换变量、配置等。守则会照顾到所有这一切。你只需传递一些变量，它就会为你创建文件。

然而，编写代码来生成这些清单文件在 Kubernetes 世界中并不常见——可能是因为这比手工编写 YAML 更难。

# 使用 Kustomize 更新和修补清单文件

是一个帮助你管理 Kubernetes 清单文件的工具。从 1.14 开始，Kubectl 还支持使用 kustomization 文件管理 Kubernetes 对象。

使用`Kustomize`，创建一个基本的 YAML 文件，然后对其应用补丁。假设您为创建一个 Pod 编写了一个 YAML 文件。但是您的公司需要对其应用额外的网络策略。您可以在 YAML 中对策略进行硬编码，也可以将其作为单独的文件添加，并在部署时对其进行修补。

相同的概念可以应用于副本、配置映射、环境变量等。

`Kustomize`通过草绘文件工作。让我们看一个如何使用`Kustomize`创建和修补清单文件的例子。

在一个新目录中，创建两个文件夹:`base`(用于基本 YAMLs)和`overlays`(用于您想要修补的任何额外内容)。

![](img/48a6b2a70a39bef43ecc72c11c7f926f.png)

在我的`base`中，我有一个用于创建 pod 资源的部署 YAML:

除了这个`base`清单文件，我们还需要添加一个库定制文件。看起来是这样的:

现在，让我们看一下专门为我们的临时集群添加一个映像。

在`overlays`文件夹中，让我们为配置创建一个`stag`文件夹。添加一个文件:`image.yaml`。

以下是该文件的外观:

*注意:请确保名称与* `*base*` *相同。*

除此之外，我们还需要一个`kustomization.yaml`:

现在，让我们尝试修补 YAMLs:

您可以观察图像是如何添加到基本 YAML 的。

目录结构如下所示:

```
Cintos-Mac:testKustomize cinto [druiddevdev]$ tree .
.
├── base
│   ├── deployment.yaml
│   ├── kustomization.yaml
│   └── service.yaml
└── overlays
    └── stag
        ├── image.yaml
        └── kustomization.yaml
```

Kustomize 在为不同环境修补清单文件方面提供了很大的灵活性。Kustomize 唯一的小问题是不能在 YAMLs 中逐字替换，这可能是一些人的用例。但是有很多选项可以做到这一点，比如 sed 或`yq`。

# 使用 yq 更新清单文件

`yq`是一个伟大的轻量级命令行工具，用于模板化 YAML 文件。

如 vik 代码所示，`yq` [也可以](https://dev.to/vikcodes/yq-a-command-line-tool-that-will-help-you-handle-your-yaml-resources-better-8j9):

> "1.从文件中读取值。
> 
> 2.添加新值。
> 
> 3.更新现有值。
> 
> 4.生成新的 YAML 文件。
> 
> 5.将 YAML 转换成 JSON。
> 
> 6.合并两个或多个 YAML 文件。"

让我们看一个如何使用`yq`更新清单文件的例子。以下是用于创建 pod 的 YAML 文件示例:

我们希望将副本数量从`2`更新到`5`:

你也可以使用`sed`或`awk`来完成这些转换。但是使用`yq`优于`sed`的地方在于，与`sed`不同的是，`yq`理解 YAML 格式，并且能够导航和处理结构化标记。`sed`将文件视为字符串，不介意文件是否是有效的 YAML。

# 结论

如果您使用 Kubernetes 进行部署，您将需要某种方法来为不同的环境和集群模板化您的清单文件。

以下是三种最常用的方法:

*   编写您的清单文件。
*   使用`Kustomize`修补文件。
*   使用`yq`更新文件。

我已经在我的生产集群中使用了这三个工具。我经常使用`Kustomize`和`yq`一起更新和修补文件。
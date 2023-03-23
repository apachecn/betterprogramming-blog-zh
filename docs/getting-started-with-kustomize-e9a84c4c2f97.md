# Kustomize 入门

> 原文：<https://betterprogramming.pub/getting-started-with-kustomize-e9a84c4c2f97>

## Kubernetes 资源的声明式管理及实践示例

![](img/bcf4c82e9cfda0ad8bbd26dcab44fe87.png)

由 [Christopher Zarriello](https://unsplash.com/@chris_zarriello?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

Kubernetes 是著名的容器编制者之一，并导致了围绕它的整个生态系统的发展。它允许组织通过提供多种资源来管理容器部署、副本、扩展、服务发现和通过单个 API 接口进行联网，从而轻松管理其容器应用程序。

在将这些应用程序部署到生产环境之前，大多数组织都有多个环境来开发和测试这些应用程序。这些环境之间的配置可能会有所不同，并且可能有几个方面需要调整。

有多种方法可以为多种环境管理 Kubernetes 资源，例如 Helm 和 Kustomize。Helm 是最动态的基于模板的 Kubernetes 资源管理器。然而，你可能不需要所有的头盔。Kustomize 是 Kubernetes 为多种环境管理 Kubernetes 资源清单的本地方法。它是根据叠加原理来实现的。让我们在下一节了解这两种方法。

# 模板与覆盖

基于模板的引擎的工作原理是用值替换变量。Helm 是一个基于模板的引擎，它允许您通过在通用清单的特定位置声明变量占位符来定义基于模板的通用清单。然后使用变量文件用值替换变量。

基于覆盖的引擎按照查找和替换的原则工作，即，它搜索清单中的特定部分，并使用基于查找、替换和合并的策略用所需的值替换它们。这允许您创建基本的清单文件，这些文件基本上是标准的 Kubernetes 资源定义文件，可以独立部署。然后，您可以使用 Kustomize 根据您的需求进一步定制这些文件。

# Kustomize 如何工作

Kustomize 使用一个名为`kustomization.yaml`的文件，该文件包含声明性的规范，说明需要从什么清单文件中导入什么资源以及需要进行什么更改。一旦处理完资源，它就将它们发送到标准输出，标准输出可以存储在一个文件中，也可以直接与 kubectl 一起使用，以将其应用到特定的集群。

Kustomize 的一个优秀用例是管理多个环境的 Kubernetes 资源。为了让 Kustomize 在这种情况下工作，您需要一个包含所有公共元素的所有清单文件的`base`目录和一个包含特定环境的所有差异的`overlays`目录。

为了更好地理解，我们来看一个动手的例子。

# 先决条件

您将需要一个运行 Kubernetes 1.14 或更高版本的 Kubernetes 集群和一个类似版本的`kubectl` CLI。

您还需要派生这个存储库[。](https://github.com/bharatmicrosystems/kustomize-example-app)

我们还需要安装 Kustomize。那么，让我们在下一节来看看。

# 安装 Kustomize

安装 Kustomize 有多种方法，你可以在[https://kubectl.docs.kubernetes.io/installation/kustomize/](https://kubectl.docs.kubernetes.io/installation/kustomize/)找到。在我们的例子中，我们将使用二进制方法安装它。

为此，请运行以下命令:

```
$ curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  **|** bash
$ sudo mv kustomize /usr/bin/
```

既然我们已经安装了 Kustomize，让我们来理解问题陈述以及我们计划实现什么。

# 问题陈述

我们有一个 web 应用程序，希望部署在几个环境中。它具有以下特定于环境的差异:

1.  它应该包含一个与其部署环境相对应的`env`标签(即开发、测试和生产)。
2.  它应该部署在与环境相对应的名称空间上(例如，开发、测试和生产)。
3.  开发环境中的副本数量应该是一个，测试环境中两个，生产环境中三个。
4.  在 prod 环境中，我们将使用`maxSurge` 1 和`maxUnavailable` 1 实现滚动更新策略。

因此，让我们来看看它的目录结构。

# 目录结构

我们将在两个文件`deployment.yaml`和`service.yaml`中创建一个`Deployment`和一个`Service`资源。目录结构如下所示:

```
kustomize-example-app/
├── base
│   ├── deployment.yaml
│   ├── kustomization.yaml
│   └── service.yaml
└── overlays
    ├── dev
    │   ├── deployment.yaml
    │   ├── kustomization.yaml
    │   ├── namespace.yaml
    │   └── service.yaml
    ├── prod
    │   ├── deployment.yaml
    │   ├── kustomization.yaml
    │   ├── namespace.yaml
    │   └── service.yaml
    └── test
        ├── deployment.yaml
        ├── kustomization.yaml
        ├── namespace.yaml
        └── service.yaml
```

`base`目录包含基本清单和一个`kustomization.yaml`文件，该文件包含三个环境之间的所有公共元素。`overlays`目录包括分别用于`dev`、`test`和`prod`的目录。因为我们只是简单地改变了开发、测试和生产环境中的`Deployment`和`Service`资源的属性，所以我们为它们分别准备了一个清单。然而，清单将只包含资源之间的差异。

我们先来看一下`base`目录。

# 基本配置

在`base`目录中，`deployment.yaml`文件如下所示:

`service.yaml`文件如下所示:

现在，为了让 Kustomize 工作，我们需要创建一个类似如下的`kustomization.yaml`文件:

正如我们所看到的，Kustomization 文件像任何其他 Kubernetes 资源一样包含了`apiVersion`和`kind`属性。根据您的计划，该文件也可以有几个部分。在这种情况下，由于我们只是试图在 Kustomization 中导入`Deployment`和`Service`，我们使用`resources`部分来完成。

当我们在`base`目录上运行`kustomize build`命令时，Kustomize 发出一个清单 yaml，它是两者的组合。

现在，我们来看一下特定于环境的配置。

# 特定于环境的配置

现在，我们将重点放在`overlays`目录上。我们在`overlays`目录中有针对`dev`、`test`和`prod`环境的目录。我们先来看看`dev`目录的内容。

## 开发配置

`namespace.yaml`文件如下所示:

现在，基本配置中没有这一点，因为命名空间会随着环境而变化。

现在我们来看一下`kustomization.yaml`文件:

在这里，你可以看到有几个部分。让我们来详细了解一下每一项:

*   `bases` —此部分包含对`base`目录的引用。
*   `commonLables` —本节定义了我们希望应用于 Kustomize 生成的所有资源的任何通用标签。根据我们的要求，我们给了标签`env:dev`。
*   `resources` —此部分包含我们想要添加到 Kustomize 配置中的任何附加资源。因为我们在这里指定了一个额外的清单文件`namespace.yaml`。
*   `namespace` —此部分定义了应该在其中部署资源的任何公共名称空间。这种情况下就是`dev`。

现在，让我们在`dev`目录上运行`kustomize build`，看看我们会得到什么:

正如我们所见，它符合我们的所有要求。

要将清单应用到 Kubernetes 集群，我们可以使用下面的 kubectl 命令:

```
$ kubectl apply -k overlays/dev
namespace/dev created
service/nginx-service created
deployment.apps/nginx-deployment created
```

正如我们所看到的，这三个资源都已创建。让我们使用下面的代码列出`dev`名称空间中的所有资源:

太好了！现在，我们有了一个`Deployment`，其中一个副本`Pod`由负载平衡器`Service`公开。所有这些资源都被部署到`dev`名称空间，并包含标签`env: dev`。

现在，让我们转到`test`目录，看看那里有什么。

## 测试配置

在`test`目录中，我们有以下`namespace.yaml`文件:

除此之外，我们还有下面的`deployment.yaml`文件:

这里有一个`deployment.yaml`文件的原因是因为我们想将测试环境中的副本数量从 1 个改为 2 个。

现在，让我们来看看`kustomization.yaml`文件:

大多数部分都类似于 dev `kustomization.yaml`文件。不过我们这里多了一段，也就是`patchesStrategicMerge`。在其中，我们声明了`deployment.yaml`文件，它只包含增量配置(补丁)，即副本的数量。`patchesStrategicMerge`将用补丁 yaml 文件中声明的配置为与资源`name`、`kind`和`apiVersion`匹配的所有资源打补丁。

当我们在`test`目录上运行 kustomize 构建时，我们将得到以下结果:

现在，让我们使用以下命令将其应用到我们的 Kubernetes 集群:

```
$ kubectl apply -k overlays/testnamespace/test created
service/nginx-service created
deployment.apps/nginx-deployment created
```

类似地，让我们使用下面的命令获取部署在`test`名称空间中的所有资源的详细信息:

正如我们所看到的，这次我们有一个`Deployment`运行两个副本。

现在让我们继续看 prod 的例子。

## 生产配置

产品目录由类似于`test`和`dev`配置的`namespace.yaml`组成:

它还包含以下`deployment.yaml`补丁清单:

这个补丁包含三个`Deployment`副本，以及一个类型为`RollingUpdate`、`maxSurge` 1 和`maxUnavailable` 1 的部署`strategy`部分。

现在让我们来看看`kustomization.yaml`文件:

这个与我们在`test`中的相似。现在，让我们继续运行`kustomize build`，看看如果应用它我们会得到什么。

如我们所见，`replicas`和`strategy`部分已正确填写。现在，让我们使用以下命令来应用它:

```
$ kubectl apply -k overlays/prod
namespace/prod created
service/nginx-service created
deployment.apps/nginx-deployment created
```

现在，让我们列出`prod`名称空间中的所有资源，自己看看:

正如我们看到的，我们现在有三个副本在运行。

让我们尝试使用`curl`到达`Service`端点，看看我们会得到什么:

我们得到默认的 NGINX 响应！这证明设置工作正常。

# 结论

显然，这是对 Kustomize 是什么以及我们如何有效地使用它的一个初步了解。使用 Kustomize 还有很多其他方法和途径，我认为这将是未来故事中一个有趣的探索。

感谢阅读！我希望你喜欢这篇文章！
# 使用舵操作员部署微服务

> 原文：<https://betterprogramming.pub/deploy-microservices-with-helm-operators-a67e39b81765>

## 库伯内特斯

## 使用 helm operators 通过单一 Helm 图表管理和部署您的微服务

![](img/6b995a59ffd546350f9411510adf6dfb.png)

德里克·欧文斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# **简介**

微服务架构是当今的一种趋势，许多公司都采用这种架构来构建和管理他们的服务。此外，工程师们更喜欢这种架构而不是单片架构，原因有几个，比如职责分离和遵循这种模式开发软件应用程序所获得的更高的可维护性。然而，每种架构都有其缺点和挑战。以下是微服务架构面临的三大挑战:

*   跟踪请求:单个应用程序通常在整体架构上处理传入的请求，这使得通过跟踪应用程序日志来跟踪请求变得很容易。另一方面，在微服务架构中，多个服务可能会对单个请求的响应做出贡献。因此，单个请求的日志将跨越几个应用程序，因此需要关联来自不同服务的请求日志。
*   管理共享源代码:尽管项目或用于开发服务的编程语言不同，微服务在大多数情况下共享相同的基本功能。例如，每个服务都需要写日志消息，为其 API 端点提供健康检查，以及许多其他功能。在服务级别上开发这些功能非常耗时，并且会在服务之间引入不一致性。维护共享代码的更好方法是尽可能使用库。
*   部署配置和管道:很明显，部署遵循微服务架构的软件比部署单一应用程序更复杂，因为它涉及部署几个可能相互依赖的服务。

# **问题**

将服务部署到 Kubernetes 集群需要为每个托管服务定义资源。以下是在 Kubernetes 中部署服务所需的一些资源列表:

*   部署
*   进入
*   配置映射
*   秘密

逐个创建这些资源是一项耗时且容易出错的任务。这就是为什么我们有一个叫做 [Helm](https://helm.sh/) 的工具，一个运行在 Kubernetes 上的服务的包管理器。Helm 使我们能够用一个命令将一个应用程序部署到 Kubernetes，而无需为该应用程序创建所有需要的 Kubernetes 资源。然而，假设您有几个服务(微服务架构就是这种情况)。在这种情况下，您需要为每个应用程序定义一个掌舵图，这将为部署服务和管理掌舵图带来大量工作，原因如下:

*   每个军种都需要自己的舵轮图。
*   更新标准部署配置可能会导致触及每一个图表。
*   如果多个团队维护舵图，舵图可能会不一致，并且不遵循任何标准。

# **可用选项**

为了解决上面提到的问题，我们有几个可以遵循的选项:

*   使用 CICD 工具，如 [Argo CD](https://argo-cd.readthedocs.io/en) 来管理 Kubernetes 资源和部署。
*   构建一个部署工具来生成 Kubernetes 资源，并将它们部署到集群中。
*   使用 Helm 操作员来管理和部署服务。

这篇博文将带你一步一步地构建一个可以部署多个微服务的 Helm operator。

# **建造舵操作员**

[Kubernetes operators](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) 是在 Kubernetes 集群中运行的软件应用程序，管理定制 Kubernetes 资源的生命周期。市场上有几种框架可以用来构建 Kubernetes 控制器，例如:

*   [迷倒算子框架](https://juju.is/)
*   [kubebuilder](https://book.kubebuilder.io/)
*   [KubeOps](https://buehler.github.io/dotnet-operator-sdk/) (。网络运营商 SDK)
*   [KUDO](https://kudo.dev/) (Kubernetes 通用声明运算符)
*   [元控制器](https://metacontroller.github.io/metacontroller/intro.html)
*   [运营商框架](https://operatorframework.io/)

构建 Kubernetes 操作符的最佳框架之一是[操作符框架](https://operatorframework.io/)。该框架使我们能够使用如下所述的三种不同方法来开发运算符:

*   使用 Go 编程语言。
*   使用 Ansible
*   使用舵图。

与使用 Go 编程语言或其他框架开发 Kubernetes 操作符相比，使用舵图构建 Kubernetes 操作符是一项简单的任务。并且可以减少开发和维护 Kubernetes 操作符所需的工作。

[操作符框架](https://operatorframework.io/)使我们能够创建 Kubernetes 操作符进行管理，只需定义一个部署和管理应用资源的掌舵图。构建操作符不需要额外的开发。本节将逐步引导您创建一个 Helm 图表，并基于该图表生成一个 Kubernetes 操作符。

在我们开始构建 Kubernetes 操作符之前，确保您有以下需求:

*   拥有 Kubernetes 集群的集群管理权限的用户。
*   一个可访问的图像注册表，如 hub.docker.com。

一旦设置了上述要求，就可以继续执行以下步骤来构建操作符:

首先，您需要在您的机器上安装`operator-sdk`命令行。您可以使用下面的命令在 macOS 上安装它

```
$> brew install operator-sdk
```

如果您使用其他操作系统，请查看本页中的[了解安装说明。](https://sdk.operatorframework.io/docs/installation/)

接下来，我们需要为我们的操作员创建一个舵图。如果您已经有一个现有的舵图表，并希望基于它创建一个操作符，您可以跳过这一步。下面的命令将使用舵命令行创建一个新的舵图表。Helm chart 将管理以下 Kubernetes 资源。

*   **ServiceAccount** :定义部署使用的服务账户。
*   **部署**:定义一个 Nginx 部署对象。
*   **服务**:定义一个服务对象来访问 Nginx pods。
*   **Ingress** :定义 Ingress 对象来公开 Nginx 服务。
*   `HorizontalPodAutoscaler`:根据 RAM 和 CPU 资源水平扩展 Nginx pods。

```
$> mkdir microservice-operator && cd microservice-operator
$> helm create microservice
```

然后，使用以下命令初始化操作符(如果需要，替换域和项目标志):

```
$> operator-sdk init --plugins=helm --domain wshihadeh.dev \
   --project-name microservice-operator
```

上面的命令将在本地创建一堆文件，下面是对其中最重要的文件的简要描述

*   Dockerfile:操作符 dockerfile。
*   Makefile:提供了一组用于管理操作者生命周期的命令，比如构建 Docker 映像和推送它。
*   Config:该文件夹包含部署操作员所需的所有 Kubernetes 资源。
*   舵图:包含由操作员管理的舵图。

接下来，使用下面的命令为操作员创建 API 资源(确保更新组、版本和舵图标志):

```
$> operator-sdk create api --group=deployments --version=v1 \
   --helm-chart=./microservice
```

现在，我们为操作员生成了所需的文件。我们可以为操作者构建 docker 映像，并使用下面的命令将其推送到注册中心。

```
$> make docker-build docker-push \
   IMG**=**"wshihadeh/microservice-operator:v0.1"
```

接下来，将操作员 CRDs 安装到`~/.kube/config`中指定的 K8s 集群中。

```
$> make install
```

然后，将操作员部署到`~/.kube/config`中指定的 K8s 集群..

```
$> make deploy IMG=wshihadeh/microservice-operator:v0.1
```

在这个阶段，操作员应该在 Kubernetes 集群中启动并运行，并准备好管理定制资源。您可以使用以下命令来验证安装过程:

```
$> kubectl get ns | grep microservice-operator-system
$> kubectl -n  microservice-operator-system get pods
```

最后，您可以使用 Kubernetes `apply`命令开始向 Kubernetes 部署资源。您可以在目录`config/samples`下找到一个资源示例，您可以如下所示部署它:

```
$> kubectl apply -f config/samples/deployments_v1_microservice.yaml
```

一旦您使用上面的命令创建了新的资源，运行的操作员将在集群上安装该版本。您可以使用以下命令之一来验证安装过程:

```
$> kubectl get pods
$> kubectl -n microservice-operator-system \
   logs -f $conatiner manager
```

# **好处和限制**

使用[操作符框架](https://operatorframework.io/)基于舵图构建 Kubernetes 操作符有以下好处

*   开发很容易，因为它只涉及更新舵图，只需要标准的 Kubernetes 配置知识。
*   使用 CICD 管道可以自动构建和部署操作符。

另一方面，使用 Helm 和[操作符框架](https://operatorframework.io/)开发操作符的缺点是:

*   操作员功能仅限于 Helm 中可用的功能和特性。

# **结论**

管理实现微服务器架构的软件可能是一项复杂的任务，尤其是如果需要为每个服务单独完成几项例行任务。管理 Kubernetes 服务可能是复杂的任务之一。您可以使用 Helm 和 Operator Framework 快速构建一个安装和管理微服务的 Kubernetes 操作员。[操作框架](https://operatorframework.io/)允许我们将舵图转换为基于舵图模板部署资源的软件。
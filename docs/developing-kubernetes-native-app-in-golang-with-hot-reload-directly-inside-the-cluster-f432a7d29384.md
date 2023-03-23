# 使用 Golang 开发 Kubernetes 本地应用程序，直接在集群中热重装

> 原文：<https://betterprogramming.pub/developing-kubernetes-native-app-in-golang-with-hot-reload-directly-inside-the-cluster-f432a7d29384>

## 让我们讨论一下如何在 Golang 上直接在集群内部开发 K8s 原生应用，尤其是使用热重装

![](img/bb9b08cdbc790a89e0bc6f8a4cb2885d.png)

Alessandro Bianchi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我将告诉您我在 Golang 中开发 Kubernetes 本地应用程序的经验，该应用程序直接在 Kubernetes 集群中进行热重装。

> 通过这种方法，我们可以使用`client-go`库中的`[rest.InClusterConfig()](https://pkg.go.dev/k8s.io/client-go@v0.23.4/rest#InClusterConfig)`函数，并创建一个附加到角色的服务帐户来控制应用程序权限，因此配置永远不会离开集群本身。

## 准备 Golang 工作空间

让我们创建一个新文件夹，把`main.go`文件放在那里。我们也可以使用 go 模块，现在我们可以用“hello world”函数填充`main.go`。

## 启动 Minikube 集群

我在开发过程中使用了`minikube`,用 minikube 构建本地 kubernetes 集群很容易。让我们使用以下命令创建一个:

```
minikube start --mount-string="your-directory/your-go-app:/tmp/golang" --mount
```

在上面的命令中，我们创建了一个 minikube 并挂载了之前创建的 golang 工作空间。通过这种方式，我们可以更改 golang 代码，并能够在部署到集群时进行热重新加载。

## 热重装

对于热重新加载特性，我们将使用 [CompileDaemon](https://github.com/githubnemo/CompileDaemon) 来观察 golang 文件中的变化并触发重建。

我们可以将`CompileDaemon`作为 docker 图像中的`entrypoint`。当我们在运行的容器上开发应用程序时，这是拥有热重载能力的关键。

## 准备文档

好的，我们需要创建一个 docker 文件来…当然创建我们的应用程序映像。这只是一个简单的 docker 文件，你可以修改它来满足你的需要。我的看起来像这样:

以 CompileDaemon 作为入口点的 Dockerfile

我希望输出的二进制文件在`tmp/`目录中，所以如果你复制了上面的 docker 文件，不要忘记在你的 Golang 工作空间中创建`tmp/`目录。

下一步是创建 app 镜像，你知道怎么创建；)

## 准备开发工作区

因为我们在集群中运行应用程序，所以我们将为我们的应用程序创建一个 k8s 工作负载。这需要一些工作，但是我们希望我们的应用程序运行在生产 k8s 集群上。

所以我们首先要创建一个`service-account`、一个`role`和一个`rolebinding`:

服务帐户、角色和角色绑定

上述角色将授予对来自`my-namespace`名称空间的所有资源的访问权。当涉及到生产使用时，您应该调整特权以匹配您的需要。

在我们创建了`ServiceAccount`、`Role`和`RoleBinding`之后，我们将创建一个部署、PVC 和 PV，它们从前面的`minikube start`命令挂载主机目录，也就是`tmp/golang`目录。

pv、pvc 和部署

我假设您使用像`my-app:latest` :D 这样的基本名称构建了您的映像。有了这些，我们开发本地 k8s 应用程序并在集群中进行热重装的本地工作区就准备好了。

# 开发应用程序

准备好工作空间后，我们可以尝试来自`client-go` github 存储库的[集群内](https://github.com/kubernetes/client-go/tree/master/examples/in-cluster-client-configuration)示例。这个例子向您展示了如何配置一个客户机，以便从运行在 Kubernetes 集群内部的应用程序向 Kubernetes API 进行身份验证，这个例子与本文的目的相同。

# 结论

因此，我发现当我们需要一个在集群内与 k8s API 对话的应用程序，并使用`ServiceAccount`来控制应用程序权限和使用`*client-go*`库中的`[*rest.InClusterConfig()*](https://pkg.go.dev/k8s.io/client-go@v0.23.4/rest#InClusterConfig)`函数时，这种方法很有用，就像上面的例子一样，因此应用程序向 k8s 进行身份验证的集群配置永远不会离开集群本身。
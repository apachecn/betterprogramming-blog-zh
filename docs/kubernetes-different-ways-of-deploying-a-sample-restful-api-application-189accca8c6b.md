# kubernetes——部署示例 RESTful API 应用程序的不同方式

> 原文：<https://betterprogramming.pub/kubernetes-different-ways-of-deploying-a-sample-restful-api-application-189accca8c6b>

## 为了保持代码的灵活性

![](img/f0e20bb8c4614731011b3ba4d1c68ad1.png)

集装箱船由[登上](https://pixabay.com/users/dendoktoor-14802912/)[码头或](https://pixabay.com/photos/container-ship-container-transport-6631117/)登上

让我们假设有一个 API 可以部署，并且有人决定使用业界领先的编排软件——Kubernetes。

源自谷歌， *Kubernetes* 在希腊语中的意思是舵手或领航员，它正是为这个目的服务的——在这个例子中，指挥一个集装箱船队。它使扩展巨大的应用程序成为可能，并避免了向大众提供服务的常见陷阱。

尽管一开始这非常符合逻辑并且很容易上手，但是如果没有端口转发，要实现对集群的访问可能会有点困难。就我个人而言，我不太喜欢端口转发。因此，即使开发本地应用程序，我袖手旁观开发他们，使他们准备好部署到生产没有任何突破性的变化。

核心概念很容易掌握。但是，由于运行“真正的”——或者更正式地说，生产集群——需要一系列组件，大多数时候，人们从开发环境备选方案开始，如`minikube`、`microk8s`或内置的 Docker Desktop Kubernetes 引擎。

一般来说，有几种方法可以让流量进入 Kubernetes 集群，主要取决于它的部署位置。

每个选项的清单可以在[这里](https://github.com/piotrostr/pong)找到。

# 1.本地使用 Minikube 或 k3s 进行端口转发

```
kubectl create deployment [deployment] --image=piotrostr/pong
kubectl expose deployment [deployment]
```

然后，它可以从节点转发到端口:

```
kubectl port-forward service/[deployment] [port-host]:[port-container]
```

这对于调试单个部署可能很有用。

还有一个选项是使用`kubectl proxy`并直接与本地 Kubernetes API 交互，但是与下面的其他选项相比，这有点麻烦。

# 2.使用 Docker 桌面 Kubernetes 提供程序(参考资料:manifest-docker.yaml)

这是一个很好的例子，因为 docker-desktop 使用 vpnkit 来暴露任何负载平衡器并将流量转发到集群中。

这里的清单只包括负载平衡器服务和部署本身，不包括入口。

代码如下:

```
kubectl apply -f manifest-docker.yaml
```

它使应用程序准备好被`curl`调用。

值得注意的是，Docker Desktop 只将流量转发给`kind: LoadBalancer`的资源，这种方法不适用于`kind: NodePort`等。

我想说这是调试简单应用程序的必备工具，使用`skaffold dev`非常有效。更多关于 skaffold [的信息，请点击](https://github.com/GoogleContainerTools/skaffold)。

# 3.在 Google Kubernetes 引擎上(资源:`manifest-gke.yaml`)

**注意:**要求`gcloud`配置正确的项目并启用 GKE。

在清单中包含以下入口资源后，它可以用于在 GCP 云上无缝地供应集群。代码如下:

创建一个 GKE 集群，并将`kubectl`配置为使用`gcloud`上下文:

```
gcloud container clusters create-auto [cluster-name] \
  --region=[region]
gcloud container clusters get-credentials [cluster-name] \
  --region=[region]
```

应用 yaml 后，负载平衡器将从 GCP 进行配置，并将所有流量转发到集群中。

# 4.使用 Nginx 入口(资源:manifest-nginx.yaml)

使用以下命令安装它:

```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo [https://kubernetes.github.io/ingress-nginx](https://kubernetes.github.io/ingress-nginx) \
  --namespace ingress-nginx \
  --create-namespace
```

通过包括与第三个相同的入口资源并在`spec`下添加`ingressClassName: nginx`(以定义使用哪个控制器)。

对于`minikube`，有一个额外的启用入口的步骤，因为它不支持开箱入口。下面是如何做到这一点:

```
minikube addons enable ingress
minikube tunnel
```

这种方法支持外部流量进入集群和部署，而无需 GKE 或 EKS(AWS 的弹性 Kubernetes 服务)。该清单可以很容易地部署在虚拟机的单个节点集群上。

它使人受益于 Kubernetes 的强大功能，如自动伸缩和自动修复。它不会强迫用户使用 AWS/GCP 负载平衡服务和集群成本，这对于小型应用程序来说可能会越积越多。

NGINX 入口是负载平衡的，这意味着`curl`分布在五个 pod 之间(更改`manifest-nginx.yaml`中的`replicas`以修改 pod 编号)。

# 结论

可以看到，在 GCP 上引入流量甚至部署自动扩展群集的速度非常快。有一些警告，主要是由于生产集群和集群一的不同结构，集群一将在本地运行以测试其应用程序。如果没有 NGINX 这样的入口控制器，需要记住许多额外的步骤。对于`minikube`集群，需要启用入口，当它进入 Docker Desktop 时，需要确保服务资源是一个`LoadBalancer`。实现入口控制器似乎是最合适的选择，因为从本地部署转移到生产部署时几乎没有开销，而且我们大多数开发人员都习惯于使用`curl localhost`。

*最初发表于*[](https://gist.github.com/piotrostr/fbd029d428a336e3c7431e5fe20dcd25)**。**
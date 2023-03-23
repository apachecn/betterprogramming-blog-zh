# 借助网真，在 Kubernetes 应用上进行更快的开发和测试

> 原文：<https://betterprogramming.pub/do-faster-development-and-testing-on-kubernetes-apps-with-telepresence-b7eac604dca4>

## 使用 Telepresence 将您的代码更改即时部署到 Kubernetes 集群中

![](img/1a301e800837b1ca6f7742734f96c8bd.png)

来自 [Pexels](https://www.pexels.com) 的[安德里亚·皮亚卡迪奥](https://www.pexels.com/fr-fr/@olly)拍摄的照片。

我们来设定场景。

您目前正在开发一个新的应用程序。为了利用您闪亮的新云架构，您刚刚[创建了一个 AWS EKS 集群](https://faun.pub/spawning-an-autoscaling-eks-cluster-52977aa8b467)，您想好好利用它！

[](https://faun.pub/spawning-an-autoscaling-eks-cluster-52977aa8b467) [## 产生一个自动缩放的 EKS 星团

### 让您的 EKS 集群真正富有弹性

faun.pub](https://faun.pub/spawning-an-autoscaling-eks-cluster-52977aa8b467) 

首先，您将应用程序打包成一个容器。然后，您在本地运行它来运行一些测试，但是很快您意识到这还不够。毕竟，你的应用程序应该与大量的其他服务进行通信，你没有能力在本地运行所有的服务。

因此，您开始考虑在本地构建您的容器，对其进行标记，然后将其部署为 Kubernetes pod。在集群内部，您的 pod 可以访问这些其他外部服务，并获得更真实的运行时的额外好处。

但是在几次尝试之后，你很快意识到这个过程是缓慢的，繁琐的，并且没有效率。每次代码更改都意味着在 Kubernetes 中一次又一次地重新打包和部署您的容器。呃。真是浪费时间！

您最终说服自己，在笔记本电脑上进行本地测试，虽然仍然是一个一般的解决方案，但毕竟不是那么糟糕。尽管您的笔记本电脑不是一个好的测试环境，但是您需要快速发布特性。

应该有更好的办法！

在本文中，我将回顾市场上可用的一些工具**，**告诉你哪一个显然是最好的，并教你如何使用它来改善你的开发体验。

# 目录

```
1\. The Perfect Solution2\. Kubernetes Application Development Tools3\. Using Telepresence for Fast Development Loop
```

# 完美的解决方案

好的第一步是描述我们的期望。我们说的是什么样的开发者体验？

我的理想工作流程定义如下:

1.  我在 Kubernetes 上运行了一个复杂的应用程序，其中有许多 pods，我想为其中一个 pods 中的容器开发代码。
2.  我克隆了应用程序容器的代码报告，并修改了一堆代码。
3.  很快，新代码就在我的 Kubernetes pod 中运行了。没有码头工人建筑之类的。我真的是说*瞬间*！

对于基于解释器的流行语言(看看你，Python)，你有一个快速简单的解决方案:你进入 pod 并直接在容器内修改代码。但是让我们严肃点。没人会这么做，对吧？

让我们来看看市场上可用的一些工具。

# Kubernetes 应用程序开发工具

下面的列表并不详尽，但它很能代表市场。

## KSync——重视简单性的最佳解决方案

KSync 是一个非常简单的解决方案。它相当于 Kubernetes pods 的 [rsync](https://en.wikipedia.org/wiki/Rsync) 。

您可以配置该工具，使本地文件夹与 Kubernetes pod 中的远程目录保持同步。每次你对你的代码做一些改变，它几乎立即被重新部署到 pod。

举例来说，如果你正在做一些 Python 开发，这是真的。但是对于其他一些语言，您仍然需要重新构建或重启您的应用程序。

## 斯卡福德——CICD 球迷的最佳选择

我会谈到谷歌的 [Skaffold](https://skaffold.dev/) ，但还有很多类似的解决方案，如 [DevSpace](https://devspace.sh) 或微软的 [Draft](https://draft.sh/) 。

坦率地说，这些工具是运行在您笔记本电脑上的本地 CICD 解决方案。Skaffold 检测到代码变更，重新构建容器，并将其部署到 Kubernetes 中。您可以使用 YAML 配置一个工作流来完成所有这些事情。

如果你喜欢 CICD 的管道，你一定会有宾至如归的感觉。但是坦率地说，我不想等我的容器构建好了再部署我的代码。我们的完美解决方案似乎很遥远！

## 网真——对每个人都是最好的

Ambassador 的[远程呈现](https://www.telepresence.io/)使您能够通过双向代理机制将本地开发机器无缝连接到集群。

远程呈现运行时:

*   您可以从您的笔记本电脑直接连接到集群中的每个 Kubernetes 工作负载，只需使用它们的 Kube 服务名，就像您在集群中一样！
*   您可以拦截到 Pod 的流量，并将其重定向到您的笔记本电脑(例如，应用程序的本地运行版本)。

好像真的不错。但是怎么用呢？

# 将网真用于快速开发循环

## 设置

我们将使用示例 [Whoami](https://github.com/traefik/whoami) 应用程序，这是一个简单的 echo 服务器，打印关于传入 HTTP 请求的信息。

首先，构建应用程序:

```
$ git clone [git@github.com](mailto:git@github.com):traefik/whoami.git
$ cd whoami
$ make image
```

然后，您可以将这个映像推送到您的容器注册中心。我个人在这个例子中使用了一个[类](https://kind.sigs.k8s.io/docs/user/quick-start/)集群，因此使用以下命令将映像直接加载到集群中非常容易:

```
kind load docker-image traefik/whoami --name=my-cluster
```

最后，在 Kubernetes 中安装应用程序，并检查它是否正在运行:

```
$ kubectl create deployment whoami --image=traefik/whoami --port=80
$ kubectl expose deploy whoami --port=80
$ kubectl get all -l=app=whoamiNAME                          READY   STATUS    RESTARTS   AGE
pod/whoami-754f6db6d4-f54qc   1/1     Running   0          2m18sNAME             TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/whoami   ClusterIP   10.96.201.56   <none>        80/TCP    65sNAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/whoami   1/1     1            1           2m18sNAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/whoami-754f6db6d4   1         1         1       2m18s
```

查询应用程序以确保:

```
$ kubectl port-forward svc/whoami 8080:80
$ curl [http://localhost:8080](http://localhost:8080)Hostname: whoami
IP: 127.0.0.1
IP: ::1
IP: 192.168.247.24
IP: fe80::8cbd:8fff:fecb:396c
RemoteAddr: 127.0.0.1:49938
GET / HTTP/1.1
Host: localhost:8080
User-Agent: curl/7.68.0
Accept: */*
```

停止你的端口转发，因为你不再需要它了！

## 运行远程呈现

Telepresence 需要您系统上的 root 权限来调整您的系统 IP 表规则。“为什么？”你可能会问。允许每个 Kubernetes pod 和服务都可以在本地解析。

通过运行以下命令设置网真:

```
$ telepresence connectLaunching Telepresence Daemon v2.3.0 (api v3)
Connecting to traffic manager...
Connected to context kind-telepresence ([https://127.0.0.1:40671](https://127.0.0.1:40671))
```

现在，您应该能够直接从笔记本电脑查询所有集群服务，而无需运行端口转发或入口:

```
$ curl [http://whoami.default.svc:80](http://whoami.default.svc:80)Hostname: whoami-754f6db6d4-f54qc
IP: 127.0.0.1
IP: ::1
IP: 192.168.247.26
IP: fe80::5414:f4ff:fe5a:d652
RemoteAddr: 192.168.247.25:53832
GET / HTTP/1.1
Host: whoami.default.svc
User-Agent: curl/7.68.0
Accept: */*
```

单说这个功能确实不错，但是我们可以做得更好。

## 在本地进行更改

我们想对应用程序进行一些更改。

首先，我们告诉网真拦截所有定向到 whoami 服务的流量到您的笔记本电脑:

```
$ telepresence intercept whoamiUsing Deployment whoami
intercepted
    Intercept name    : whoami
    State             : ACTIVE
    Workload kind     : Deployment
    Destination       : 127.0.0.1:8080
    Volume Mount Point: /tmp/telfs-926534300
    Intercepting      : all TCP connections
```

如果您有兴趣并检查部署和服务，您将会看到:

*   whoami 服务的目标端口现在是`tx-80`而不是`80`。
*   现在有一个边车容器暴露了你的 whoami 舱中的`tx-80`港。
*   这个边车是一个代理，将流量重定向到您的笔记本电脑(更准确地说，到`127.0.0.1:8080`)。

使用`curl`，我们注意到我们的应用程序不再工作了:

```
$ curl [http://whoami.default.svc:80](http://whoami.default.svc:80)
curl: (52) Empty reply from server
```

这里没有惊喜！您的请求在被重定向到您的笔记本电脑上的`127.0.0.1:8080`之前，会被发送到 Kubernetes 内部的 pod，但是那里还没有运行任何东西。

让我们在本地启动应用程序，并通过设置一些标志来更改默认消息:

```
$ go run app.go -port=8080 -name="Running on my laptop"Starting up on port 8080
```

当我们现在查询 URL 时，Kubernetes 流量被重定向到您的本地修改应用程序:

```
$ curl [http://whoami.default.svc:80](http://whoami.default.svc:80)
Name: Running on my laptop
Hostname: xps
IP: 127.0.0.1
IP: ::1
...
RemoteAddr: 127.0.0.1:37780
GET / HTTP/1.1
Host: localhost:8080
User-Agent: curl/7.68.0
Accept: */*
```

我们正在看到我们的变化！现在，您可以对您的应用程序进行任意数量的代码更改，借助网真的代理魔力，就好像您的本地应用程序正在集群中运行一样。即时反馈循环！

# 结论

后端堆栈每年都在变得越来越厚，掌握它的全部内容是一个真正的挑战——特别是对于需要掌握 Kubernetes 的开发人员来说。

Kubernetes 是已经太长的开发循环中的一个新阶段，这个循环就是进行代码更改、构建工件并部署它。远程呈现确实减轻了痛苦，并使其成为一种愉快、简单的体验。
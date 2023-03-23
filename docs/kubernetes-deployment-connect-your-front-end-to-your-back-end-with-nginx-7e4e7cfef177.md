# Kubernetes 部署:用 Nginx 将您的前端连接到后端

> 原文：<https://betterprogramming.pub/kubernetes-deployment-connect-your-front-end-to-your-back-end-with-nginx-7e4e7cfef177>

## 使用 Kubernetes 部署全栈应用

![](img/9f578370f73d4b4252aeb913a8859180.png)

约瑟夫·巴里恩托斯在 [Unsplash](https://unsplash.com/s/photos/helm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Kubernetes 是目前最好的编排工具之一。此外，它得到了 Google 的支持，所以我们对 Kubernetes 能够提供的支持和性能没有任何疑问。

在本文中，我们将使用 [Minikube](https://kubernetes.io/docs/setup/learning-environment/minikube/) 来了解如何在 Kubernetes 中部署全栈应用。

我记得当我在进行我的第一次 Kubernetes 部署时，我意识到部署一个 Kubernetes 全栈应用程序并连接前端和后端可能有点棘手。所以我决定写一篇教程来帮助你部署你的全栈应用。

# 先决条件

本教程假设您对 Kubernetes 有基本的了解。为了使本教程与主题相关，强烈建议您至少阅读这些主题。

*   [Kubernetes 部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
*   [Kubernetes 服务](https://kubernetes.io/docs/concepts/services-networking/service/)
*   [码头工人](https://docs.docker.com/)

另外，如果你在本地机器上工作，请安装`minikube`、`kubectl`和`docker`。

# 我们今天要部署什么？

最常见的全栈应用至少有三个组件:

*   数据库ˌ资料库
*   API 服务器
*   前端应用程序

本教程旨在帮助您熟悉 Kubernetes 部署。一旦您理解了如何部署上述三个组件，您就能够在 Kubernetes 中部署任何应用程序。

我们将使用 Node.js 作为后端，使用 [MongoDB](https://www.mongodb.com/) 作为数据库，为了简单起见，我们将使用一个带有 Ajax 的简单 HTML 文件作为前端。您可以选择 React 或 Angular 或您选择的任何前端框架。框架的选择不会影响任何步骤。

一步一步，我们将访问所有三个组件。你可以在 [Github](https://github.com/vidu171/Kubernetes-deployment) 上找到与本文相关的所有代码。

那我们就直入主题吧。

# 后端

对于本教程，我们将保持我们的后端非常简单。我们有一个简单的节点服务器，带有 [Express](https://expressjs.com/) 和[mongose](https://mongoosejs.com/docs/)。

我们使用 Mongoose 连接到我们的 MongoDB。我已经为快递选择了端口`3000`。

注意:我们使用了`mongo-service`作为数据库 URL 的主机名。这是因为 Kubernetes 为您提供了名为[服务](https://kubernetes.io/docs/concepts/services-networking/service/)的抽象。我们将在本教程的后半部分创建一个`mongo-service`服务。并且您可以使用服务名作为主机名来连接到您的应用程序(在本例中为`mongo-service`)。

TLDR；如果您有一个名为`mongo-service`的 Kubernetes 服务，您可以使用`mongo-service`作为您的应用程序的 DNS 名称。Kubernetes 负责将请求转发到相应的 pod。别担心，我们会重新讨论的。

我们还应该向我们的后端应用程序添加一个 route `/api`。

就是这样。我们现在已经完成了前端应用程序。我们现在要做的就是将应用程序容器化并编写 Kubernetes 对象。

## 容器化节点应用

要将应用程序部署到 Kubernetes，我们需要该应用程序的 Docker 映像。所以在这一步，我们将容器化我们的后端应用程序。下面是我们的节点应用程序的一个相当简单的`Dockerfile`。

我已经使用`node-alpine`作为我的基本图像。要创建 Docker 映像，请运行以下命令:

```
docker build -t backend .
```

现在我们有一个图像叫做`backend`。接下来，我们将编写 Kubernetes 对象。

## 用于节点应用程序的 Kubernetes 对象

对于任何 Kubernetes 部署，我们都需要 Kubernetes 对象。这些 [Kubernetes 对象](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)代表你的容器的状态。

我们将创建一个 Kubernetes 部署对象。部署对象包含诸如使用的 Docker 映像、副本集的数量、资源分配等信息。

我们将创建一个名为`node-deployment`的部署。我们将使用我们的后端映像并创建容器的一个副本。在`node-deployment.yaml`文件中，写下:

对于这个部署对象，我们告诉 Kubernetes 创建一个名为`node-deployment`的部署，它有一个名为`node-pod`的 pod 副本集。`node-pod`是一个`backend`图像的集装箱，涉及集装箱港口`3000`。

要创建此部署，只需运行以下命令:

```
kubectl apply -f node-deployment.yaml
```

我们已经创建了一个部署，但是我们无法与之通信。为了解决这个问题，我们必须创建一个[服务](https://kubernetes.io/docs/concepts/services-networking/service/)。服务是 Kubernetes 中的一个抽象概念，它为 pod 分配 IP 地址，为一组 pod 分配一个 DNS。正如我们之前在连接 MongoDB 时看到的，我们使用`mongo-service`作为主机名。我们将编写一个名为`node-service`的服务，这样我们可以从前端连接到我们的后端应用程序。

我们的`node-service.yaml`文件看起来像这样:

我们现在已经创建了一个类型为`LoadBalancer`的服务。该服务被绑定到名为`node-pod`的 pod 的所有副本。现在，您可以使用主机名`node-pod`与节点服务器对话。

使用以下命令应用服务:

```
kubectl apply -f node-service.yaml
```

我们的后端步骤到此结束。我们现在可以开始开发我们的前端应用程序了。

# 前端

对于前端，我使用一个简单的 HTML 文件和 jQuery 来保持本教程的简单明了。但是您可以使用自己选择的任何前端框架。无论您使用哪个框架，都不会影响这些步骤。

为了服务我们的前端应用程序，我们将使用 [Nginx](https://www.nginx.com/) web 服务器。我们使用 Nginx 服务器的原因是它提供了一个代理。我们将需要 Nginx 代理将我们的请求传递给我们的节点部署。

我们将创建一个名为`index.html`的非常简单的 HTML 文件。我们将使用 Ajax 来查询我们的节点服务器。

从网络浏览器，如果您尝试连接到`[http://node-server:3000/api](http://node-server:3000/api)`，您将得到“未找到主机”错误。这是因为 DNS `node-server`没有在互联网上注册。

一种解决方法是将请求发送到前端应用程序，比如说发送到一个路径`/api`。使用 Nginx，我们可以将查询转发给后端应用程序。

我的意思是，我们将把所有后端调用发送到前端应用程序中的`/api`路径，并配置 Nginx，使其将请求传递给`[http://node-service/api](http://node-service/api)`。

我们的`index.html`文件看起来像这样:

看到我们如何使用`/api`作为我们的网址了吗？

```
const url = "/api/"
```

现在我们将编写 Nginx conf `Nginx.conf`文件，将请求从路径`/api`转发到`[http://node-service/api](http://node-service/api)`。

我们的`Nginx.conf`文件应该是这样的:

这个配置文件告诉 Nginx，对于路径`/api`的所有请求，将请求发送到`[http://node-service](http://node-service):3000/api`。你可以在文档中阅读更多关于`proxy_pass` [的内容。](http://nginx.org/en/docs/http/ngx_http_upstream_module.html)

## 集装箱化前端

接下来，我们将为 Kubernetes 部署容器化应用程序。在我们的前端图像中，我们需要用 conf 文件替换默认的 Nginx conf 文件。

我们的`Dockerfile`会是这样的:

第 4 行从图像中删除默认的 Nginx 配置文件

第 5 行将我们的新配置文件复制到`Conf.d`文件夹。

至此，我们已经成功地为前端应用程序创建了 Docker 映像。让我们进入下一步，为我们的前端应用程序创建 Kubernetes 部署和服务。

## Kubernetes 前端对象

Nginx 的部署对象将与我们在后端应用程序中创建的部署对象非常相似。

运行`kubectl apply`之后，我们有一个名为`frontend-deployment`的部署，它有一个名为`frontend-pod`的 pod 的副本，该副本是使用`frontend`映像制作的。

同样，我们的`nginx-service.yaml`文件应该是这样的:

这创建了一个名为`frontend-service`的`LoadBalancer`类型的服务，它被绑定到名为`frontend-pod`的 pod 的所有副本。

我们的前端步骤到此结束。现在我们可以进行最后一步，即数据库。

# 数据库ˌ资料库

在 Kubernetes 中部署我们的数据库是最简单的步骤，因为我们不需要为它创建任何 Docker 映像。而且非常容易配置。

我们不需要创建 MongoDB 映像，因为我们可以直接使用来自 [Docker Hub](https://hub.docker.com/_/mongo) 的 MongoDB 映像。我们可以直接开始编写部署对象。

这创建了一个简单的 Mongo 部署。容器中的`env`键告诉 Kubernetes，这些是创建图像时必须使用的环境值。我们基本上是告诉 Kubernetes 用 pod 的一个副本集`mongo-pod`创建一个部署，它有一个 Mongo 容器，数据库的名称是`database`。

最后一步，我们将编写我们的`mongo-service`。

在执行完`kubectl apply`命令后，我们应该已经创建了一个名为`mongo-service`的服务，该服务绑定到所有名为`mongo-pad`的副本。现在您可以使用主机名`mongo-pod`与 MongoDB 对话。

# 结论

有了这个，我们在 Kubernetes 中部署了我们的全栈应用。

使用 Nginx `proxy_pass`衍生工具，我们能够将前端部署连接到后端部署。大多数人在 Kubernetes 中连接前端和后端时都会遇到问题。

现在，您可以按照上述步骤部署一个完整的全栈应用程序。

快乐编码。
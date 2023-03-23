# 使用“werf”在 Kubernetes 上构建和部署一个简单的应用程序

> 原文：<https://betterprogramming.pub/building-and-deploying-a-simple-app-to-kubernetes-using-werf-51bee0b3718d>

## 知道如何使用开源工具

![](img/8d4a737fa17121894a99a89f88f23a35.png)

照片由[迈克尔](https://unsplash.com/@michael75?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

本文着眼于构建一个极简应用程序的 Docker 映像，并使用名为 werf 的开源工具将其部署到 Kubernetes 集群。我还将展示如何在应用程序的代码和运行它的基础设施中进一步交付您的更改。

我将使用一个基于 shell 脚本的小型 echo 服务器作为示例应用程序。该服务器返回字符串`Hello, werfer!`以响应`/ping`端点请求。

**NB**。您可以探索这个极简应用程序的所有文件，并从 [*这个资源库*](https://github.com/werf/first-steps-example) *下载它们。*

我们将在本文中使用的 Kubernetes 集群是基于 [minikube](https://minikube.sigs.k8s.io/docs/) 的，因此您不需要任何特定的硬件来遵循指令:您的普通台式机/笔记本电脑就可以了。

# 简短介绍

对于不熟悉这个 CLI 实用程序的人来说，werf 在 Kubernetes 中实现了完整的应用交付工作流。它使用 Git 作为应用程序代码和配置的单一来源:

*   每次提交反映一个特定的应用程序状态；
*   `werf`将其与容器注册中心(通过构建最终 Docker 映像的缺失层)和 Kubernetes 中运行的应用程序(通过重新部署已更改的资源)同步；
*   `werf`还使用基于 Git 历史和用户定义的策略的独特算法来清理容器注册中心中过时的工件。

werf 的显著特点是集成了许多面向开发者和 DevOps/SRE 工程师的知名工具，如 Git、Docker、container registry、CI system、Helm 和 Kubernetes。这些组件组合在一起，确保固执己见的 CI/CD 工作流将您的应用交付到 Kubernetes。将它们结合在一起可以最大限度地减少实现 CI/CD 所需的工作。

让我们看看它的实际效果。

# 准备系统

在开始之前，在你的系统上安装最新的稳定版本的 werf(来自稳定发布渠道的 v1.2)(参考[官方文档](https://werf.io/installation.html))。

文中给出的所有命令和动作都适用于 Linux 操作系统(在 Ubuntu 20.04.03 上测试)。虽然对于其他系统(如 Windows 和 macOS)来说，这些命令通常是相同的，但可能存在细微的差异。*如果你对你的操作系统有任何特殊的要求，请查看本文末尾的链接。*

# 建立形象

首先，我们必须创建应用程序本身。让我们创建一个工作目录(在我们的例子中，它是用户主目录中的 app 目录):

```
mkdir app
```

在目录中创建一个 hello.sh 脚本，内容如下:

hello.sh

在目录中初始化一个新的 Git 存储库，并提交第一批更改——我们刚刚创建的脚本:

```
cd ~/app
git init
git add .
git commit -m initial
```

因为我们的应用程序将在 Docker 中构建和运行，所以让我们也创建一个 Docker 文件，其中包含构建应用程序映像的说明:

Dockerfile 文件

为了让 werf 使用 Dockerfile 进行构建，我们需要在项目根目录下创建一个`werf.yaml`配置文件，其中包含 Dockerfile 描述:

werf.yaml

> 在 [werf/first-steps-example](https://github.com/werf/first-steps-example) 存储库的 [this directory](https://github.com/werf/first-steps-example/tree/main/001_build) 中有一个存储库，其中包含到目前为止创建的文件。

现在，我们已经**准备好构建我们的应用程序**。注意，您需要将所有的变更提交到项目存储库(Dockerfile，等等)。)之前，即首先运行以下命令:

```
git add .
git commit -m FIRST
```

使用下面的命令开始构建:

```
werf build
```

您应该会看到以下输出:

werf 构建输出

要检查构建是否成功，请使用以下命令运行应用程序:

```
werf run app --docker-options="-ti --rm -p 8000:8000" -- /app/hello.sh
```

让我们仔细看看上面的命令。`—-docker-options`选项指定了一组 [Docker 相关参数](https://docs.docker.com/engine/reference/run/)，而要在容器中执行的命令(在末尾)前面有两个连字符。

让我们检查一下**是否一切正常，并按预期运行**。为此，请在您的浏览器中访问[http://127 . 0 . 0 . 1:8000/ping](http://127.0.0.1:8000/ping)或在另一个终端中使用以下 CURL 请求:

```
curl [http://127.0.0.1:8000/ping](http://127.0.0.1:8000/ping)
```

你应该看到“你好，沃弗！”问候。此外，正在运行的容器的日志中应该会出现以下消息:

```
GET /ping HTTP/1.1
Host: 127.0.0.1:8000
User-Agent: curl/7.68.0
Accept: */*
```

# 准备部署

构建一个应用程序是问题的一半(甚至是三分之一)。毕竟，您仍然需要将它部署到生产服务器上。为此，让我们创建一个本地“生产”Kubernetes 集群，并配置 werf 来使用它。以下是需要采取的步骤列表:

*   安装并运行 minikube，一个最小的 Kubernetes 发行版(它是测试目的的理想选择)；
*   安装 NGINX 入口控制器，负责流量路由的集群组件；
*   编辑`/etc/hosts`文件，使用应用程序域名启用群集访问；
*   登录 Docker Hub，使用所需的凭证设置密码；
*   将应用程序部署到 Kubernetes。

## 1.安装和运行 minikube

首先，按照官方文档中的描述安装 [minikube](https://minikube.sigs.k8s.io/docs/start/) 。如果您已经安装了它，请确保您的版本是最新的。

让我们使用 minikube 启动一个 Kubernetes 集群:

```
# Delete the existing minikube cluster (if there is one).
minikube delete
# Start a new minikube cluster.
minikube start --driver=docker
```

设置默认的 Kubernetes 名称空间，这样您就不必在每次使用 kubectl 时都输入它(注意，我们只配置默认名称，并不创建名称空间本身——我们稍后会这样做):

```
kubectl config set-context minikube --namespace=werf-first-app
```

如果您没有安装 kubectl，有两种方法可以安装它:

*   使用[官方文件](https://kubernetes.io/docs/tasks/tools/install-kubectl/)手动安装；
*   使用 minikube 附带的 kubectl 二进制文件。为此，请运行以下命令:

```
alias kubectl="minikube kubectl --"
echo 'alias kubectl="minikube kubectl --"' >> ~/.bash_aliases
```

> 如果您选择第二个选项，那么在您第一次使用上面的别名调用 kubectl 时，将会下载并安装这个实用程序。

让我们通过列出在新创建的集群中运行的所有 pod 来检查 kubectl 是否工作:

```
kubectl get --all-namespaces pod
```

> Pod 是一个短暂的 Kubernetes 实体，它托管一个或多个应用程序容器以及在这些容器之间共享的资源。

运行此命令应该会产生类似于下面的输出:

kubectl get - all-namespaces pod

仔细观察`READY`和`STATUS`列。如果所有的 pod 都有一个`Running` 状态，并且 Ready 列中的数字是 1/1(注意左边的数字必须等于右边的数字)，那么我们的集群就可以使用了。如果您没有看到与上面类似的输出，请尝试等待一段时间，然后重新运行上面的命令(可能有些 Pods 没有时间启动)。

## 2.安装 NGINX 入口控制器

下一步是安装和配置 NGINX 入口控制器。它会将外部 HTTP 请求路由到我们的集群。

使用以下命令安装它:

```
minikube addons enable ingress
```

此过程可能需要一些时间，具体取决于您电脑的性能。例如，我的机器花了大约四分钟安装这个插件。

该过程完成后，您应该会看到以下成功消息:

```
The 'ingress' addon is enabled
```

等待加载项启动并检查它是否工作:

```
kubectl -n ingress-nginx get pod
```

您应该会看到类似下面的输出:

kubectl -n ingress-nginx get pod

最后一行是我们感兴趣的。`Running`状态表示一切正常，控制器正在运行。

## 3.编辑主机文件

设置环境的最后一步是编辑`hosts`文件，这样所有对测试域的请求都会在本地集群中结束。

在我们的例子中，我们将使用`werf-first-app.test`地址。在终端中运行`minikube ip`命令，查看它是否输出有效的 IP 地址。如果输出**和**看起来不像有效的 IP 地址(在我的例子中是`192.168.49.2`，返回并重新安装 minikube 集群。

接下来，运行以下命令:

```
echo "$(minikube ip) werf-first-app.test" | sudo tee -a /etc/hosts
```

您可以通过查看 hosts 文件来检查上述命令是否成功。应该有这样一句台词:`192.168.49.2 werf-first-app.test`。

现在，让我们看看一切是否如预期的那样工作。为此，我们将向应用程序端点发送一个 CURL 请求:

```
curl http://werf-first-app.test/ping
```

在这种情况下，NGINX 入口控制器应返回 404 页面，指示端点尚不可用:

404 页

## 4.登录 Docker Hub

现在，我们需要为构建的图像建立一个存储库。我们建议使用私有的 Docker Hub 库。为了方便起见，我们将使用应用程序名称(`werf-first-app`)作为存储库名称。

通过运行以下命令登录 Docker Hub:

```
docker login
Username: <DOCKER HUB USERNAME>
Password: <DOCKER HUB PASSWORD>
```

您应该会看到`Login Succeeded`消息。

## 5.为注册表访问创建密码

要使用私有注册表存储图像，您必须使用注册表登录凭据创建一个密码。请注意，机密必须位于与应用程序相同的名称空间中。

因此，您需要事先为应用程序创建一个名称空间:

```
kubectl create namespace werf-first-app
```

您应该看到新名称空间已经创建的消息(`namespace/werf-first-app created`)。

接下来，创建一个名为`registrysecret`的秘密:

```
kubectl create secret docker-registry registrysecret \
  --docker-server='[https://index.docker.io/v1/'](https://index.docker.io/v1/') \
  --docker-username='<DOCKER HUB USERNAME>' \
  --docker-password='<DOCKER HUB PASSWORD>'
```

如果成功，您应该会看到一条消息`secret/registrysecret created`。如果您在创建密码时出错，使用`kubectl delete secret registrysecret`命令将其删除并重新创建。

> 注意，上面描述的方法是在 Kubernetes 中创建秘密的标准方法。

这就完成了将应用程序部署到集群的环境准备工作。

我们将使用上面创建的秘密，通过在设置 pod 时在`imagePullSecrets`字段中指定它，从注册表中提取应用程序映像。

# 将应用程序部署到集群

在部署应用程序之前，我们必须创建定义所需资源的 Kubernetes 清单。为此，我们将使用舵图表格式。Helm 图表(或 Helm 包)包含在 Kubernetes 集群中运行应用程序或服务所需的所有资源定义。

我们的应用程序需要三个 K8s 资源。部署负责在容器中运行应用程序，而入口和服务分别在集群中路由外部和内部流量。

我们以下面的文件结构结束:

文件的结构

我们将把上面提到的清单放在隐藏的`.helm`目录的`templates`子目录中。

**注意**:您必须将包含清单的目录添加到`.dockerignore`文件中，以便从 Docker 映像构建的上下文中排除这些文件:

```
/.helm/
```

让我们仔细看看我们的资源清单。

## 1.部署

部署资源创建一组用于运行应用程序的 pod。看起来是这样的:

部署

这里，`{{ .Values.werf.image.app }}`模板变量用于插入应用 Docker 图像的全名。注意，您必须使用在`werf.yaml`中使用的相同组件名(在我们的例子中是 *app* )。

werf 自动将待构建图像的全名和其他[服务值](https://werf.io/documentation/v1.2/advanced/helm/configuration/values.html)插入舵图表值(`.Values`)。您可以使用`werf`键访问它们。

werf 仅在添加的文件发生变化时(Dockerfile `COPY/ADD`指令中使用的文件)或`werf.yaml`本身发生变化时重建映像。重新构建会导致映像标记发生变化，这将自动导致部署更新。如果这些文件没有更改，应用程序映像及其相关部署将保持不变，这意味着应用程序在集群中的状态是最新的。

## 2.服务

服务资源允许集群中的其他应用程序连接到我们的应用程序。看起来是这样的:

服务

## 3.进入

与前面的资源不同，服务入口开放了从集群外部*对我们的应用的访问。它的目的是将`werf-first-app.test`公共领域的流量重定向到我们的 Kubernetes 服务。看起来是这样的:*

## 部署应用程序

让我们将配置更改(部署应用程序所需的 K8s 资源)提交给 Git:

```
git add .
git commit -m FIRST
```

> 在 [werf/first-steps-example](https://github.com/werf/first-steps-example) 存储库的 [this directory](https://github.com/werf/first-steps-example/tree/main/002_deploy) 中有一个存储库，其中包含到目前为止创建的文件。

使用以下命令启动部署过程:

```
werf converge --repo <DOCKER HUB USERNAME>/werf-first-app
```

让我们看看这个过程是否成功:

werf 汇聚输出

再次运行:

```
curl [http://werf-first-app.test/ping](http://werf-first-app.test/ping)
```

您应该会看到以下响应:

```
Hello, werfer!
```

恭喜，您已经成功地将应用程序部署到了 Kubernetes 集群中！

# 对应用程序进行更改

让我们试着修改我们的应用程序，看看 werf 如何将它重新构建并重新部署到集群中。

## 缩放比例

我们的 web 服务器作为 web 优先应用部署的一部分运行。让我们看看有多少副本正在运行:

应用程序的副本

目前，我们只有一个运行的副本(以 werf-first-app 开始的那个)。将其数量增加到四个:

```
kubectl edit deployment werf-first-app
```

将打开一个包含清单文件内容的文本编辑器。找到`spec.replicas`行，将副本数改为四:`spec.replicas=4`。稍等片刻，检查正在运行的应用程序副本的数量:

应用程序的 4 个副本

在这种情况下，我们通过直接编辑清单并绕过 Git，手动增加了集群中副本的数量。现在，运行`werf converge`命令:

```
werf converge --repo <DOCKER HUB USERNAME>/werf-first-app
```

再次检查副本的数量:

副本的数量

如您所见，**正在运行的副本数量对应于 Git** 中存储的清单中指定的数量(我们没有编辑它)。这是因为 werf 已经将集群状态恢复到当前 Git 提交中描述的状态。这种机制叫做[Giterminism](https://werf.io/documentation/v1.2/advanced/giterminism.html)(Git+determinism)。

为了尊重这一原则并正确地做每一件事，您需要改变存储库中项目文件的副本数量。因此，让我们编辑`deployment.yaml`文件并将更改提交到存储库:

部署

使用以下命令提交更改并重建应用程序:

```
werf converge —-repo <DOCKER HUB USERNAME>/werf-first-app
```

现在，让我们再次检查副本的数量:

应用程序的 4 个副本

如您所见，有四个副本。让我们把它们的数量减回 1。为此，编辑`deployment.yaml`文件，提交更改，并通过`werf converge`命令重新部署应用程序。

## 更改代码

目前，我们的应用程序用`Hello, werfer!`来响应，让我们更改答案并将更新后的应用程序重新部署到集群中。在编辑器中打开`hello.sh`，用别的东西替换现有的行(例如`Say hello one more time!`):

新 hello.sh

现在，提交更改并运行`werf converge`。我们最终会得到什么？

```
curl [http://werf-first-app.test/ping](http://werf-first-app.test/ping)
Say hello one more time!
```

恭喜你，一切正常，运行正常！

# 外卖食品

在本文中，我们使用 werf 为 Kubernetes 集群构建并部署了基本应用程序。我希望它能帮助您熟悉 werf，并获得一些将应用程序部署到 K8s 的经验。

本文基于[在线自学指南](https://werf.io/guides.html)的*第一步*章节。为了尽可能地简洁，我选择不深入完整指南中涵盖的理论问题，例如 Kubernetes 模板和清单、运行应用程序的基本 K8s 资源(部署、服务、入口)、werf 操作模式和 Giterminism、在 werf 中使用 Helm 的特性等。你可以在上述指南中了解更多。那里还提供了更具体的说明，包括针对其他操作系统的说明。

欢迎在文章评论或 [werf_io](https://t.me/werf_io) 电报聊天中提出任何问题和建议。

# 资源

*   [werf . io](https://werf.io/)—werf 实用程序官方网站；
*   [Giterminism](https://werf.io/documentation/v1.2/advanced/giterminism.html) —关于 Giterminism，实用程序使用的原理；
*   [GitHub](https://github.com/werf/first-steps-example) —源代码库。
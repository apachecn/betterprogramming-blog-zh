# 如何使用 Kind 在 Kubernetes 集群上持续测试和部署您的舵图

> 原文：<https://betterprogramming.pub/how-to-continuously-test-and-deploy-your-helm-charts-on-kubernetes-clusters-using-kind-d71e3585d2dc>

## 设置您的 CI/CD 工具，以便在短暂的 Kubernetes 集群上轻松测试和发布图表

![](img/7ce516c05e08f0ef174c864adb73adb9.png)

由[马库斯·温克勒](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

当您为 Kubernetes 打包应用程序时，通常有两种选择:

*   Helm 使用模板和类似包管理器的方法将应用程序捆绑成“图表”。
*   [Kustomize](https://kustomize.io/) 允许您修补您的原始资源，以生成新的清单并达到所需的配置。

然而，头盔和 Kustomize 并不互相排斥，你可以同时受益于他们两个。例如，您可以使用 Helm 模板生成大部分资源，并使用 Kustomize 对生成的清单进行一些特别的修补。

无论您使用什么技术，您都需要构建图表或“库定制化”来包装您的应用程序。然后，您可以分发它们，但是您确定它们按照预期工作吗？它们能在 Kubernetes 集群中正确部署吗？还是在您的客户群中使用各种 API 版本？

在本文中，我将向您展示如何针对真正的 Kubernetes 集群构建、测试和持续部署您的 Helm 应用程序。

# 目录

```
1\. [Helm Basics](#d7ac)2\. [Considering a CI/CD Approach for Charts](#7f87)3\. [Choosing the Right Tools](#025c)4\. [Writing the Build, Test, and Deploy Jobs](#1cfe)5\. [Handling Multiple Versions of Kubernetes](#7e9e)6\. [Publishing the Chart](#18a8)
```

# 头盔基础知识

让我们快速回顾一下舵的基本原理:

*   Helm 是模板工具:Helm 图表由 Kubernetes 清单的 Golang 模板和一个包含图表名称和版本等内容的`Chart.yaml`元数据描述符组成。
*   Helm 是一个打包工具:您的图表被打包成一个 tar 归档文件，并上传到一个远程版本化的 Helm 存储库。Helm 然后允许您从存储库中推或拉图表。
*   Helm 是一个部署工具:您给 Helm CLI 一个图表和值替换作为一个`values.yaml`文件，Helm 将生成 Kubernetes 清单并将其部署到您的集群。

# 考虑图表的 CI/CD 方法

## 林挺

它是在许多层面上完成的:

*   确保你的`values.yaml`文件没有外观问题或显示出有问题的迹象(例如重复按键)。
*   拥有一个包含每个强制字段和有用信息的`Chart.yaml`，就像一个维护者知道*和*确实存在。
*   正确编写的清单模板，提供所有默认设置，没有 Golang 模板错误。

## 建筑物

您需要运行`helm package`命令来构建图表并输出准备上传到存储库中的归档文件。

## 出版

你在一个远程舵库上推送图表。在此之前，您应该对在`Chart.yaml`中增加图表的版本感兴趣！没有什么比引入突破性的变化而忘记修改图表版本更糟糕的了。

## 部署测试

构建的图表真的可以在 Kubernetes 集群上部署吗？此外，它可以针对多个版本的 Kubernetes API 进行部署吗？如果您(或您的客户)在各种集群中安装图表，这一点很关键，因为您的清单可能依赖于特定的`kind`和`apiVersion`组合。你需要动态构建一些集群来完成这个任务，我们将使用[种类](https://kind.sigs.k8s.io/docs/user/quick-start/)来产生它们。

如果您的图表中有[测试作业，这一步可能还涉及到运行`helm test`命令，以确保您的应用程序按预期运行。](https://helm.sh/docs/topics/chart_tests/)

# 选择正确的工具

首先，我们将利用 CI 解决方案。我们将在这里使用 [GitLab CI](https://about.gitlab.com/) ，但是任何东西都可以。只要您可以运行 bash 脚本并拥有一个支持 Docker 的环境，您就可以开始了。

为了运行我们的验证工作，我们将使用官方的 Helm [图表测试](https://github.com/helm/chart-testing)工具。这是一个漂亮的小脚本，通过它的`lint`和`install`命令提供了验证图表所需的一切。

当然，我们会要求[掌舵](https://helm.sh/)。

# 编写构建、测试和部署作业

## Lint 和 build

我们首先使用`ct`工具林挺我们的图表。它将为您执行以下操作:

*   用`yamllint`检查你的`values.yaml`文件。
*   确保你的维护者是 GitHub 上的真人。
*   检查你的舵图版本是否增加。
*   对父文件夹下的大量图表进行批量操作。

此外，`ct`将检查一个远程主分支的 diff，并且只在事情发生变化时运行林挺。漂亮。

然后，我们想要构建我们的图表，并将归档保存为工件。您应该从`helm package`命令中获得一个`CHARTNAME-VERSION.tgz`。

## 在实时集群上测试部署

测试阶段到了。我们将编写一个作业框架，其作用是启动一个新的集群，运行`helm install`命令，并为给定的 Kubernetes 版本运行`helm test`命令。

我们不会直接运行头盔命令，而是通过`ct`。首先，主要工作如下所示:

我们传递一些变量，但是大部分工作是在下面的 bash 脚本`e2e-kind.sh`中完成的:

这很冗长，但事情是这样的:

*   我们启动一个`ct`容器，为我们提供实用程序。这样，即使您没有安装这个脚本，也可以在本地运行它。
*   我们使用方便的`ct list-changed`检查图表是否有任何变化，以避免白白产生一个集群。
*   我们用所需的版本启动一个 Kind 集群，如果还没有的话，下载`kind`二进制文件。
*   我们跑`ct install`。该命令将首先在集群中部署图表，如果部署成功，则运行`helm test`。

*注意:如果您的图表需要特殊的测试配置或者您想要测试多个配置，Helm 将加载图表目录中* `*ci*` *文件夹下的任意* `**-values.yaml*` *文件。详见* [*舵文档*](https://github.com/helm/charts/blob/master/test/README.md#providing-custom-test-values) *。*

另一件事是，当运行同类集群时，可能需要额外的配置步骤:

*   如果您正在使用私有容器注册中心，您希望[创建相应的秘密](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)用于您的 pods/服务帐户，或者直接在节点上配置凭证[。](https://kind.sigs.k8s.io/docs/user/private-registries/)
*   如果您的图表使用了一个`LoadBalancer`服务，您肯定希望[设置一个负载均衡控制器](https://kind.sigs.k8s.io/docs/user/loadbalancer/)。

# 处理多个版本的 Kubernetes

到目前为止，我们已经针对给定的 API 版本将图表部署到 Kubernetes 集群中。因为我们的集群是动态配置的，所以很容易对几个 Kube 版本进行测试。

为此，我们根据需要同时生成多个集群，并在每个集群上运行我们的脚本:

# 发布图表

如果我们的图表成功地通过了所有测试，剩下唯一要做的事情就是将它们发布到存储库中，但是有一个问题:

*   如果我们不在主分支上，我们不准备发布我们的图表，所以我们将在一个*孵化器* Helm 存储库中发布它。这使得图表很容易用于手工测试。
*   如果我们在主分支上，我们将它发布到生产环境使用的*稳定的*存储库中。

# 外卖食品

对于你的核心应用软件，依靠手工测试和打包似乎不太合理。然而，这是大多数人在处理 Kubernetes 应用程序打包时所做的事情！

借助 Kind 等出色的工具，您可以通过几行 shell 脚本毫不费力地为您的舵图创建持续集成/部署策略。我希望你能跳下去！
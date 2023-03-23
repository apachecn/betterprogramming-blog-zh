# 建立 Kubernetes 促进地方发展

> 原文：<https://betterprogramming.pub/setting-up-kubernetes-for-local-development-3a9557fbc4af>

## 将您的生产环境与本地环境相匹配

![](img/c88cba37912289a904f942190fd5e064.png)

伊利亚·巴甫洛夫在 [Unsplash](https://unsplash.com/s/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

公司已经在生产中迁移到 Kubernetes，但是仍然有许多开发者将应用程序作为开发机器运行，或者使用像 [Docker Compose](https://docs.docker.com/compose/) 这样的工具来运行和测试他们的应用程序。

由于大多数开发人员不需要完全了解 Kubernetes，我们需要一种方法让他们在本地机器上通过运行一个命令来测试他们的应用程序。

像 [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) 和 [Draft](https://draft.sh/) 这样的工具将帮助我们实现这一目标。在这篇文章中，我们将看到如何。

# **基本设置**

要为开发人员设置开发环境，机器需要一个可工作的 Kubernetes 节点，并安装其他工具。开发人员设置要求:

*   迷你库贝
*   库贝特尔
*   [舵](https://helm.sh/)
*   草稿，[斯卡福德](https://skaffold.dev/)或[花园. io](https://garden.io/)
*   Docker(可选)

为了自动化本地部署，我们可以使用许多可用的工具，如 Draft、Skaffold 或 Garden.io。

要找出这三者之间的区别，请阅读托马斯·普利卡斯的博客，他在博客中对这三者进行了比较。

您可以使用这个 sh 文件来安装所有的工具，使您的本地开发环境对草稿友好。这个脚本会自动下载上面提到的所有工具。

# 起草

[Draft](https://draft.sh/) 是微软开发的一个项目，帮助开发者构建一个可以部署到 Kubernetes 集群的应用。

Draft 的目标是开发人员工作流程的“内部循环”,这意味着无需提交源代码控制，开发人员就可以将该特性部署到集群中。

## 面临的问题

要将应用程序部署到集群，首先，Draft 必须构建 Docker 映像，然后将它推送到 Kubernetes 集群可以访问的存储库。

默认情况下，Draft 使用运行在 dev 机器上的 docker-daemon，所以集群不能访问 docker 映像。草案还提供了将构建的 Docker 映像推送到 Docker 存储库服务器的选项，但这在我们试图部署到生产环境时非常有用。

有些人还建议在你的本地 Kubernetes 集群上制作一个 Docker 注册服务器，并把镜像推送到那里，这也是一个不错的方法，但我认为这有点矫枉过正。

有一个简单的解决方案，那就是使用 Kubernetes docker-daemon 来构建 docker 映像。这可以通过运行以下命令来完成:

```
$ eval $(minikube docker-env)
```

该命令将 Minikube docker-daemon 暴露给终端。因此，如果您在终端中构建任何映像，它将由 Minikube docker-daemon 构建。

现在，当 Kubernetes 试图提取图像时，它会在本地找到它，并使用该图像。

# 设置步骤

*   安装上面提到的工具。Mac 用户可以使用[这个脚本。](https://github.com/ashish932/localKubernetesSetup)
*   启动集群并初始化工具。

```
minikube starthelm initdraft init
```

*   第一次使用`draft create`，会生成 Docker 文件和舵图。根据自己的需求定制。(仅一次)

```
draft create
```

*   使用`draft up`将应用部署到 Kubernetes 集群。

```
draft up
```

您的应用程序将被部署到入口中提供的主机名。将该路径添加到您的 DNS 文件，并将其定向到 Minikube IP。

*   查找 Minikube 正在哪个 IP 上运行。

```
$ minikube ip
```

*   对于 Mac 和 Linux 用户，请更新/etc/host

# 资源

[](https://codefresh.io/howtos/local-k8s-draft-skaffold-garden/) [## Kubernetes 上的本地发展终极指南:Draft vs ska ffold vs garden . io-code fresh

### 在本系列的前几篇文章中，我们已经看到了专门为…设计的本地 Kubernetes 装置

codefresh.io](https://codefresh.io/howtos/local-k8s-draft-skaffold-garden/) [](https://github.com/ASHISH932/localKubernetesSetup) [## ashish 932/localkubernestsetup

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/ASHISH932/localKubernetesSetup)
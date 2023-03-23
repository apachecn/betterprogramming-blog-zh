# 用 CircleCI 和 Alpine(为您的 Apple M1)设置多拱门码头构件

> 原文：<https://betterprogramming.pub/setting-up-a-multi-arch-docker-build-with-circleci-and-alpine-for-your-apple-m1-ba739ef1f754>

## 为多个基本映像版本和平台构建 Docker 映像的简洁设置

![](img/b4c197fae38bd54f980274840441f50d.png)

[马丁·卡特勒](https://unsplash.com/@martinkatler?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](/s/photos/m1?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

随着苹果 M1 或苹果硅的发布，对 arm64 图像的需求只会上升。此外，在 Raspberrys 或其他 arm64 微型计算机上运行 Docker 容器和整个 Kubernetes 集群并不是什么新鲜事。

例如，你可以在 Raspberry Pi 上运行你自己的*《我的世界》*服务器:

[](https://codeburst.io/run-a-minecraft-server-for-free-on-kubernetes-ac82a892969e) [## 在 Kubernetes 上免费运行《我的世界》服务器

### 使用 Kubernetes 在自托管的 Raspberry Pi 服务器上简化多人《我的世界》的部署

codeburst.io](https://codeburst.io/run-a-minecraft-server-for-free-on-kubernetes-ac82a892969e) 

# 用 CircleCI 建立一个平行的多拱建筑

这是为多种架构或平台(最重要的是 AMD 64 和 ARM 64)构建具有多个版本的 Docker 映像的快速指南。

当我的开源 Travis-CI 账户在他们转换模型后用完了积分，而我又想尝试不同的 CI 工具时，我遇到了 CircleCI。经过最初的学习曲线，它是相当干净和简单的设置。️

# 1.为多个版本设置图像

如果您想为基础映像的多个版本构建您的映像，比如说`alpine`、`1.9-alpine`和`1.7-alpine`(或者 Ubuntu 世界中的`focal`、`bionic`和`latest`)，您可以简单地向 Dockerfile 添加一个`ARG`:

```
ARG ALPINE_VERSION=alpine
FROM influxdb:$ALPINE_VERSION
```

现在，您只需在构建时传递构建参数:

```
ALPINE_VERSION=1.9-alpine
docker build --build-arg ALPINE_VERSION=$ALPINE_VERSION -t $REPO/$IMAGE_NAME:$TAG .
```

现在您可以在`.circleci/config.yml`中设置并行构建作业:

# 2.检查架构的源代码

检查您用于多个体系结构的基础映像。您只能构建基本图像标签支持的内容。

本例中的官方`influxdb`映像支持`linux/amd64`和`linux/arm64/v8`，因此您必须为这些平台构建。

```
PLATFORMS=linux/amd64,linux/arm64/v8
```

 [## influxdb 标签

### InfluxDB 是一个开源的时间序列数据库，用于记录指标、事件和分析。

hub.docker.com](https://hub.docker.com/_/influxdb?tab=tags) 

# 3.构建管道

对于 Docker 映像，我使用了`docker:dind`，尽管我不确定是否需要，因为构建引擎是远程的。此外，您可以选择特定版本的远程 Docker 引擎:

```
 docker:
    - image: docker:dind
  steps:
    - setup_remote_docker:
        version: 20.10.2
```

## 安装 docker buildx

受球体`sensu/docker-buildx`的启发，我安装了`buildx`作为插件，并通过`DOCKER_CLI_EXPERIMENTAL`变量启用了实验特性。

 [## CircleCI 开发者中心- sensu/docker-buildx

### Docker buildx 的常见 CircleCI 任务。

circleci.com](https://circleci.com/developer/orbs/orb/sensu/docker-buildx) 

**更新:**为了避免安装所需的工具(在本例中为`buildx`，您可以使用一个包含管道所需内容的映像:

[](https://github.com/DrPsychick/docker-ci-images) [## DrPsychick/docker-ci-images

### 可以在基于 docker 的 CI 管道中使用的图像集合。而不是将依赖项安装在…

github.com](https://github.com/DrPsychick/docker-ci-images) 

## 设置 Docker buildx

要构建其他架构，您必须设置一个上下文和一个构建器:

## 构建和推动

最后，您可以像这样简单地构建和推动:

# 只有最近的图像才是好的

剩下要做的最后一件事是确保定期自动构建映像。当它失败时，我们会得到通知(通过管道通知)并修复它。我们映像的用户可以依赖一个包含所有必需的更新和安全修复的最新版本。

只需添加另一个包含`triggers`和`schedule`配置的工作流:

# 看看它的实际效果

以下是我为多种支持的架构自动构建的 InfluxDB 和 airprint-bridge 映像:

[](https://github.com/DrPsychick/docker-influxdb) [## 心理医生/码头工人-流入 db

### influxdb 基于官方 influxdb:alpine 映像、多种架构(amd64、arm64/v8)、云就绪、完全…

github.com](https://github.com/DrPsychick/docker-influxdb) [](https://github.com/DrPsychick/docker-cups-airprint) [## DrPsychick/docker-cups-air print

### 运行一个带有 CUPS 和 Avahi (mDNS/Bonjour)的容器，以便网络上的本地打印机可以通过 AirPrint 暴露给…

github.com](https://github.com/DrPsychick/docker-cups-airprint) 

感谢您的阅读和享受！
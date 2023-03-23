# 在基于 Linux 的 Docker 容器环境中使用 Swift

> 原文：<https://betterprogramming.pub/using-swift-inside-a-linux-based-docker-container-environment-65ec16b0004d>

## 探索基于 Linux 的概念，将您的快速开发容器化

![](img/3f99106b2e9c634d91d8111ca0eba022.png)

[Ruben Bagües](https://unsplash.com/@rubavi78?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

运行 Swift 需要 Mac 吗？不。Swift 是一种设计用于苹果生态系统的编程语言。但是，它可以在 Linux 上运行。不仅仅是为苹果产品写的。

让我们利用 Docker 创建一个适合 Swift 运行和执行其指令的环境:

```
curl -O https://swift.org/builds/swift-5.2.4-release/amazonlinux2/swift-5.2.4-RELEASE/swift-5.2.4-RELEASE-amazonlinux2.tar.gz
tar -xf swift-5.2.4-RELEASE-amazonlinux2.tar.gz
sudo cp -R ./swift-5.2.4-RELEASE-amazonlinux2 /usr/local/bin/
```

太好了！看起来我们可以运行`swift`命令并继续前进。

没那么快…我们需要`swift`可以从任何地方访问。

目前，由于二进制文件的位置，运行`swift`的唯一方法是直接从路径`/usr/local/bin/swift-5.2.4-RELEASE-amazonlinux2/local/bin/swift`执行。

# 在您的机器上随时随地快速运行

在上面，你可以看到我们正在将 [Swift 编程语言](https://swift.org/)下载为一个预构建的二进制文件，解压其内容，并将其直接添加到系统的用户目录中。

Bash 不能主动使用二进制文件，除非在 bash 会话中的某个地方有到二进制文件的系统链接。为了启用这个链接，`export`我们刚刚解压的二进制文件的路径，并用`:${PATH}`将它链接到当前的 bash 会话。

PATH 是一个与文件位置相关的变量，相当于在 ubuntu 中使用`/usr/sbin:/usr/bin:/sbin:/bin`。在 macOSx 上，这相当于使用`/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin`。

现在让我们用`export`函数创建二进制文件的快捷方式:

```
export PATH="/usr/local/bin/swift-5.2.4-RELEASE-amazonlinux2/usr/bin/:${PATH}"
```

在这里，您可以看到`export`是一个命令，它将环境变量标记为子进程导出。

让我们检查一下这个`swift --version`命令是否有所不同:

```
bash-4.2# swift --version
```

成功！我们在 Linux 内部安装了一个通常在苹果产品上使用的全系统编译语言，并第一次运行了它。

让我们清理剩下的文件:

```
sudo rm -rf ./swift-5.2.4-RELEASE-amazonlinux2
sudo rm -rf ./swift-5.2.4-RELEASE-amazonlinux2.tar.gz
```

# 对接 Swift

包含编译语言是隔离项目、代码和系统进程的下一步。由 [DockerHub](https://hub.docker.com/_/amazonlinux) 提供的一个 Amazon Linux 2 Docker 镜像得到了很好的维护，处理了包的清理和包的轻松安装。

让我们试着让我们的 Swift 程序在基于 Linux 的环境中运行，类似于 AWS ec2 linux2 实例。[亚马逊 Linux 2](https://aws.amazon.com/amazon-linux-2/faqs/) 是利用亚马逊 EC2 和亚马逊 ECS(AWS 提供的服务)的亚马逊机器镜像。

以下是一个 Docker 文件的示例:

```
FROM amazonlinux:2

# Install -- See https://hub.docker.com/_/amazonlinux
RUN yum -y update; yum clean all

# Install requirements.
RUN yum makecache fast \
 && yum -y install deltarpm epel-release initscripts \
 && yum -y update \
 && yum -y install \
      binutils \
      gcc \
      git \
      glibc-static \
      gzip \
      libbsd \
      libcurl \
      libedit \
      libicu \
      libsqlite \
      libstdc++-static \
      libuuid \
      libxml2 \
      tzdata \
      tar \
      sudo \
      nano \
      which \
      python-pip \
 && yum clean all

RUN curl -O https://swift.org/builds/swift-5.2.4-release/amazonlinux2/swift-5.2.4-RELEASE/swift-5.2.4-RELEASE-amazonlinux2.tar.gz
RUN tar -xf swift-5.2.4-RELEASE-amazonlinux2.tar.gz
RUN sudo cp -R ./swift-5.2.4-RELEASE-amazonlinux2 /usr/local/bin/
ENV PATH="/usr/local/bin/swift-5.2.4-RELEASE-amazonlinux2/usr/bin/:${PATH}"
RUN sudo rm -rf ./swift-5.2.4-RELEASE-amazonlinux2
RUN sudo rm -rf ./swift-5.2.4-RELEASE-amazonlinux2.tar.gz

RUN cd ./home && export PATH=/usr/local/bin/swift-5.2.4-RELEASE-amazonlinux2/usr/bin/:"${PATH}" && swift package init --type="executable" && swift package update && swift build && swift run

CMD ["/bin/ping","localhost"]
```

让我们举例说明如何构建这个`.dockerfile`:

```
docker build -t swiftamazon .
```

您可以看到`build`命令将预配置 Swift 并初始化一个 Swift 项目，其类型设置为`executable`:

```
swift package init --type="executable"
```

它将创建一个现成的示例项目。

# 在 Linux 上运行 Swift

让我们运行它！在 Docker 命令后包含`CONTAINER ID`:

```
docker run <CONTAINER ID>
```

或者在 Docker 构建命令中最初用参数`-t`设置的构建映像的标记名:

```
docker run swiftamazon:latest
```

我们现在运行一个基于 Linux 的 Docker 镜像作为一个容器，二进制文件`ping`调用`localhost`来保持这个容器活动。

这将允许我们对容器发出`exec`命令。

让我们将参数`-it`与运行程序`/bin/bash`结合起来，在预装 Swift 的基于 Linux 的环境中工作:

```
docker exec -it <CONTAINER ID> /bin/bash
```

下面是一个运行中的`amazonlinux:2`实例，其中`Swift`已经初始化，可以在容器中运行。

# 决赛成绩

这就是如何让 Swift 在一个隔离的 Linux 容器环境中运行。Swift 从一个预构建的二进制文件运行，可以从容器内的任何地方直接调用。使用 Swift 进行开发现在只需稍加调整就可以被容器化并部署到 AWS。

在本文中，我们探索了多个基于 Linux 的概念，并展示了如何利用非 Mac 自带的开发人员工作流。

编码快乐！
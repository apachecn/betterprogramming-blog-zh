# 构建高质量 Docker 图像的 5 个良好实践

> 原文：<https://betterprogramming.pub/5-must-follow-rules-for-building-high-quality-docker-images-30f1355e4371>

## 创建生产级 docker 文件的最佳实践

![](img/c483d12c2b74d59db4ea0aecdf1280e8.png)

由 [Timelab Pro](https://unsplash.com/@timelabpro?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/containers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Docker 已经成为软件开发领域的一个主要工具。每个人和他们的母亲都在利用微服务的灵活性。但是，即使在它迅速流行起来之后，我仍然看到开发人员无效地使用它；很多人已经掌握了基础知识，但是却没有掌握专业知识。

您可能认为编写 Dockerfile 应该是一个简单的过程，但是实际上，构建一个生产就绪的映像需要做很多工作。

未经优化的 DockerFiles 通常会导致更大的整体形象。这为威胁创造了更大的攻击媒介，并增加了安全漏洞的可能性。所有这些对您的应用程序来说都是坏消息。

今天，我们将重点介绍一些创建 Docker 文件的最佳实践，以构建高质量、高效的图像。

# #1 —使用多阶段构建工具

2017 年 Docker 17.06 CE 发布，随之而来的是[多阶段构建工具](https://www.docker.com/blog/multi-stage-builds/)。这是 Docker 试图在工具中实现一个[构建器模式](https://en.wikipedia.org/wiki/Builder_pattern)功能，这也是 Docker 中利用最少的功能之一。

多阶段构建利用了在一个`Dockerfile`中使用多个`FROM`命令的优势。这些指令中的每一个都将建立在先前的阶段之上，并创建新的阶段。通过像这样的增量构建，我们可以分离出所有我们在最终应用程序中不需要的步骤，并消除它们，从而节省空间。

例如，我们可以创建一个构建阶段，在这个阶段我们安装一些依赖项，下载我们的代码，运行一些测试，等等。但是因为大部分在最终的图像中是不需要的，所以我们可以在它到达我们的最终版本之前删除它。我们在构建阶段编译我们的应用程序，并且只将我们编译的代码复制到最终的精简映像中运行。

让我们来看一个例子:

```
# First, specify a base image that has the dependencies and tools needed to build your application
FROM golang:1.14 as builder

# Copy your application code into the image
COPY . /app

# Use the RUN command to build your application
RUN cd /app && go build -o myapp

# Now, create a new stage using a lightweight base image
# that only has the dependencies needed to run your application
FROM alpine:3.12

# Copy the compiled binary from the previous stage into this stage
COPY --from=builder /app/myapp /usr/local/bin/myapp

# Set the default command to run when a container is started from this image
CMD ["myapp"]
```

在 Docker 中使用这种多阶段构建技术有几个优点:

1.  提高效率和性能:通过在 docker 文件中创建多个阶段，您可以隔离构建过程的不同步骤，并且在每个阶段只包含必要的文件和依赖项。这可以帮助您创建更高效、更轻量、更易于管理和维护的 Docker 映像。
2.  减少映像大小:通过在构建的最后阶段使用轻量级基础映像，您可以减少 Docker 映像的大小，使它们更容易分发和部署。如果您在资源受限的环境中部署容器，比如基于云的容器平台，这可能特别有用。
3.  提高安全性:通过使用多阶段构建，您可以避免在最终 Docker 映像中包含任何敏感或机密信息。这有助于保护您的应用程序和用户，也有助于您遵守安全和隐私法规。

总的来说，多阶段构建技术对于创建更高效、更易于维护的高质量图像是必不可少的。

# # 2——明智地选择你的基本形象

你可以写出世界上最好的 docker 文件，但是如果你使用了错误的基础映像，那就完全是浪费。无论何时使用`From`命令，您都是在从以前的图像和它继承的每个图像中获取所有代码。因此，请确保使用包含应用程序运行所需的所有内容的基础映像来启动 docker 文件，例如特定版本的操作系统。这将使您的 docker 文件更有效，并减少最终图像的大小。

您应该考虑以下几点:

1.  **与应用程序的兼容性**:基本映像应该包括应用程序运行所需的操作系统和依赖项。这可能包括您的应用程序所依赖的特定版本的语言运行库、库或工具。
2.  **尺寸**和**效率**:基本映像应该尽可能小而高效，以减小最终 Docker 映像的尺寸并提高性能。
3.  **安全性**:基础映像应该是安全的，并得到良好的维护，定期进行安全更新和修补。这将有助于保护您的应用程序和用户免受漏洞和其他安全风险的影响。
4.  **声誉**和**可靠性**:基础图像应来自声誉良好的来源，并且应在 Docker 社区中得到广泛使用和良好测试。
5.  **灵活性**和**定制**:基础映像应该足够灵活，允许您对其进行定制，并添加您自己的依赖项和配置。这将使您能够更好地控制最终的 Docker 图像，并允许您根据自己的特定需求调整基础图像。

# #3 —使用 ENV 定义环境变量

另一个最佳实践是使用 ENV 来定义环境变量。这将使您的容器更具可移植性，并且它将帮助您避免在不改变主机系统的操作系统的情况下在容器中改变任何关于 Linux 操作系统的东西。

```
# Start with a base image that has the dependencies and tools needed to run your application
FROM python:3.8

# Use the ENV command to define your environment variables
ENV APP_NAME="My Application"
ENV APP_PORT=5000

# Use the COPY or ADD command to add your application code to the image
COPY . /app

# Use the RUN command to install any additional dependencies and perform other setup tasks
RUN pip install -r /app/requirements.txt

# Use the EXPOSE command to specify the ports that your application listens on
EXPOSE 5000

# Use the CMD command to specify the default command that should be run when a container is started from your image
CMD ["python", "/app/main.py"]
```

例如，您可以在应用程序代码中使用`APP_NAME`变量来显示应用程序的名称，并且可以使用`APP_PORT`变量来指定应用程序监听的端口。

`ENV`命令可以用来定义任意数量的环境变量，如果需要，可以在 docker 文件中多次使用`ENV`命令。当您的应用程序在 Docker 容器中运行时，这可以使它更容易管理和配置。

# #4 —暴露的端口

此外，要注意哪些端口是公开的。默认情况下，Docker 会将所有容器暴露给一系列随机的内部端口。这可能会带来安全风险，因此如果您使用的服务需要向公共互联网公开，请确保在 docker 文件中创建一个条目。

# #5 —提交版本控制

您应该将 docker 文件提交到您的存储库中，以便以后可以轻松地引用它。这对某些人来说可能听起来非常明显，但是使用版本控制将允许您跟踪变更并在您的项目中与其他人协作。

> “全公司范围的共享源代码库是用于整合整个组织的本地发现的最强大的机制之一。”
> ——吉恩·金的《德沃普斯手册》

最后，尽可能保持你的文档简单。不要试图增加不必要的复杂性，并确保它易于其他开发人员理解和执行，无需任何帮助。

感谢阅读。
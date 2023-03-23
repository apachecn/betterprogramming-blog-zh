# 优化您的 Go Dockerfile 文件的大小和速度

> 原文：<https://betterprogramming.pub/modern-rest-api-with-go-and-postgresql-e0070aa1b383>

## 用 Go 构建现代 REST API 第 4 部分

![](img/0090449ffef31b7a9e5fa8c3b7359d68.png)

Guillaume Bolduc 在 [Unsplash](https://unsplash.com/s/photos/container?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

本文是一个系列的第四篇，涵盖了逐步实现现代 REST API 微服务的所有方面:

1.  [用 sqlc 定义 SQL 优先数据模型](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-c765d571b9e7)
2.  [用 Gin 实现 REST API](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-39b0e966534a)
3.  [用 Viper 配置](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-1d48767a813)
4.  **在容器中构建和运行**
5.  [集装箱测试](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-7c916ce2816a)

该系列的所有代码都可以在 https://github.com/bquenin/modern-go-rest-api-tutorial[获得](https://github.com/bquenin/modern-go-rest-api-tutorial)

# 我们的执行环境是什么？

为了更好地理解我们需要构建什么，我们需要知道我们的目标是什么执行环境:

*   **生产环境**:我们希望在尽可能接近生产环境的环境中运行我们的容器化应用程序，
*   **开发环境**:我们也想要一个快速的开发周期，在这个周期中，我们可以运行、停止和重启我们的应用程序，而不需要将其容器化。通常，这在开发新特性、实现测试、调试问题、分析等时很有用。

# 生产环境

在开发 REST API 微服务时，您负责为您的应用程序提供一个容器。一旦构建了容器，您可能需要将它推送到您选择的容器注册中心，以便可以将应用程序部署到您的生产环境中。

在本教程中，我们不会涵盖容器化应用程序的整个部署过程，但我们将涵盖以下主题:

*   构建生产容器映像，
*   使用 docker-compose 在模拟生产环境的堆栈中运行该映像。

## 尺寸很重要

在一个集装箱化的世界里，占地面积小是至关重要的。事实上，您的图像将被无数次地构建、压缩、推送到注册表，并从该注册表中取出。更大的映像不仅需要更长的时间来完成所有这些操作，而且还会导致更高的成本，因为云提供商会收取存储和网络使用费！

这就是为什么像 [Alpine](https://www.alpinelinux.org/) 这样的小型操作系统非常方便。Alpine 是一个非常小的 Linux 发行版，专为运行容器化的应用程序而定制，力求占用空间尽可能小。压缩图像的大小是[2MB 多一点](https://hub.docker.com/_/alpine/tags)。

Go 是构建容器化应用程序的完美选择。事实上，二进制文件是静态链接的，这使得将它们打包到一个容器中变得非常容易。您甚至可以构建一个只包含 go 二进制文件的映像(从头开始)，它仍然可以工作！

这里，我们使用 Alpine 作为基础映像，因为当您调试一个正在运行的容器并附加到它时，拥有一个 shell 和其他命令是非常有用的。

## 构建容器映像

有多种方法可以构建符合 OCI 标准的容器映像，但是在本教程中，我们将使用 Docker。为了保持我们的容器映像较小，我们将使用多阶段构建过程:

多级码头建造

1.  构建 go 二进制文件:我们使用官方的 Go docker 映像。为了启用缓存，我们首先下载 go 模块。这将创建一个可以跨构建重用的单独层，从而大大减少构建时间。然后，我们复制源代码并构建 Go 二进制文件。
2.  构建生产映像:我们使用官方的 Alpine 映像和**复制**我们刚刚构建的 go 二进制文件`--from`前一层。

生成的映像很小，因为它只包含 Alpine OS 和 go 二进制文件。没有任何构建工件(源代码、导入的包等。)都在最终图像中。

请注意，我们将图像锁定到特定的版本。您希望尽可能精确，因为 Docker 图像标签是别名，可能会随着时间的推移指向不同的图像。确定特定的版本可以确保您拥有可重现的构建，这意味着如果某个给定的构建出现任何问题，您都可以自信地重现它。

## 定义我们的服务堆栈

现在我们有了自己的容器，我们希望在尽可能接近生产环境的环境中运行它。为了实现这一点，我们将使用 docker-compose。 [Compose](https://docs.docker.com/compose/) 是一个定义和运行多容器 Docker 应用程序的工具。使用 Compose，您可以使用 YAML 文件来配置应用程序的服务。然后，使用一个命令从配置中创建和启动所有服务。

我们的堆栈只需要 2 个服务:Postgres 数据库和我们的微服务。我们可以使用 Compose 描述这个堆栈，如下所示:

stack.yml

# 微服务

是我们的应用微服务。

它包括一个**构建**部分，告诉 Compose 在我们启动堆栈时构建容器。如果您修改了源代码，将会构建一个新的映像来反映这些更改。

正如在上一篇文章中提到的，我们使用**环境**变量来配置我们的应用程序，以指定 Postgres 数据库的主机名和密码。请注意，服务 DNS 名称来自堆栈中定义的服务名称。在这种情况下，Postgres 可以通过 **postgres** DNS 名称访问。

默认情况下，容器不会将其端口导出到本地主机。因此，我们需要指定一个端口映射来访问端口 8080 上的服务。你可以在这里了解更多关于 [Compose networking](https://docs.docker.com/compose/networking/) 的信息。

## Postgres

这是我们的 Postgres 数据库服务。我们使用 Docker hub 的官方图片。

至于另一个服务，我们使用环境变量来指定密码。在生产部署中，配置管理器或其他方式(如 Kubernetes secret)会提供这个环境变量。

为了初始化我们的数据库模式，我们依赖于容器化的数据库约定，并将我们的 schema.sql 挂载到**/docker-entry point-initdb . d**文件夹。你可以在这里阅读更多关于这个[机制的内容。](https://hub.docker.com/_/postgres)

最后，我们使用一个命名卷来存储 Postgres 数据。在本教程中，我们确保在重新启动之间擦除卷，但是您可以在重新启动之间保留卷以重用现有数据。

## 关于安全性的说明

在本教程中，我们使用 Docker 合成文件中的环境变量来提供数据库密码。为了方便起见，在源代码库中检查默认的数据库密码。然而，记住**永远不要检查源代码库中的任何敏感信息**。

在我们的例子中，部署管道可以通过在 Compose 文件中执行[变量替换来设置数据库密码环境变量。一个更安全的方法是依靠一个秘密管理工具，例如](https://docs.docker.com/compose/environment-variables/#substitute-environment-variables-in-compose-files) [Hashicorp Vault](https://www.vaultproject.io/) 结合 [Kubernetes secrets](https://kubernetes.io/docs/concepts/configuration/secret/) 或 [Docker secrets](https://docs.docker.com/engine/swarm/secrets/) 。

# 开发环境

现在让我们来看看开发环境。唯一的根本区别是，我们不希望我们的微服务在堆栈中运行。我们希望使用我们最喜欢的 IDE 或其他方式来启动我们的服务。

我们仍然需要让 Postgres 数据库运行并可到达。事实上，production Postgres 服务并不导出任何端口，因此从堆栈外部连接到它是不可能的。我们需要在开发模式下公开这个端口。

希望我们可以通过创建另一个堆栈文件来表达这些差异:

堆栈.开发. yml

我们可以通过将这个文件与产品堆栈相结合来开始我们的开发堆栈。微服务的副本数量设置为 0，因此堆栈中没有运行任何实例，Postgres 服务将其端口暴露给主机。

我们只需要启动我们的微服务实例，并使用环境变量对其进行配置，以从堆栈(可在 localhost:5432 上访问)连接到 Postgres 实例。

# 把它们包装在一起

现在我们有了堆栈，我们需要使用方便的命令来启动和停止它们。为此，我们将使用一个很好的旧 Makefile:

您现在可以使用以下命令:

*   `make prod`:在 localhost:8080 上启动可以连接到您的微服务的生产环境，
*   `make dev`:启动 dev 环境，在这里可以连接到 localhost 上的数据库:5432。
*   `make stop`:优雅地停止任何堆叠。

# 下一步是什么？

既然我们已经介绍了生产和开发环境，我们将看看如何编写和容器化我们的集成测试[！](https://medium.com/better-programming/modern-rest-api-with-go-and-postgresql-7c916ce2816a)
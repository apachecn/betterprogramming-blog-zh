# 使用 Viper 为云配置 Go 微服务

> 原文：<https://betterprogramming.pub/modern-rest-api-with-go-and-postgresql-1d48767a813>

## 用 Go 构建现代 REST API 第 3 部分

![](img/1d4283a2fc0198f2d2c0a77574a82792.png)

由 [Austin Lowman](https://unsplash.com/@shutter_hunter) 在 [Unsplash](https://unsplash.com/) 拍摄的照片

本文是一系列文章中的第三篇，涵盖了逐步实现现代 REST API 微服务的所有方面:

1.  [使用 sqlc 定义 SQL first 数据模型](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-c765d571b9e7)
2.  [用 Gin 实现 REST API](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-39b0e966534a)
3.  **用 Viper 配置**
4.  [在容器中构建和运行](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-e0070aa1b383)
5.  [集装箱测试](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-7c916ce2816a)

该系列的所有代码可从以下网址获得:

[](https://github.com/bquenin/modern-go-rest-api-tutorial) [## GitHub-bque nin/go-modern-rest-API-tutorial:这是一个 REST 微服务样本。它的特点是:一…

### 这是一个 REST 微服务示例。它的特点是:一个使用 https://gin-gonic.com/数据库的 REST API 服务器…

github.com](https://github.com/bquenin/modern-go-rest-api-tutorial) 

# 使用哪种配置机制？

在配置应用程序时，有很多选择。您可以使用命令行参数、配置文件、环境变量、远程配置服务(如`etcd`、consul 等。)，或者全部的组合。

每种机制都有意义，这取决于您的用例。由于我们正在构建一个最终将被容器化的微服务，所以依赖命令行参数对我们来说没有太大意义。事实上，为了更好地与一个 orchestrator(比如 Kubernetes 或其他)配合，我们的容器应该立即启动，最好不需要任何额外的配置。

任何需要提供或覆盖的值都应该使用环境变量来完成。这为运营团队提供了很大的灵活性，并且可以很好地使用所有基于容器的部署工具。

但是，如果您的应用程序有很多参数，您不希望让操作者为启动应用程序而提供大量的值。因此，我们将混合使用配置文件和环境变量:

*   配置文件包含所有的默认值，并且嵌入在 go 二进制文件中:您既不想公开您的配置文件，也不想记录它。这是一个实现细节；您可以稍后决定直接从代码中获取默认值，或者从远程配置服务中获取它们。
*   使用环境变量可以覆盖任何值:这是您的公共配置机制，必须记录下来。

这看起来工作量很大，但幸运的是，有很多 Go 配置库。最著名的库之一是 [Viper](https://github.com/spf13/viper) ，它很好地处理了我们的用例。

# 实施配置

到目前为止，我们一直遵循 Go 应用程序的标准布局，我们的项目结构如下所示:

```
└── cmd
    └── microservice
        └── main.go
```

有多种方法可以放置配置代码，但是我们将把它放在主包的旁边。毕竟，配置将包含整个应用程序，并可能跨越所有包。根据这一基本原理，我们的项目结构将如下所示:

```
└── cmd
    └── microservice
        ├── config
        │   ├── config.go
        │   └── config.yml
        └── main.go
```

## 定义我们的配置模型

不，我们不会在这里回顾代码优先、配置优先的方法！您应该遵循的唯一规则是将配置分解成对用户有意义的块。对于我们的示例应用程序，我们只有数据库的配置数据，所以我们的配置很简单:

config.go

现在我们已经定义了配置模型，我们可以使用 Viper 来读取配置文件并启用环境变量覆盖机制。

## 配置文件

Viper 支持[很多配置文件格式](https://github.com/spf13/viper#reading-config-files)。在本教程中，我们将使用 YAML:

默认配置

如前所述，配置文件包含应用程序的所有默认值。

## 关于安全性的说明

请注意，默认配置文件不得包含敏感数据，如密码、机密、API 密钥等。相反，提供非工作默认值，这些值必须在运行时被覆盖，例如上面的“`changeme`”值。

在本教程中，我们使用环境变量覆盖配置。虽然这对于运行本地堆栈来说很好，但具有可靠安全性的实际生产部署很可能依赖于一个秘密管理工具，例如与 [Kubernetes secrets](https://kubernetes.io/docs/concepts/configuration/secret/) 结合的 [Hashicorp Vault](https://www.vaultproject.io/) 。

## 嵌入配置文件

为了嵌入我们的配置文件，我们使用了`[//go:embed](https://pkg.go.dev/embed)` [指令](https://pkg.go.dev/embed)。顾名思义，它允许将任何文件嵌入到 go 二进制文件中，并在运行时将其用作变量:

## 读取配置

我们现在可以使用 Viper 读取默认配置:

1.  我们启用环境变量覆盖机制，
2.  我们使用`ReadConfig`读取配置:它将首先从嵌入式配置中读取默认值，然后用来自环境变量的值覆盖它(如果有的话)。
3.  将结果解组到我们的配置模型中

使用 Viper 读取配置

## 使用环境变量覆盖值

使用环境变量覆盖配置的工作原理是定义一个变量，该变量的名称是配置模型中参数的“路径”:

参数目标覆盖 **→前缀参数目标覆盖**

例如，如果我们想要覆盖`postgres.password`值，我们定义`APP_POSTGRES_PASSWORD`环境变量，因为我们已经为我们的应用程序配置了前缀`APP`。

# 包装它

我们现在可以相应地更新我们的主函数:

# 下一步是什么？

现在我们已经实现了配置，我们将看看如何在容器中运行 API 服务器[！](https://medium.com/better-programming/modern-rest-api-with-go-and-postgresql-e0070aa1b383)
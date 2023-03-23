# 用 Rust、Actix 和 MongoDB 构建一个 API 来统计 GitHub 概要视图

> 原文：<https://betterprogramming.pub/build-an-api-to-count-github-profile-views-with-rust-actix-and-mongodb-5a6532b6d2eb>

## 调用 API 端点并创建您自己的徽章来显示视图统计。

![](img/e6711d455a76d76eb161ec87bb40ec85.png)

在这篇文章中，我们将学习如何使用 Rust、Actix 和 MongoDB 构建一个简单的 API 来计算 GitHub 上的概要视图。您可以将它视为如何从头开始一个典型的 HTTP API 项目的教程。

收集视图计数的想法很简单。我们需要嵌入一个跟踪图像到 github 页面，用户可以查看和显示收集的统计数据。为了显示收集的统计数据，我们将使用 [shields](https://shields.io/endpoint) 项目，该项目允许使用自定义数据提供者创建徽章。

# 先决条件

首先你需要设置合适的工具来开始开发。以下是最简单的列表:

*   [Rust 语言](https://www.rust-lang.org/tools/install) —构建和运行应用程序的 Rust SDK
*   [VSCode](https://code.visualstudio.com/) —用于修改应用源代码的编辑器
*   [Rust Analyzer](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer) —一个 VSCode 扩展，为 Rust 开发提供了更好的体验
*   [Docker 桌面](https://www.docker.com/get-started/) —构建或使用 Docker 图像

Docker 对于在本地开发应用程序也很有用，以防您需要一个数据库实例，而不需要在操作系统中安装它。

# 设置项目

让我们用这个 cargo 命令创建一个 Rust 项目:

```
cargo new counter
```

现在您可以在`VSCode`中打开项目文件夹，并添加开发 HTTP API 所需的依赖项。在这个项目中，依赖关系将是:

*   Actix——一个流行的 Rust web 框架
*   MongoDB 的 Rust 驱动程序
*   Serde —一个用于 JSON 序列化/反序列化的 Rust 库

你可以编辑`Cargo.toml`文件或者在项目目录下使用这个命令:

```
cargo add actix-web mongodb serde
```

您还需要修改`serde`特性标志，以允许使用`derive`宏来实现更具声明性的序列化。

因此，依赖项部分将如下所示:

```
[dependencies]
actix-web = "4.1.0"
mongodb = "2.3.0"
serde = { version = "1.0.140", features = ["derive"] }
```

# 数据库ˌ资料库

在我们开始编码之前，我们需要一个`MongoDB`实例来存储应用程序数据。我们将使用 DockerHub 注册表中的官方 Docker 图像来制作。

让我们为本地开发创建一个 Docker 容器:

```
docker run -d --name local-mongo \
    -p 27017:27017 \
    -e MONGO_INITDB_ROOT_USERNAME=admin \
    -e MONGO_INITDB_ROOT_PASSWORD=pass \
    mongo:latest
```

一旦完成，您就可以用这个连接字符串`mongodb://admin:pass@localhost:27017`连接到您的本地 MongoDB。

为了允许应用程序访问数据库，您必须将连接字符串添加到项目的`.cargo`目录中的`config.toml`文件中:

```
[env]
DATABASE_URL = "mongodb://admin:pass@localhost:27017"
```

您可能还需要这个命令来(重新)启动 MongoDB 容器:

```
docker restart local-mongo
```

# 项目结构

在这个项目中，我们将有两层来组织代码。第一层将代表一个数据服务，我们将使用它与 MongoDB 集合一起添加和统计 GitHub 概要视图。第二层将公开 API 端点，用于跟踪视图和检索收集的统计数据。

让我们使用上面描述的两个功能来创建数据服务:

我们 API 的端点层:

让我们一起总结一下:

要运行 web 服务器，只需构建并启动应用程序:

```
cargo run
```

# 结论

因此，现在您可以调用 API 端点并创建自己的徽章来显示视图统计信息。

材料:

*   示例如何收集和显示一个 [GitHub 概要文件](https://github.com/shadeglare)的视图。
*   完整的源代码可以在[这里](https://github.com/hexarc-software/hexarc-counter)找到。

希望这篇教程对你有所帮助。
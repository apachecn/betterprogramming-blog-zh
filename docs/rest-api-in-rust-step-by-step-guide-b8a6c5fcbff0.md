# 如何在 Rust 中构建 REST API 分步指南

> 原文：<https://betterprogramming.pub/rest-api-in-rust-step-by-step-guide-b8a6c5fcbff0>

## 在堆栈溢出的基础上，用过去四年中最受欢迎的语言构建你的 API

![](img/2b901df7b9365dc09a0734fa0123937c.png)

[Mozilla Rust](https://www.rust-lang.org/)

你好，我是阿赛尔。今天，我将向您展示如何在 [Rust](https://www.rust-lang.org/) 中创建一个简单的 REST API。

为此，我们将为 API 使用 [Rocket 框架](https://rocket.rs/)，为持久化特性使用 [Diesel ORM 框架](http://diesel.rs/)。这个框架将涵盖下面提到的所有内容。所以，这比从零开始实现要容易得多。

*   启动 web 服务器并打开一个端口。
*   在此端口上侦听请求。
*   如果有请求进来，查看 HTTP 头中的路径。
*   根据路径将请求路由到`handler`。
*   帮助您从请求中提取信息。
*   将生成的`data`(由你创建)打包，形成一个`response`。
*   将`response`发送回发送方。

# 每夜安装铁锈

因为 Rocket 充分利用了 Rust 的语法扩展和其他高级的、不稳定的特性，我们必须安装`nightly`。

```
rustup default nightly
```

如果您希望将`nightly`只安装在您的项目目录中，您可以使用以下命令:

```
rustup override set nightly
```

# 属国

一旦我们使用这些板条箱，我会解释的。

# 设置柴油

所以，接下来我要做的是设置[柴油](http://diesel.rs/guides/getting-started/)。Diesel 提供了自己的 CLI，所以我们必须先安装它。(假设你用的是 [PostgreSQL](http://postgresql.org/download/) 。)

```
cargo install diesel_cli — no-default-features — features postgre
```

然后，您需要告诉 Diesel 您的 DB 凭证是什么。该命令将生成一个`.env`文件。

```
echo DATABASE_URL=postgres://username:password@localhost:port/diesel_demo > .env
```

之后，运行以下命令:

```
diesel setup
```

这将创建我们的数据库(如果它还不存在的话)，并创建一个空的迁移目录，我们可以用它来管理我们的模式(稍后将详细介绍)。

运行上述代码时可能会出现几个错误。

```
= note: LINK : fatal error LNK1181: cannot open input file ‘libpq.lib’
```

通过将`PG lib folder`路径添加到环境变量中，可以很容易地解决这个问题。

```
setx PQ_LIB_DIR “[path to pg lib folder]”
```

我很惊讶那些错误没有在[柴油机文件](http://diesel.rs/guides/getting-started/)中被提及。

我强烈建议在 CMD 或 Powershell 中运行这些命令。如果你正在使用 IDE 的终端，你不会得到任何这样的错误，你会浪费两个小时试图弄清楚到底发生了什么。

![](img/d609783182454c7f11dd625f656e8e74.png)

要解决这个问题，您可以将 PG 的 bin 文件夹路径添加到 path 变量中。问题解决了？很好！

让我们创建一个用户表，并为其创建一个迁移:

```
diesel migration generate users
```

运行该命令后，您将看到在迁移文件夹中生成了两个文件。

接下来，我们将编写用于迁移的 SQL:

`up.sql`

`down.sql`

要应用我们的迁移，您可以使用:

```
diesel migration run
```

最好确保`down.sql`是正确的。通过*重做*迁移，您可以快速确认您的`down.sql`正确回滚了您的迁移:

```
diesel migration redo
```

您可以看到数据库中有一个用户表。对！

我忘了说，如果你已经注意到，有一个文件是在你运行 Diesel 设置后生成的，名为`schema.rs`。应该是这样的。

# 酷，生锈的部分来了

既然我们要使用 ORM，显然我们必须将用户表映射到 Rust 中的某个东西。在 Java 中，我们使用类来映射表。在Java **，**我们通常称之为*bean*。在 Rust 中，我们使用`struct` s。让我们创建一个`struct`。

现在，你可能想知道这些注释是什么，就像上面的结构定义。

它们被称为衍生产品。因此，该行将派生出*序列化的*和*可查询的*特征。`#[derive(Serialize)]`和`#[derive(Deserialize)]`用于将数据映射到响应和请求。

现在我要再创建两个结构体。你以后会得到它们的。

接下来我们要做的是实现`User`。所以它会有一些方法来做数据库操作。

在这里，正如您所看到的，我们已经将连接传递给了方法，并返回了一个向量`User` s。我们正在获取用户表中的所有行，并将它们映射到`User`结构。

当然，我们预计会有错误。如果出现紧急情况，将会打印出“错误”消息。

现在我们已经创建了一个表和映射该表的结构。接下来我们要做的是创建使用它的方法。因此，我们将创建一个`routes`文件。我们通常称之为*处理程序*。

现在，我们要做的就是建立连接池。这里有一个来自[火箭文档](https://rocket.rs/v0.4/guide/state/#databases)的关于连接池的简单解释。

> “Rocket 包含内置的、与 ORM 无关的数据库支持。特别是，Rocket 提供了一个过程宏，允许您通过连接池轻松地将 Rocket 应用程序连接到数据库。
> 
> 数据库连接池是一种数据结构，它维护活动的数据库连接，供以后在应用程序中使用。"

最后，我们需要在`main`文件中启动我们的服务器。

在我的项目中，我也添加了棱角分明的前端。我也会用我们生锈的后端来为它服务。

运行应用程序→ `cargo run`。

![](img/78b4935ce34fa57024d9ca83c830c57c.png)

启动服务器

让我们用[失眠](https://insomnia.rest/)来测试我们的服务器。

![](img/e4be1be509493767e4a83b5e63607ce5.png)

我希望这有所帮助。干杯！
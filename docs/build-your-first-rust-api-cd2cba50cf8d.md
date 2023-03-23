# 构建您的第一个 Rust API

> 原文：<https://betterprogramming.pub/build-your-first-rust-api-cd2cba50cf8d>

## 通过使用 Rocket.rs 和 Diesel.rs 构建一个真实的 API，亲自动手学习 Rust

![](img/a18b7f791f00c885b26114efbe3be703.png)

由[paweczerwiński](https://unsplash.com/@pawel_czerwinski?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在本教程中，我将描述如何使用流行的 Rust web 框架 [Rocket](https://rocket.rs/) 和 [Diesel](https://diesel.rs/) 作为 Postgres 的 ORM 来构建一个简单的 API。我最初构建这个小项目是为了学习 Rust 和上述技术，所以我认为尝试构建一些我可以以某种方式使用的东西是个不错的主意。这就是为什么我决定构建这个小 API 来帮助我管理我的作品集中的中型文章的链接。

# 我们的 API 的结构

首先，让我们看看我们的 API 将如何构造。它将有三个主要的路由处理器:

*   `/user` **:** 创建用户，然后验证用户的访问令牌
*   `/auth` **:** 用于获取用户访问令牌的组
*   `/posts` ***:*** 创建和检索帖子

在本教程结束时，我们的 API 中将有以下端点(这是我们运行服务器时得到的输出):

```
Mounting */user:*=> ***POST*** */user* *application/json* *(create)*=> ***GET*** */user/info* *(info)*=> ***GET*** */user/info* *[2]* *(info_error)*Mounting */auth:*=> ***POST*** */auth/login* *(login)*Mounting */posts:*=> ***POST*** */posts* *(create)*=> ***GET*** */posts* *(read)*=> ***POST*** */posts* *[2]* *(create_error)*Rocket *has* *launched* *from* [*http://0.0.0.0:8000*](http://0.0.0.0:8000)
```

# 设置好一切

*在本教程中，假设你已经下载了 Rust 并运行了一个* `Postgres` *实例。*

首先，我们将使用以下命令创建一个新的 Rust 项目(从现在开始，我们将使用`blog-backend`作为我们项目的名称):

```
**cargo** new blog-backend
```

由于 Rocket 利用了 Rust 的高级特性，我们将需要使用 Rust 的夜间版本。为了在我们的项目中只使用夜间版本，我们导航到我们项目的目录(`blog-backend`)并运行以下命令:

```
**rustup** override set nightly
```

*注意* : Rocket 每晚都需要 Rust 的最新版本，所以如果在构建过程中出现任何问题，只需更新工具链和依赖项即可:

```
**rustup** update && **cargo** update
```

现在我们将安装`diesel_cli`包，它将帮助我们管理数据库模式。同样，由于我们正在使用`Postgres`，我们可以避免下载所有其他默认特性:

```
**cargo** install diesel_cli --no-default-features --features "postgres"
```

然后，我们通过设置以下环境变量向`Diesel`指示连接到我们数据库的 URL。在本例中，`blog`是我们正在使用的模式的名称:

```
**export** DATABASE_URL=postgres//user:pass@localhost/blog
```

现在，Diesel CLI 将使用以下命令处理数据库和迁移目录创建:

```
**diesel** setup
```

现在，如果我们回忆一下我们的 API 定义，我们显然是在使用两种不同的数据类型:用户和帖子，所以我们必须为这两种实体创建表。

首先，我们创建一个表来存储我们的用户。我们通过定义迁移策略来做到这一点:

```
**diesel** migration generate users
```

该命令生成两个文件，`up.sql`和`down.sql`，放在目录`migrations/date_of_creation_users/` *中。*第一个文件定义如何应用迁移，第二个文件定义如何恢复迁移。

用于用户迁移的 up.sql

用于用户迁移的 down.sql

我们应用迁移:

```
**diesel** migration run
```

我们将用下面的定义文件对 posts 表重复这个过程:

posts 迁移的 up.sql

posts 迁移的 down.sql

最后，我们在`*Cargo.toml*`文件中添加我们需要的所有依赖项:

# 进入代码

您可能已经注意到，在设置过程中的某个时候已经生成了一个名为`schema.rs` 的文件。该文件由 Diesel 自动生成，并在我们运行迁移时进行更新。它包含代表数据库模式中所有表和列的`table!`宏。

schema.rs

为了建立数据库连接，我们将使用以下文件创建一个数据库连接池，以便在将来的请求中重用数据库连接:

数据库连接池

现在我们添加一个主函数，启动 rocket 服务器，初始化数据库连接池，并挂载 users 和 posts 模块:

main.rs

# 用户

现在我们进入用户模块。在本模块中，我们不仅包括与用户相关的所有内容，还包括与身份认证相关的内容。

首先，我们看一下我们的`model.rs` 文件*、*，其中我们定义了用户结构并实现了与数据库交互的方法:

用户模块的模型

请注意，我们有两种不同的用户结构。这两种结构的命名解释了其中的原因:由于用户 ID 是由数据库创建的，所以我们用于插入的用户结构将与常规的用户结构相同，但是删除了 ID 字段。

我们使用`bcrypt` 机箱来加密用户的密码。当插入一个新用户时，我们通过散列先前的值来更新用户结构的密码字段。另一方面，当试图通过用户名和密码检索用户时，我们必须使用前面提到的验证函数来验证两个密码是否匹配。

## 证明

我们使用基于 JWT 的认证。我在[下面的库](https://github.com/marcocastignoli/rust_rocket_api_authentication)中发现了一个 Rocket 实现，所以我们可以看到这就是我们正在使用的:

证明

在这个文件中，我们实际上是在检查每个需要对用户进行身份验证的请求。如果请求包含一个带有有效令牌的报头，则请求被接受，但是如果令牌丢失或无效，则请求被转发。

最后，我们为用户和认证路由定义处理函数。唯一不直接的处理程序是登录处理程序，在这里我们检查凭证是否正确，然后为用户生成一个有效的令牌。

# 邮件

文章模型将类似于用户模型。因为文章也有一个数据库生成的 ID，所以我们也采用两个结构的方法:

岗位模型

最后，我们还将为 POST 方法定义两条路由—一条用于处理包含有效令牌的请求，另一条用于发送未经授权的错误:

帖子修改

现在我们已经拥有了所需的一切，我们只需运行以下命令，一切都将启动并运行:

```
**cargo** run
```

您可以在[库](https://github.com/UxioAndrade/blog-backend)中找到本教程中描述的所有文件。
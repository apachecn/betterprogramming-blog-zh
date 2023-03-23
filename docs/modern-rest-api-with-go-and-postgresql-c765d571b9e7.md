# 使用 PostgreSQL 和 sqlc 在 Go 中使用 REST API

> 原文：<https://betterprogramming.pub/modern-rest-api-with-go-and-postgresql-c765d571b9e7>

## 用 Go 第 1 部分构建现代 REST API

![](img/bf48c9bc557477057a30bf2d02fde831.png)

照片由 [Unsplash](https://unsplash.com/s/photos/sql?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Sunder Muthukumaran](https://unsplash.com/@sunder_2k25?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

本文是一系列文章中的第一篇，涵盖了逐步实现现代 REST API 微服务的所有方面:

1.  **用 sqlc 定义 SQL 优先数据模型**
2.  [用 Gin 实现 REST API](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-39b0e966534a)
3.  [用 Viper 配置](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-1d48767a813)
4.  [在容器中构建和运行](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-e0070aa1b383)
5.  [集装箱测试](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-7c916ce2816a)

该系列的所有代码都可以在 https://github.com/bquenin/go-modern-rest-api-tutorial[获得](https://github.com/bquenin/go-modern-rest-api-tutorial)

# 我们的数据模型的真实来源是什么？

将数据库模型和查询与代码同步通常有两种主要方法:

*   **代码优先**:代码是真理的源泉，数据库模式就是从中生成的。这就是 ORM 库如 [gorm](https://github.com/go-gorm/gorm) 所做的。
*   **SQL 第一**:反过来说，SQL 模式是真理的来源，代码就是从中生成的。最流行的库是 sqlc。

这两种方法各有利弊。当您希望 ORM 库为您处理所有样板 CRUD 操作时，代码优先通常更好。但是，如果您想要微调数据库查询，它可能会碍事。在这种情况下，SQL 优先的方法可能更合适，允许您完全控制您的数据库模式并微调您的 SQL 查询，这可能是必要的，具体取决于您正在构建的 API 的类型。然而，使用这种方法，您将不得不自己编写所有的 SQL 查询。

哪个更好没有对错之分。这完全取决于您觉得哪种方式更合适，因为您可以使用这两种方式构建生产级应用程序。在本教程中，我们将关注 SQL 优先的方法，并使用 [sqlc](https://sqlc.dev/) 从我们的 SQL 模式和查询中生成代码。

# SQL 优先数据模型

定义数据模型是实现 REST API 微服务的关键步骤。你必须掌握正确的概念，并且很好地理解你的消费领域。

然而，这超出了本教程的范围，我们将只关注如何一步一步地构建 REST API 微服务。因此，我们将使用与 sqlc 教程中相同的模型:

schema.sql

查询. sql

*schema.sql* 文件描述了一个简单的 Author 对象，而 *queries.sql 文件*提供了所有的 CRUDL(创建、读取、更新、删除和列表)查询。正如您所注意到的，每个查询前面都有一个注释，提供要生成的函数的名称以及它返回多少结果(其中一个是 **exec，one，**和 **many** )。这个例子很简单，但是如果您的模型更复杂，可以参考 sqlc 文档。

部分更新 SQL 查询更复杂，因为 sqlc 中的查询必须事先完全结构化。因此，每个字段都根据相关布尔值的存在进行有条件的更新。例如，只有当**更新名称**字段为真时，才会更新**名称**字段。

非常感谢 [Brandur](https://medium.com/u/9644bc03a055?source=post_page-----c765d571b9e7--------------------------------) 关于 sqlc 和 Postgres 的[好文章。](https://brandur.org/sqlc)

# 生成 Go 代码

首先，我们需要配置 sqlc:

sqlc.yaml

在这种情况下，我们配置`sqlc`来产生 Postgres 兼容的代码。我们还为生成的代码指定了模式和查询文件、输出路径和包名。我们的项目结构如下所示:

```
├── sql
│   ├── queries.sql
│   └── schema.sql
└── sqlc.yaml
```

我们现在可以通过运行以下命令来生成代码:

```
sqlc generate
```

它生成以下文件:

```
├── **internal**
│   └── **database**
│       ├── **db.go**
│       ├── **models.go**
│       └── **queries.sql.go**
├── sql
│   ├── queries.sql
│   └── schema.sql
└── sqlc.yaml
```

models.go

query . SQL . go

正如您在标题中看到的，这段代码不能手动编辑。如果需要修改任何内容，必须在 *schema.sql* 或 *queries.sql* 文件中完成。您还可以更改 *sqlc.yaml* 配置来调整代码生成参数。

# 使用生成的代码

sqlc 生成的代码依赖于 Go 标准库 SQL 抽象。这意味着我们需要使用适当的 Go SQL 驱动程序来实例化到数据库的连接。以下文件就是这样做的:

postgres.go

我们现在可以实例化我们的 Postgres 数据库:

main.go

# 下一步是什么？

现在我们已经完成了我们的数据库实现，我们将看看如何实现我们的 REST API。
# 《与杜松子酒同行》中惊人的快速休息 API

> 原文：<https://betterprogramming.pub/modern-rest-api-with-go-and-postgresql-39b0e966534a>

## 用 Go 第 2 部分构建现代 REST API

![](img/dae2a7e2a240a9fd21359f81d38feca7.png)

Bjarne Vijfvinkel 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

本文是一系列文章中的第二篇，涵盖了逐步实现现代 REST API 微服务的所有方面:

1.  [用 sqlc 定义 SQL 优先数据模型](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-c765d571b9e7)
2.  **用 Gin 实现 REST API**
3.  [用 Viper 配置](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-1d48767a813)
4.  [在容器中构建和运行](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-e0070aa1b383)
5.  [集装箱测试](https://medium.com/@bquenin/modern-rest-api-with-go-and-postgresql-7c916ce2816a)

该系列的所有代码都可以在 https://github.com/bquenin/modern-go-rest-api-tutorial[获得](https://github.com/bquenin/modern-go-rest-api-tutorial)

# 数据模型与 API 模型

至于数据模型，我们必须设计我们的 REST API 模型。存储在数据库中的数据模型和通过网络发送的 REST API 模型相似但不相同。例如，您的用户数据模型中可能有一个密码字段，您不想通过网络发送它。

因此，虽然相似，但数据和 API 模型并不相同，在某些情况下，差异很大。我们可以将与数据模型相同的基本原理应用于 API 模型:

*   **API 优先**:有竞争的标准，但是 [OpenAPI](https://swagger.io/specification/) (又名 Swagger)是事实上的标准。OpenAPI 允许你描述 REST API 的所有方面:资源路径、输入、输出等等。您可以用自己喜欢的语言从 API 描述中生成客户机和服务器存根。例如，您可以使用 [oapi-codegen](https://github.com/deepmap/oapi-codegen) 在 Go 中创建存根。
*   **代码优先**:另一方面，您可以直接在代码中定义 API 端点、输入和输出，让您完全控制您的资源处理程序。在 Go 中，有几个著名的库允许快速 REST API 开发，比如 [gin](https://gin-gonic.com/) 、 [echo](https://echo.labstack.com/) ，或者 [chi](https://go-chi.io/) 。

这两种方法各有利弊，并且已经有大量关于何时使用它们的文献。同样，没有正确或错误的答案，因为它取决于许多因素。本教程将遵循使用 [gin](https://gin-gonic.com/) 库的代码优先方法。

# 实现作者服务

如前所述，我们正在为 sqlc 教程中提供的示例模型构建一个 REST API。我们希望实现以下方法:

*   在帖子上创建作者/作者
*   在 GET /authors/:id 上按 id 获取作者
*   PUT 上的完整更新作者/authors/:id
*   修补程序/authors/:id 上的部分更新作者
*   在删除/authors/:id 时删除作者
*   在 GET /authors 上列出作者

首先，我们需要定义我们的作者服务。我们将在以下位置添加 *authors.go* 文件:

```
└── api
    └── authors
        └── authors.go
```

注意，在 Go 中，有一个惯例是将所有 API 服务放在 **api** 文件夹下。如果我们要实现多个服务，我们可以将它们添加到各自的文件夹下，如下所示:

```
└── api
    ├── authors
    │   └── authors.go
    ├── books
    │   └── books.go
    └── publishers
        └── publishers.go
```

下面是我们的作者服务的基本实现:

作者服务的基本实现

我们在服务结构中唯一需要的字段是由 sqlc 生成的代码提供的**查询**对象。它允许运行我们之前定义的 SQL 查询。

# 注册处理程序

我们现在要为每个 API 方法定义一个处理程序。处理程序负责管理对特定 HTTP 动词和路径的任何调用。由于我们正在使用 [gin](https://gin-gonic.com/) ，我们将像这样向路由器注册我们的处理程序:

将 **RegisterHandlers** 方法添加到作者服务

# 实现处理程序

处理程序的实现分为 3 个步骤:

*   **解析请求:**验证并清理所有输入参数，
*   **执行请求**:执行我们用 sqlc 定义的 SQL 查询，
*   **构建响应**:编辑任何敏感数据，并返回请求的信息以及适当的 HTTP 状态代码。

## 创建作者处理程序

按照上面的结构，我们得到下面的**创建作者**处理程序:

创建作者处理程序

我们使用`apiAuthor`结构来解析和验证我们的输入。Gin 依靠 Go struct 标签来表达给定字段的验证约束。在我们的例子中，我们指定两个字段都是必需的，name 字段的最大长度是 32。

您可以表达许多验证约束:最大长度、最小长度、电子邮件、UUID，甚至比特币地址！确保查看验证器库的[文档，以了解所有提供的验证器。](https://github.com/go-playground/validator#baked-in-validations)

如果违反了任何约束，`ShouldBindJSON`方法会返回一个错误，我们会将该错误传播给调用者。

一旦输入被验证，我们通过从 sqlc 调用由`queries`对象提供的`CreateAuthor`函数来创建作者。

最后，我们将作者返回给调用者。我们使用以下方法将数据库作者转换为 API 作者:

将数据库对象转换为 API 对象

在这个例子中，数据库和 API 对象之间没有区别。然而，这通常是在需要时进行修订的地方。

## 获取作者处理程序

按照上面的结构，我们得到下面的`Get Author`处理程序:

对于这个 API 调用，`id`参数是一个路径参数。然而，我们可以遵循相同的模式，定义一个`pathParameters`结构来表达我们的验证约束，并像验证任何其他参数一样验证它。

一旦输入被验证，我们就从数据库返回用户。

## 删除作者处理程序

按照上面的结构，我们得到下面的`Delete Author`处理程序:

同样，唯一的参数是 **id** 路径参数，所以我们使用相同的验证机制。

然后，我们删除相应的作者，并返回一个带有 OK 状态代码的空响应。

## 列表作者处理程序

按照上面的结构，我们得到下面的**列表作者**处理程序:

这个 API 调用没有参数，所以我们执行 SQL 查询并将结果返回给调用者。请注意，这个实现很简单。现实世界的实现将提供搜索查询、过滤、排序、分页等。

# 更新作者

您可以对 REST API 资源执行两种类型的更新:

*   **完全更新**:调用者必须提供他们想要替换的对象的所有字段。任何缺少的字段都将被视为更新的空值，并将清空数据库中的相应字段。
*   **部分更新**:调用者只提供需要更新的字段。

根据您的使用情况，如果您想更新几个字段，完全更新可能有些过头，而且会浪费大量资源。这就是为什么部分更新通常是首选的原因。

有几种方法可以实现部分更新。最常见的有:

*   **JSON 合并补丁**:如 [RFC 7396](https://www.rfc-editor.org/rfc/rfc7396.html) 中所定义的，定义了只有请求中提供的字段才应该被验证和更新；任何缺失的字段都应保持不变。
*   **JSON 补丁**:如 [RFC 6902](https://www.rfc-editor.org/rfc/rfc6902) 中定义的，定义了一个补丁格式，调用者在其中定义要执行的更新。

在本教程中，我们将遵循 **JSON 合并补丁**，因为它实现起来稍微容易一些，但两者都在行业中广泛使用。

## 完全更新作者处理程序

完整更新需要更新作者的 ID 和完整内容。然后，我们必须相应地验证两个参数(路径和主体)。

一旦通过验证，我们就在数据库中更新作者，并将更新后的作者作为响应返回。

## 部分更新作者处理程序

如前所述，部分更新有点复杂，因为我们需要接受缺失的字段。为了适应这些，我们定义了一个`apiAuthorPartialUpdate`结构，其中:

1.  字段可以为空:使用指针，这样我们可以检查字段是否已经被提供，
2.  验证约束从“必需”更改为“忽略”。它告诉底层验证器只在字段存在时才验证字段，如果字段丢失，则不引发任何错误。

一旦验证成功，我们必须为 SQL 查询构建部分更新参数。如前所述，sqlc 希望查询是完全结构化的，因此我们必须指出哪一列要更新或不更新:

然后我们更新作者并将更新后的作者返回给调用者。

# 把所有的放在一起

现在我们已经实现了我们的处理程序，我们可以相应地更新 main 函数:

1.  如前一篇文章所述，实例化我们的数据库连接，
2.  用数据库查询对象实例化我们的作者服务，
3.  实例化路由器并注册我们的处理程序，
4.  启动服务器！

# 下一步是什么？

既然我们已经完成了 REST API 处理程序的实现，我们将看一看如何配置 API 服务器。
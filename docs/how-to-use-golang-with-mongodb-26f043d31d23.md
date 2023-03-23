# 如何在 MongoDB 中使用 Go

> 原文：<https://betterprogramming.pub/how-to-use-golang-with-mongodb-26f043d31d23>

## 连接到 MongoDB 服务器，用 Go 语言(Golang)执行基本的 CRUD 操作

![](img/ebb1deda122016ea02f989dcf71e33da.png)

[博伊图梅洛·菲特拉](https://unsplash.com/@writecodenow?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在本文中，我将向您介绍如何在 MongoDB 中使用 Golang。首先，我将向您展示如何安装 MongoDB 和重要的 Golang 包。

然后，我们将连接到一个 MongoDB 服务器，ping 它，并列出现有的数据库。之后，我将查看一个示例，其中我使用各种函数来执行 CRUD 操作。

# 安装 MongoDB 和 Golang 包

为您的平台安装 MongoDB 非常简单。你可以从[这里](https://www.mongodb.com/try/download/community)获得社区版。

鉴于我在 windows 上工作，我可以选择将 MongoDB 作为服务安装。我选择不这样做，因为我使用 MongoDB 作为测试服务器。但是，结果是我需要手动启动 MongoDB。

## 在 windows 上手动启动 MongoDB

要手动启动 MongoDB 服务器，可以执行以下操作:

*   首先，确保在运行命令的路径中有一个数据目录——在下面的例子中，它是`./data`。
*   然后在您的终端中运行`"C:\Program Files\MongoDB\Server\5.0\bin\mongod" — dbpath ./data`。你的道路可能略有不同。在这种情况下，您可以搜索`mongod`,看看您的系统上的路径可能是什么。

## MongoDB 指南针桌面应用程序

也可以安装 MongoDB Compass。在这里得到它。

这个桌面应用可以帮助你管理和审计你输入到 MongoDB 服务器的数据。

这对于测试来说非常方便，因为这个应用程序可以让你看到哪些数据库、集合和文档已经被创建。

## 服务器地址和密码

在本文的其余部分，我的 MongoDB 数据库服务器将运行在地址`mongodb://localhost:27017`上。

由于数据库只是为了探索和测试 Golang 中的想法，我没有在数据库服务器上设置用户名和密码。

如果您将它用于生产代码，您将需要保护您的 MongoDB 服务器。

因此，当使用 MongoDB 服务器进行生产时，地址看起来会有所不同，包括用户名和密码。请参见此处的文档。

## 为 MongoDB 安装 Golang 包

要在 Golang 中使用 MongoDB，您可能需要在终端中运行以下命令:

```
go mod init
go get go.mongodb.org/mongo-driver/mongo
go get go.mongodb.org/mongo-driver/bson
```

## BSON 用于数据的序列化

在上面的命令中，你可以看到我们也安装了 BSON。BSON 是一种类似于 JSON 的序列化格式。

根据 BSON 规范:

> BSON [bee sahn]，是 Bin ary JSON 的缩写，是类似 JSON 的文档的二进制编码序列。像 JSON 一样，BSON 支持文档和 ar 数组与其他文档和 ar 数组的嵌套。BSON 还包含一些扩展，允许表示不属于 JSON 规范的数据类型。

其余规格可在此处阅读[。](https://bsonspec.org./)

我们将在 MongoDB 中使用它，用于保存文档数据和在查询中处理文档时创建过滤器等。

# MongoDB 服务器连接和断开

让我们从连接到 MongoDB 服务器开始。

我们可以创造许多不同形式的语境。对于这个例子，我将创建尽可能简单的上下文，即`context.TODO()`。你可以在这里阅读更多关于上下文[的内容。](https://docs.mongodb.com/drivers/go/current/fundamentals/context/)

如上所述，我的数据库服务器位于:`mongodb://localhost:27017`。

请注意，我们在第 17 行推迟了断开客户机与数据库的连接。这意味着 disconnect 将发生在它所在的函数中的最后一条语句之后——在本例中，它是`main()`。

如果在一个语句之后发生任何错误，Go 仍然会执行延迟的`Disconnect()`函数调用。

# ping MongoDB 服务器

如果我将 MongoDB 的端口更改为不正确的端口号，例如`20000`，那么 MongoDB 客户端将不会给出错误。

因此，为了确保我们有一个正确的连接，我们还可以`Ping()`数据库。

上面的代码会给出一个错误，因为我的 MongoDB 服务器不是运行在端口`20000`上，而是运行在端口`27017`上。

如果我将端口更改为正确的端口号，`Ping()`语句将不会给出错误，程序将成功完成。

# 列出 MongoDB 服务器上的当前数据库

可以列出 MongoDB 服务器上的数据库。如果您刚刚安装了服务器，可能只有几个“管理”数据库。

一旦添加了更多的数据库，您就会在第 25 行的语句打印的清单中看到它们。

# 创建数据库集合

> 这里有一个提示:在其他数据库服务器中，集合通常被称为数据库表。

让我们首先在 MongoDB 服务器上创建一个名为`test`的数据库，然后我们将向这个名为`example`的数据库添加一个集合。

由于这只是一个示例，并且我们不想在程序运行后保留数据库，我们还将丢弃(删除)该集合。所以在第 25 行，我们将推迟`Drop()`函数调用，这样它将在`main()`函数中所有其他语句之后执行。

显然，如果您希望在程序运行后保留数据库集合，您应该删除`Drop()`语句(即删除第 25 行)。

# 将文档添加到集合

这里，我们将文档添加到刚刚创建的集合中。

我们插入的每个文档包含三个字段:`someString`、`someInteger`和`someStringSlice`。

在第 1–5 行，我们创建了一个包含三个字段的文档，如上所述。

在第 6 行，让我们首先使用`InsertOne()`函数插入这个项目。

在第 10 行，我们可以打印出集合中商品的 ID。为此，我们可以使用由`InsertOne()`函数返回的结果的`InsertedID`属性。

在第 11–22 行，创建了另外两个文档。

然后在第 23 行和第 27 行，我们将使用`InsertMany()`插入这两个文档，并用`InsertedIDs`属性列出这些 id。

在第 29 行，我们还将包含`time.Sleep()`语句，这样我们就可以使用 MongoDB Compass 来检查在集合被删除之前，条目是否已经被正确地插入到集合中。如果您决定在程序结束后保留集合，则没有必要保留 sleep 语句。

# 查询收款单据

现在我们可以查询集合，因为我们已经在 MongoDB 集合中插入了文档。

在第 1 行，我们将使用`FindOne()`在集合中查找一个文档。对于这个例子，这个文档的 ID 将与我们插入到集合中的第一个文档的 ID 相匹配。

在第 2 行，我们创建了一个名为`exampleResult`的 BSON 映射，用于保存查询的解码结果。这个解码结果是一个文档。通过使用 BSON 地图，很容易获得字段的值。

在第 3 行，使用`Decode()`将查询结果解码为`exampleResult`。

在第 5–8 行，我们打印出 ID 和文档的三个字段。

然后，在第 10 行，创建一个过滤器，根据文档的`someInteger`字段过滤文档。该字段应大于 60。

在第 11 行，我们将找到几个带有这个过滤器的文档。

在第 15 行，创建了一片名为`examplesResult`的 BSON 地图来保存结果文档。

在第 16 行，使用函数`All()`而不是`Decode()`将文档提取到`examplesResult`。

随后，在第 20-25 行，包含在`examplesResult`中的文档字段将被打印出来。

# 更新收款凭证

在这里，我介绍了三种更新文档的方法。

在第 1–7 行，我们更新了一个 ID 为`r.InsertedID`的文档(这是我们插入到集合中的第一个文档的 ID)。在本文件中，`someInteger`字段被设置为`201`。为了通过 ID 更新文档，我们使用了名为`UpdateByID()`的函数。

在第 11 行，我们打印已经更新了多少个文档。更新文档的数量包含在结果中，并使用`ModifiedCount`提取。这种情况下应该是`1`。

在第 14–20 行，我们更新了一个文档。为了更新一个文档，我们使用名为`UpdateOne()`的函数。同样，该文档的 ID 为`r.InsertedID`，但其地址与之前不同。这一次，`someString`域被设置为`"The Updated String"`。

事实上，除了使用文档 ID，您还可以使用不同的方法来查找要更新的文档。它可以是满足特定属性的文档。例如，这可能是`someInteger`等于`201`的文档。

在第 27–33 行，我们可以看到这个想法。在这里，我们更新满足特定属性的所有文档。该属性需要`someInteger`在`60`之上。满足这一条件的所有文档都被更新，其`someInteger`字段被设置为`60`。

正如您在上面的例子中看到的，当更新时，您通常只更新文档的一个或几个字段。

另一方面，如果您希望更新一个完整的文档，使用`ReplaceOne()`函数可能会更好。你可以在这里阅读更多关于函数[的内容。](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.8.0/mongo#Collection.ReplaceOne)

# 删除收款单据

最后但同样重要的是，这里有一个删除文档的方法。

在第 1 行，我们将删除使用`DeleteOne()`函数插入到集合中的第一个文档。

在第 6 行，我们将打印出删除了多少文档。使用`DeletedCount`从`DeleteOne()`的结果中提取该值。这类似于更新文档时的`ModifiedCount`属性。

MongoDB 包还包含一个名为`DeleteMany()`的函数，用于一次删除多个文档。你可以在这里阅读更多相关信息[。](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.8.0/mongo#Collection.DeleteMany)

# 完整的代码…

下面你可以找到这篇文章的全部代码。上面所有的代码片段都包括在内。

# 进一步阅读

[](/how-to-use-golang-structs-with-mongodb-f1772e4a1da3) [## 如何在 MongoDB 中使用 Golang 结构

### 在这个简短的例子中，我将向您展示如何在 Go 语言中创建用于 MongoDB 的结构

better 编程. pub](/how-to-use-golang-structs-with-mongodb-f1772e4a1da3) 

# 参考

[“如何在 Go 中使用 MongoDB”作者 Solomon Esenyi](https://blog.logrocket.com/how-to-use-mongodb-with-go/)

[tree house 的“在 Windows 上安装 MongoDB”](https://treehouse.github.io/installation-guides/windows/mongo-windows.html)

[BSON 规范](https://bsonspec.org./)

[“只有真正的围棋忍者才会使用 MongoDB”，作者唐纳德·弗瑞](https://youtu.be/D3jhplPWqnA)
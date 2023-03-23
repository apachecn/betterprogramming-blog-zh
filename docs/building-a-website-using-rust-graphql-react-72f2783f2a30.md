# 使用 Rust、GraphQL、React 建立网站

> 原文：<https://betterprogramming.pub/building-a-website-using-rust-graphql-react-72f2783f2a30>

## 帮助您更快创建的分步指南

![](img/bf19b4890964b2a74a27baf95529f258.png)

照片由[劳塔罗·安德烈亚尼](https://unsplash.com/@lautaroandreani?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在之前的一篇文章中，我解释了如何使用 React、gRPC-web 和 Rust 创建我的个人网站。在本文中，我将修改代码以使用 GraphQL 接口。

对于服务器，我使用 [Rocket](https://rocket.rs/) web 框架和 [Juniper](https://github.com/graphql-rust/juniper) 库来服务 [GraphQL](https://graphql.org/) 。

对于客户端，我使用 [React](https://reactjs.org/) 和 [Apollo 客户端](https://www.apollographql.com/docs/react/)，并通过 [Express](https://expressjs.com/) 为其提供服务。

# 计算机网络服务器

对于服务器，我们需要以下板条箱:

*   `rocket="0.5.0-rc.2”`
*   `juniper={version="0.15.10",features=["uuid","chrono"]}`
*   `juniper_rocket="0.8.2”`帮助整合杜松和火箭
*   `chrono={version="0.4.22",features=["serde"]}`用于创建时间戳并将其序列化到 json
*   `uuid={version="0.8.2",features=["v4","serde"]}`用于创建 UUIDs 并将它们序列化为 json

对于这个例子，我们不会实现数据库，而是使用一个[散列表](https://doc.rust-lang.org/std/collections/struct.HashMap.html)来存储我们的数据。让我们首先声明所需板条箱的用途，并为`Database`添加一个结构。我们的页面`Object`将被称为`Page`，我们将使用一个名为`“home”`的字符串键来存储它。我们还需要让我们的领域访问我们的数据库。为此，我们需要为我们的`Database`实现`Context`。

有了这些，我们将首先实现我们的模式并用内容初始化我们的数据库。接下来是内容，就像 gRPC-web 应用程序中的 [Editor.js](https://editorjs.io/) 模式。

## (计划或理论的)纲要

Juniper 有定义 GraphQL [对象](https://docs.rs/juniper/0.15.10/juniper/attr.graphql_object.html)和[接口](https://docs.rs/juniper/0.15.10/juniper/attr.graphql_interface.html)的宏。我们将使用它们。Juniper 允许我们使用一个[结构](https://graphql-rust.github.io/juniper/master/types/objects/defining_objects.html)或者一个实现作为 GraphQL 对象。使用实现允许我们手动定义我们的解析器。例如，我们可以解析计算字段。这同样适用于 GraphQL 接口。

让我们首先尝试一个简单的结构作为页面的 GraphQL 对象。对于我们的块，我们将使用一个向量，并将我们的块称为`BlockValue`。一旦我们实现了我们的`Block`接口，这一点就会变得很清楚，因为每一个`Block` 类型都需要成为一个`Block`。

我们还需要一个实现，因为我们想更容易地创建一个新的`Page`。代码如下:

如前所述，每个块类型都是一个`Block`。因此我们将实现一个名为`Block`的接口。这个`Block`应该有两个标准字段`id`和`type`。由于`type`是一个保留名称，我们需要对其进行重命名，并对其进行注释。因为我们为这个接口实现了三个对象，所以我们还需要给它们命名。

由于`to_vec()`需要特征`Clone`，我们必须为`Interface`枚举实现它，枚举是由宏 graphql_interface 生成的。

现在我们可以实现三种不同的`Block`类型。我将只显示列表类型，因为其他两个几乎相同。`block_type`将使用函数导出，不会存储在我们的数据库中。我们还将在我们的`ListBlock`中添加一个`new`函数。

接下来，我们将实现接口和 GraphQL 对象。当我们使用我们的实现作为我们的对象时，我们将需要复制一些代码。如果你知道更好的解决方法，请告诉我。

`ListData`将有一个被约束为两个选项的样式字段。我们可以通过使用 enum 和 [GraphQLEnum](https://graphql-rust.github.io/juniper/master/types/enums.html) 派生宏来实现这一点。

最后，我们可以为我们的`Page`实现`Query`。

这也适用于`ParagraphBlock`和`HeaderBlock`。

## 数据库ˌ资料库

`Database`需要两个功能:

*   一个初始化数据库的新函数
*   `get_page`函数获取逐页名称

如果有页面，函数`get_page`返回页面。

对于新函数，我们将初始化一个可变的`HashMap`，然后插入我们的`Pages`。

这就是我们实现数据库和模式所需的全部内容。

# 应用

对于与 rocket 的集成，我们坚持使用官方的[示例](https://github.com/graphql-rust/juniper/blob/master/juniper_rocket/examples/rocket_server.rs)。

由于这不包括 CORS 标题，我们将需要做出一些改变。有板条箱 [rocket_cors](https://docs.rs/rocket_cors/latest/rocket_cors/) 来做这件事，但是它目前还不稳定，所以我们手动做。

我们将需要更多的导入，如下所示:

这个想法是创建一个`Fairing`，它将把`CORS`头添加到每个响应中。此外，我们将需要添加一个 [HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/OPTIONS) `[OPTIONS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/OPTIONS)` [方法](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/OPTIONS)处理程序，这是`CORS`所必需的。选项处理程序没有任何逻辑，因为唯一需要的是响应中的`CORS`头。

最后，我们只需要添加新的路线，并将我们的`CORS` `Fairing`连接到我们的火箭<构件>上。

官方示例还包括一条 GraphiQL 路线。因此，通过启动我们的应用程序，我们已经可以尝试我们的服务器。让我们启动服务器，前往`[http://127.0.0.1:8000/](http://127.0.0.1:8000/)`。

```
cargo run
```

我们可以这样查询我们的服务器:

# 客户

客户端与 [gRPC-web 示例](https://medium.com/@christopher-scholz/building-a-website-using-rust-grpc-web-react-7412f1596a17)中的相同，所以我只解释如何实现 Apollo 客户端。

我们将需要添加

*   `@apollo/client@~3.7.0`
*   `graphql@~16.6.0`

敬我们的包裹。

在我们的`index.js`文件中，我们将实例化 apollo 客户机，如下所示:

并添加`ApolloProvider`组件作为`React`组件的子组件。`root.render`功能的其余部分保持不变。

组件将查询服务器并将数据存储在一个数据变量中。

return 函数将以与 gRPC-web 示例中相同的方式呈现块。

这就是全部。我们现在可以运行我们的客户机和服务器，并浏览到`[http://127.0.0.1:8000](http://127.0.0.1:8000)`。

```
docker-compose up --build
```

# 结论

Juniper 有很好的文档记录。如果你不喜欢 Rocket，其他很多集成都可以。此外，阿波罗客户端是非常完善的。你可能有的每一个问题都会在栈溢出时得到回答。

我喜欢一切都很容易设置，以及它的工作原理。由于 Juniper 使用的是[代码优先的方法](https://blog.logrocket.com/code-first-vs-schema-first-development-graphql/)，我们将不得不编写相当多的代码。如果您喜欢使用[模式优先的方法](https://blog.logrocket.com/code-first-vs-schema-first-development-graphql/),那么有一个 [juniper-from-schema](https://github.com/davidpdrsn/juniper-from-schema) 的箱子可以做到这一点。

应用程序的完整代码可以在[这里](https://github.com/christopherscholz/rust_graphql_website)找到。
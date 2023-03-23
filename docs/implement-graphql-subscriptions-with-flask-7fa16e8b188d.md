# 用 Flask 实现 GraphQL 订阅

> 原文：<https://betterprogramming.pub/implement-graphql-subscriptions-with-flask-7fa16e8b188d>

## 用 GraphQL 和 Flask 监听数据库变化

![](img/0d350944c1ce2fece7099e26b3fd16cd.png)

来源:[维基共享资源](https://commons.wikimedia.org/wiki/File:Horn_flask_(PSF).png)

我最近需要在基于 Flask 的 Graphql 应用程序上实现订阅。在搜索了一个简单实现的常见位置后，我什么也没找到。别担心——我想出了一个巧妙的方法来解决这个问题。我希望其他人也发现它是有帮助的。

首先，我们使用您最喜欢的工具设置了一个虚拟环境。我用`pipenv`。

您将需要安装 [Altair GraphQL 客户端](https://altair.sirmuel.design/)，这将使我们能够测试我们是否已经成功实现订阅。

我们将需要以下包:

*   烧瓶:一个 web 框架
*   **Graphene** :用于构建 Graphql APIs 的 python 库
*   **Flask-graphql** :为您的 Flask 应用程序添加 graphql 支持
*   Gevent :提供高级同步 API
*   烧瓶插座:为你的烧瓶应用程序设计的优雅的网络插座
*   **Graphql-ws** :用于 Graphql 订阅的 websocket 服务器

在我们做任何其他事情之前，让我们创建一个我们将要工作的文件夹。

```
$ mkdir my_app
$ cd my_app
```

从现在开始，我们将从这个文件夹开始工作。安装软件包:

```
$ pipenv install flask graphene flask-graphql gevent flask-sockets graphql-ws
```

激活`pipenv`外壳:

```
$ pipenv shell
```

创建一个文件夹并创建`app.py`，它将是我们应用程序的主要部分，以及`schema.py`，它将包含我们的 GraphQL 模式。

```
$ touch app.py
$ touch schema.py
```

在`app.py`文件中初始化一个支持 GraphQL 的 flask 应用程序:

使用模式来确保您有东西要导入:

现在，您可以通过运行以下命令来运行 flask 服务器，以确保一切正常运行:

```
python app.py
```

访问链接`localhost:5000/graphql`并查询样品。一旦您确认它正在工作，那么是时候来看看是什么让您来到这里了:向 API 添加订阅。

一点背景:订阅需要网络套接字。为此，我们使用之前安装的`graphql_ws`包。从文档来看，在 gevent 服务器上应用它似乎很简单(它允许同步 API 调用)。然而，对于 Flask，我发现我必须做一些重写才能让它工作。

首先，我们编辑`app.py`文件来引入套接字，并将 GraphQL 后端改为支持订阅的后端。我们还需要创建一个新文件`overriddenview.py`，在这里我们调整`GraphQLView`以允许它支持可订阅模式。

您的代码应该如下所示。不要被它有多长吓到，实际上它很简单:

在 overridden.py 文件中，代码与 GraphQLView 的代码完全相同。然而，内置的 GraphQLVIew 不能很好地处理可观测量。由于格式化执行结果的函数不是类方法的一部分，我们必须编写一个新的类，并让它使用我们为它定制的函数。在这种情况下，改变的函数是`format_execution_result`。

我们添加 if 语句来处理返回数据是一个`AnonymousObservable`的实例。我们在这个文件中使用函数`encode_execution_results` 的原因是这个函数调用了我们修改过的`format_execution_result`函数。在内置类中，这两个函数都是在文件`graphql_server`中定义的——因为我们不能更改那些特定的文件，所以我们必须使用这个变通方法。在我们用来覆盖`GraphQLView`的`OverriddenView`类中，它确保调用我们在这个文件中定义的函数，而不是那些在包中定义的函数。

现在，在`app.py`上，我们覆盖了`graphl`使用的后端，因为默认后端已经将`allow_subscriptions`设置为 false。我们还设置了套接字，众所周知，这是使用订阅来确保客户端和后端之间永久连接的重要部分。gevent 服务器允许我们进行同步 API 调用。

我们差不多完成了。剩下的工作就是在模式中定义一个订阅。我们按如下方式更新 schema.py 文件:

我们已经定义了一个订阅，它会计算我们通过的秒数，并在每秒钟提醒我们。现在我们运行应用程序:

```
$ python app.py
```

然后，我们启动 Altair Graphql 客户端。我们向`localhost:5000/graphql`发出 post 请求，向`ws://localhost:5000/subscriptions`指定订阅 URL，并使用 graphql 对象发出请求:

```
subscription{
  countSeconds(upTo: 10)
}
```

然后它会在十秒钟内每秒提醒你一次。

[上面代码的 Github 库](https://github.com/caveinn/flask_graphql_subscription)

感谢阅读！
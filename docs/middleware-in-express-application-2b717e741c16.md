# Express 应用程序中的中间件

> 原文：<https://betterprogramming.pub/middleware-in-express-application-2b717e741c16>

## 这远没有听起来那么复杂

![](img/6699742d94a1553d7e74fe525e8b6963.png)

由[昆滕·德格拉夫](https://unsplash.com/@quinten149?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

一个 [Express](https://expressjs.com/) 应用程序的核心概念之一是中间件，我们大多数人都被它弄糊涂了。

首先，把中间件想象成一个简单的函数，它接受一个请求对象，并将控制权传递给下一个中间件函数，或者向客户机发送一个响应。

中间件的一个最简单的例子是 Express 框架中的路由处理器功能。那些已经对 Express 框架有所了解的人熟悉下面的代码:

```
app.get('/', (req, res) => {
  res.send({ id: 1, name: 'Ashraful' });
}
```

在上面的例子中，`get`函数接受一个请求并向客户端发送一个响应。

另一个例子是从我们编写的请求体中获取 JSON 对象:

```
app.use(express.json())
```

`express.json()`解析请求体，并在路由处理程序的`req.body`中设置对象。这里，`express.json()`返回一个中间件函数。

通过下图，我们可以很容易地理解请求和响应周期:

![](img/838609e41694945e13e16e65e8b5d271.png)

Express 中的请求-响应管道

把它想象成一条管道。在管道一端，我们将拥有我们请求的对象。另一边，会有响应对象。在请求和响应之间，我们可以放置一些中间件。

Express 附带了一些内置的中间件功能。但是我们也可以创建自定义的中间件功能，我们可以将它们放在中间件功能之间来执行一些操作。一个很好的例子是使用定制中间件执行身份验证和授权。

# 编写自定义中间件函数

要成为中间件功能，有一些要求:

1.  它将接受一个请求对象。
2.  它将执行自己的特定操作，并将控制权传递给下一个中间件。
3.  它通过向客户端发送响应来终止请求-响应管道。

```
const loggerMiddleware = function (req, res, next) {
  // Perform operation
  next();
}app.use(loggerMiddleware);
```

在执行一个中间件功能后，我们必须将控制传递给下一个中间件，或者通过发送请求来终止请求-响应循环。否则，它将保留在管道中。

我们可以在 Express 应用程序中使用不同类型的中间件。

# 1.应用层中间件

在本文的开头，我们看到了一个路由处理函数的例子。每个路由处理器都是一个中间件功能，我们可以称路由处理器为应用级中间件。

通常，我们通过使用`app.use()`和`app.METHOD()`函数将应用级中间件绑定到`app`对象的实例，其中`METHOD`是指 HTTP 方法`get/post/put/delete`:

这里，我们有两个中间件功能。它们将按顺序被调用。

# 2.路由器级中间件

路由器级中间件的工作方式与应用层中间件相同。唯一的区别是我们将这个中间件绑定到一个路由器实例:

我们主要将这种中间件用于中型到大型的快递应用程序。当我们的分配增加时，我们通常按照特性或资源(在 REST API 术语中)来拆分整个应用程序代码。

因此，如果我们的应用程序 API 中有两个资源，比如`users`和`posts`，那么我们将创建两个路由器实例。

## 用户. js

## posts.js

现在，在主入口点文件中，我们将告诉 Express 我们希望在哪个路径上安装`users`和`posts`路由器:

# 3.错误处理中间件

错误处理中间件与其他中间件相同，但是它有四个参数，而不是三个。

```
app.use(function (err, req, res, next) {
  console.error(err.stack)
  res.status(500).send('Something broke!')
})
```

我们必须提供四个参数。否则，Express 不会理解该函数是一个错误处理中间件。

# 4.内置中间件

从 4.x 版开始，Express 具有以下内置中间件功能:

*   `express.static`提供静态资产，如 HTML 文件、图像等等。
*   `express.json`用 JSON 有效负载解析传入的请求。
*   `express.urlencoded`用 URL 编码的有效负载解析传入的请求。

[此列表](https://github.com/senchalabs/connect#middleware)包含以前包含在 Express 中的中间件功能，但现在位于单独的模块中。

# 5.第三方中间件

我们可以添加第三方中间件来扩展 Express 的功能。[这个列表](https://expressjs.com/en/resources/middleware.html)展示了我们在 Express 中使用的一些流行的第三方中间件。

# 结论

Express 应用程序是一组中间件功能。我们应该始终记住的一点是，我们可以在应用程序中放置许多自定义或第三方中间件功能，但中间件会对应用程序的性能产生影响。

[Express 的文档](https://expressjs.com/en/guide/using-middleware.html)对不同的中间件功能定义及其用法有全面的指导。
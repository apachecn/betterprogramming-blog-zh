# 快速应用程序对象指南:路由

> 原文：<https://betterprogramming.pub/guide-to-the-express-application-object-routing-6e89d6257d2>

## 让我们看看路由和参数

![](img/f69c019ec7ebdf1b807755df728a02a5.png)

[迭戈·希门尼斯](https://unsplash.com/@diegojimenez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

快速应用程序的核心部分是应用程序对象。是应用本身。

在这篇文章中，我们将看看`app`对象的方法以及我们可以用它做什么。

# app。方法(路径，回调[，回调…])

这个方法路由一个 HTTP 请求。`METHOD`是 Express 支持的各种路由方法的占位符。

它采用以下参数:

*   `path` —它可以是表示路径或路径模式的字符串或正则表达式。默认为`/`。
*   `callback` —处理请求的功能。它可以是一个中间件功能、一系列中间件功能、一系列中间件功能或以上所有功能的组合。

Express 支持以下路由方法:

*   `checkout`
*   `copy`
*   `delete`
*   `get`
*   `head`
*   `lock`
*   `merge`
*   `mkactivity`
*   `mkcol`
*   `move`
*   `m-search`
*   `notify`
*   `options`
*   `patch`
*   `post`
*   `purge`
*   `put`
*   `report`
*   `search`
*   `subscribe`
*   `trace`
*   `unlock`
*   `unsubscribe`

所有方法都采用相同的参数，并以完全相同的方式工作。所以`app.get`和`app.unlock`是一样的。

# app . param([名称]，回调)

`app.param`增加路由参数的回调触发器。`name`是参数或它们的数组。`callback`是一个回调函数。

回调的参数是请求对象、响应对象、下一个中间件函数、参数值和参数名，按照给定的顺序排列。

例如，我们可以如下使用它:

当`id` URL 参数与`app.param`回调一起存在时，上面的代码将获得该参数。

然后在回调中，我们让`id`到`req.id`，并调用`next`来调用`app.get`中的路由处理程序。

然后我们调用`res.send(req.id);`发送带有`req.id`的响应，这是我们之前设置的。

注意`app.param`中的`id`必须将`:id`与路线处理器匹配。

我们可以传入一组我们想要监视的参数:

然后，如果我们请求 `/1/foo`，我们将一次得到一个`1`和`foo`作为`value`参数的值。

# app.path()

`app.path`以字符串形式返回已安装应用程序的路径。

例如，我们可以如下使用它:

由于我们将`foo`安装到`app`上，将`bar`安装到`foo`上，因此`app.path()`将得到`''`，而`foo.path()`将得到`'/foo'`，而`bar.path()`将得到`'/foo/bar'`。

![](img/b7fb7af69dfe4412e4d37b9f21c9c19f.png)

[Bundo Kim](https://unsplash.com/@bundo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# app.post(路径，回调[，回调…])

我们可以使用`app.post`方法，通过传入一个`callback`路由处理程序，用给定的`path`来处理 POST 请求。

它采用以下参数:

*   `path` —可以是表示路径或路径模式的字符串或正则表达式。默认为`/`。
*   `callback` —处理请求的功能。它可以是一个中间件功能、一系列中间件功能、一系列中间件功能或以上所有功能的组合。

例如，我们可以如下使用它:

然后，当我们向像 Postman 这样的客户发出 POST 请求时，我们应该看到“*POST request maked*”

# app.put(路径，回调[，回调…])

我们可以使用`app.put`方法通过传入一个`callback`路由处理程序来处理带有给定`path`的 PUT 请求。

它采用以下参数:

*   `path` —可以是表示路径或路径模式的字符串或正则表达式。默认是`/`。
*   `callback` —处理请求的功能。它可以是一个中间件功能、一系列中间件功能、一系列中间件功能或以上所有功能的组合。

例如，我们可以如下使用它:

然后，当我们向像 Postman 这样的客户机发出 PUT 请求时，我们应该看到"*PUT request maked*"

# 结论

我们可以用`app.params`方法拦截请求发送的参数。

为了监听 POST 请求，我们可以使用`app.post`方法。为了监听 PUT 请求，我们可以使用`app.put`方法。

`app.path`让我们获取已安装应用的路径。

`app`也有一长串监听各种请求的方法。
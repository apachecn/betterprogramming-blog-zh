# Omicron:在 Node.js 中构建 HTTP 服务器的简单库

> 原文：<https://betterprogramming.pub/omicron-a-simple-library-to-build-http-servers-in-node-js-421ef387da7c>

## 以更实用的方式构建服务器

![](img/e71b040986e7d6c80e4137a5b1822144.png)

詹姆斯·哈里森在 [Unsplash](https://unsplash.com/@jstrippa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

几天前，我开始开发一个新的 npm 包，在 Node.js 中构建 HTTP 服务器。

目标是创建一种以更实用的方式构建服务器的方法。结果是`Omicron`。你可以在 npm 上查看[。](https://www.npmjs.com/package/@zaetrik/omicron)

# 简短的介绍

首先，去拿`Omicron` → `npm i --save @zaetrik/omicron`。为了遵循本文中的示例，您还需要 [fp-ts](https://github.com/gcanti/fp-ts) 和 [io-ts](https://github.com/gcanti/io-ts) 。可以用`npm i --save fp-ts io-ts`安装。

# 简单的服务器示例

现在让我们启动一个简单的服务器来处理对`http://localhost:3000/`的`GET`请求。

去运行你的文件吧！您现在应该有一个正在运行的服务器，它用“Hello”来响应对`http://localhost:3000/`的`GET`请求👋".

正如您可能已经看到的，我们必须定义两个处理函数。第一个处理程序用于传入的请求，它将接收传入的请求。第二个函数是错误处理程序，只有在处理程序中出现错误时才会执行。

# 使用自定义设置进行响应

您也可以发送带有自定义设置的响应:

要返回带有自定义设置的响应，您的处理函数必须返回类型为`RouteResponse`的内容。在上面的例子中，我们在自定义头的帮助下向客户机发回了一个 cookie。

# 中间件

`Omicron`也允许你使用中间件。你可以用中间件功能做一些非常酷的事情。

您可以将中间件与`Omicron`中的`useMiddleware()`功能一起使用。这个函数除了将中间件函数与您的处理函数组合起来之外，基本上什么也不做。

你可以很容易地编写中间件函数。传入的请求被传递到您的中间件，您可以从中间件函数返回您想要的任何数据。这些数据然后被传递给你的处理器。

中间件必须接受一个传入的请求，并返回类型为`Either<Error, HttpRequest | unknown>`的东西。还可以选择编写返回原始数据的中间件，并在中间件失败时抛出错误。您可以通过`useErrorThrowingMiddleware()`功能使用它们。

下面是一个示例中间件，它在一个`Option<RequestBody>`中返回一个`RequestBody`:

如您所见，您可以编写自己的小中间件函数来转换传入的请求以满足您的需求。

你也可以传入多个中间件。它们是从左向右执行的。来自第一个中间件的结果将被传递到第二个中间件，依此类推，直到它最终被传递到您的处理函数。

中间件功能的另一个有趣的例子是输入验证。

在这里，我们编写了一个小的验证器函数，它接受我们用`io-ts`定义的类型，然后返回一个验证传入请求体的中间件函数。

这真的很强大，因为我们现在可以将一个类型化的对象返回给我们的处理函数，它肯定具有我们想要的类型。然后，我们可以在处理函数中处理我们的类型化数据。

是一个正在开发的产品，仍然缺少很多重要的特性，但是你已经可以用它来创建一些很酷的东西了。如果你决定用`Omicron`建造什么东西，我很想看看。

# 参考

*   奥米克隆图书馆—[https://www.npmjs.com/package/@zaetrik/omicron](https://www.npmjs.com/package/@zaetrik/omicron)
*   fp-ts 库—[https://github.com/gcanti/fp-ts](https://github.com/gcanti/fp-ts)
*   io-ts 库—[https://github.com/gcanti/io-ts](https://github.com/gcanti/io-ts)
# 如何使用 Bun.js 编写类似 Express 的 API

> 原文：<https://betterprogramming.pub/write-a-express-like-api-using-bunjs-79e77a6a7a31>

## 使用新的运行时

![](img/22a05c0d199617be3171c9360979a2be.png)

我偶然在 Youtube 上发现了一些有趣的技术视频，然后我发现了一个关于 [Bun](https://github.com/oven-sh/bun) 的视频，这是一个用 Zig 编写的 JavaScript 运行时。它自称是一个非常快的运行时，比 Nodejs 快得多。

所以我决定试一试。

下面是如何使用命令行下载 [bun](https://bun.sh/) :

```
curl -fsSL https://bun.sh/install | bash
```

根据 bun 的文档，这就是如何从您的`index.js`文件启动 HTTP 服务器:

```
export default {
  fetch(req) {
    return new Response("HI!");
  },
};
```

在上面的代码中，如果 JavaScript 文件导出了一个默认对象，其中包含了`fetch`函数，那么它将启动服务器。

或者你可以使用`Bun.serve`:

```
Bun.serve({
  fetch(req) {
    return new Response("HI!");
  },
});
```

要运行 JavaScript 文件，只需执行`bun run index.js`。

就是这样！一切正常…

![](img/221540fff0238dc66d1fa81a03d8d736.png)

詹姆斯·哈里森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

然而，这个 API 可能有点复杂。当项目变得更大时，我们需要一个好的方法来完成它。所以，我决定从头开始用 bun 做一个类似 express 的 API。通过这样做，任何熟悉 ExpressJS 的人都能够快速进入 bun。

首先，我们需要处理请求。

我们将创建一个包含 HTTP 方法的`BunServer`类:

在 HTTP 请求方法函数中，第一个参数`path`是请求的路径，第二个参数是用于处理请求的回调数组。

一切经过 HTTP 的方法都会用`delegate`函数来处理。

*   `requestMap`存储路径的映射，它的处理程序是用户处理请求的业务逻辑。
*   `middlewares`存储用户声明的所有中间件。

上面是`delegate`函数，我们根据它们的请求方法和路径以及用户的 handler 函数来存储它们。所以每次用户调用路径时，我们都能够找到他们对应的处理程序。

例如，我们称这个 API 为:

```
curl -X POST 'http://localhost:3000/test
```

在我们的`requestMap`中，键会变成`POST:/test`，值是类似`(req, res) => { console.log('Donate me pls'); }`的处理函数

需要注意的是，`handlers`中最后一个函数之前的函数是`middlewares`，最后一个函数是实际的请求处理函数。

> 这就是我们处理请求的方式，非常简单。

接下来是**中间件**。

在 express 上，我们有一个带有签名`(req, res, next)`的中间件功能，这是一个[责任链设计模式](https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern)。

我的实现在这里也很简单。

链家网

我创建了一个`Chain`函数来处理中间件传递。我们的函数中有三个参数。

`request`是 bun 请求的对象，我们从`fetch(req)`函数中得到它。

`res`是`BunResponse`对象。在 bun 上，我们有`response`对象，为了向客户端返回响应，我们需要调用

```
return new Response('hello world', { status: 200 });
```

但是在 Express 上，我们通过调用以下命令来返回响应:

```
res.status(200).send('hello world');
```

为了实现这种语法，我创建了一个名为`BunResponse`的新类:

bun-response.js

然后，我们通过调用响应对象的默认构造函数来创建响应对象:

```
const res = new BunResponse();
```

但是问题来了，当一个用户不小心调用了 response 两次:

```
res.status(200).send();
res.status(500).send();
```

在我们的实现中，第一个响应将被忽略。这不是`ExpressJs`处理这个的方式。在 Express 上，当我们调用两次请求响应时，它会抛出一个错误来禁止这种行为。

所以在这里，我们将做同样的事情。

我用`BunResponse`对象创建了一个代理对象包装器

在这个术语中，当用户调用终止关键字`json`或`send`时，它会检查之前的`Bun Response`是否存在，如果存在，则意味着之前已经被调用过，我们抛出一个新的错误。

我们通过呼叫得到响应

```
const res = responseProxy();
```

`Chain` 中的第三个参数是`middlewares`，它是中间件的处理函数。

`Chain`函数中的第一行是将所有处理函数遍历成一个普通的回调函数，返回值为`res.isReady()`。

```
this.middlewares = middlewares.map((mid) => {        
   return () => {            
     mid.middlewareFunc(req, res, this.next);            
     return res.isReady();        
   }    
});
```

`res.isReady()`是检查用户是否发送了`stop signal`。例如，用户在中间件内部调用`res.send('return')`。在这种情况下，我们不会继续将东西传递给下一个中间件，而是返回响应。

```
this.next = () => {        
  if (this.isFinish()) {            
    return;        
  }         
  const cur = this.middlewares[this.index++];     this.isReady = cur(); if (this.isReady) {   
    return;
  }    
}
```

`next`功能类似于 Express。在我们调用`next`之后，我们将进入下一个中间件。

这里有几件事你需要知道。

1.  每一个`next`函数被调用，都会做`index++`，我们用那个索引从中间件得到特定的回调函数。
2.  停止信号。为了阻止中间件通过，我们检查是否`this.isFinish()`，这意味着索引是否等于中间件的大小。然后是`this.isReady()`，如果用户呼叫`res.send('')`。

现在，我们可以使用`fetch`函数中的中间件

在上面的代码中，我们用默认路径`/`检查所有的`middlewares`。

我们使用`Chain`作为构造函数来创建一个`chain`对象，并使用`next`来迭代中间件。

递归之后，我们检查响应是否准备好了，如果准备好了，这意味着用户已经触发了`stop signal`，我们立即返回响应。如果没有，我们检查用户是否已经完成通过中间件。

之后，我们可以像在 Express 上一样使用中间件

```
server.use((req, res, next) => { console.log('hello'); next();
});
```

接下来，我们需要找到具有特定路径的中间件。例如，如果我们在路径`/test`上添加一个中间件，我们需要在处理请求之前调用这个中间件。

我们从中间件数组的末尾开始检查`req.path`是否满足。如果`req.path`等于中间件中的`path`存储，我们将推送到一个新的数组。迭代之后，我们做和上面一样的事情来调用中间件函数。

最后一件事是处理用户逻辑，我们将它们存储在`requestMap`中。

```
if (handler) {                    
   handler.apply(null, [req, res]);                
}
```

就这么简单。

最后，我们总结一下。

让我们看看如何使用 API。这个包叫做`[bunrest](https://www.npmjs.com/package/bunrest)`，我已经在 npm 上发表了:

```
npm i bunrest
```

首先，创建一个服务器对象:

```
import App from 'bunrest'const server = App();
```

然后你可以像在快递上一样使用它:

让我们添加中间件:

让我们添加一个路由器:

最后，要启动服务器:

```
server.listen(3000, () => {
  console.log('App is listening on port 3000');
});
```

你可以在我的 [GitHub 库](https://github.com/lau1944/bunrest)中查看源代码。

感谢阅读。
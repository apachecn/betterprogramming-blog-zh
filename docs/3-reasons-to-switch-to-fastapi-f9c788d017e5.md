# 转用 FastAPI 的 3 个理由

> 原文：<https://betterprogramming.pub/3-reasons-to-switch-to-fastapi-f9c788d017e5>

## Flask vs. FastAPI

![](img/5cbae90bb7a3f0886d133b97edf5ea13.png)

照片由[沙哈达特·拉赫曼](https://unsplash.com/@hishahadat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄。

我们生活在后 Python 2 时代，因为大多数公司已经适应了 Python 3。语言越来越成熟。最近，Python 一直在采用其他语言中很酷的东西。几个例子是类型提示和`async/await`语法。

由于 Python 语言的这些变化，是时候我们讨论一下新的猛兽了: [FastAPI](https://fastapi.tiangolo.com/) 。根据我几个月来使用 FastAPI 和 Flask 的经验，让我们来看一下选择 FastAPI 比选择 Flask 的三个优点。

读完这篇文章，你会明白什么时候应该做出改变。

# 异步ˌ非同步(asynchronous)

`async/await`语法是最近在 Python 中引入的。大多数程序员认为这将加快一切。这并不完全正确，因为同步代码在大多数情况下都是可行的。`Async/await`是一种以非常方便的方式编写异步代码的方法。

就 API 编程而言，这是非常有趣的。我们看到对诸如排队、 [WebSockets](https://medium.com/better-programming/how-to-create-a-websocket-in-python-b68d65dbd549?source=friends_link&sk=556dcc527edf1d277053396332a270bd) 等异步任务的需求正在上升。与 Flask 不同，FastAPI 是在 ASGI 上实现的，允许您在本地创建异步和同步应用程序。

假设您正在编写一个检索动物图片的端点。每个请求都需要一些时间。在后台，您可能会根据文本和 HTTP 图片调用进行一些预测。大多数时候，线程都在等待某个操作完成。

在同步世界中，下面的代码片段将首先得到一张猫的图片，然后是一张狗的图片，最后是一张蝙蝠的图片。一个接一个。简单易行——我们早已熟知的编程方式。

```
get('A picture of a cat') // first
get('A picture of a dog') // second
get('A picture of a bat') // third
```

然而，想象一下，这个 API 是公开的，用户正在使用它。想象一下，每个被触发的请求需要 300 毫秒来处理。当在同步世界中同时发送 50 个请求时，最后一个请求需要 15 秒才能完成。

突然之间，你的 API 被称为慢。你可以用多线程之类的东西来扩展它，但也只能有这么多。最后，这变得非常复杂。本质上，每个执行这个任务的线程都要花费 300 毫秒等待。

异步编程提供了改进。Asyncio 允许你等待一个进程结束，同时用同一个线程执行一些其他的计算。不用再等了！50 个请求？没问题。我们一个接一个地把他们都送出去。您可以将其视为一个具有多个调用堆栈的单个线程。

只要底层基础设施能够处理它，我们就可以了。在这种极端情况下，我们甚至可以将所有请求的等待时间减少到 300 毫秒多一点。你明白了吗？如果没有，看这个[教程](https://www.youtube.com/watch?v=tSLDcRkgTsY)。

*重要警告:如果您无法处理大量请求，请确保以您能够处理的速度对传入请求进行排队。有可能编写的代码非常快，以至于会破坏整个 Kubernetes 集群。在最近的* [*在线*](https://mlconference.ai/) *机器学习大会期间，Carmine Paolino 提到，当您将应用程序投入生产时，您必须牢记这一点。确保你能处理大量同时发送的请求。*

# 类型提示

FastAPI 基于类型提示。到目前为止，Flask 只是简单地忽略了它们。类型提示给了我们很多可读性。在任何时候，我们都确切地知道我们在与什么类型的人打交道。这非常有价值。虽然这在 Java 和 TypeScript 等其他语言中是常见的做法，但对于 Python 语言来说，这是一个新的很好的补充。

这个行业正在适应。与以往相比，Python 不仅被用作快速编写脚本的语言。越来越多的团队在一起工作，笔记本被共享，比以往更多的测试被编写。现在连前端世界都在写测试(如果你是其中一员，不要觉得被冒犯)。开发被视为一门手艺，也应该如此。

今天写的大部分代码还没有达到真正的工匠想要的标准。类型提示给了我们多一个工具来朝着正确的方向前进。当与适当的变量命名相结合时，这为我们省去了许多麻烦。干得好，蟒蛇。

# 内置 OpenAPI

当你是一名程序员时，你可能对 API 很熟悉。最终，我们需要与我们的终端用户共享我们的终端，以便他们知道如何与您提供的服务进行交互。

FastAPI 内置了 OpenAPI (Swagger)和 Redoc。这是 FastAPI 在它所基于的 Starlette 上提供的一个很好的附加功能。Flask 也没有内置 OpenAPI，因此您需要一个额外的库来完成相同的任务。

每个 API 最终都需要文档。如果你正在开发的 API 没有很好的文档记录，它就没有很大的生存机会。在开发的早期拥有一个 API 是很重要的。它允许您在早期手动测试应用程序。

# 结论

在本文中，我们讨论了选择 FastAPI 而不是 Flask 的三个重要原因:

1.  异步编程
2.  类型提示
3.  内置文档

作为一个程序员，我一直认为你的框架只和你写的代码一样快。这就是为什么速度 FastAPI 在他们的[网站](https://fastapi.tiangolo.com/)上卖得很好的一件事——没有在本文中讨论。同样的道理也适用于 bug、编码速度等。对我来说，这些指标很难衡量。你很容易将苹果和橘子相提并论。

老实说，你可以用 Flask 和 FastAPI 编写速度惊人的应用程序。如果您可以使用异步编程进一步改进您的应用程序，为什么不呢？
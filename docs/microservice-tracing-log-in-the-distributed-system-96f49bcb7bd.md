# 微服务——分布式系统中的跟踪日志

> 原文：<https://betterprogramming.pub/microservice-tracing-log-in-the-distributed-system-96f49bcb7bd>

## 使用 Dotnet 示例快速找出您的微服务中的日志相关性

![](img/2fddd2569cf9b3e6d340764bb05eec5f.png)

照片由 [Marten Bjork](https://unsplash.com/@martenbjork?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

日志可以帮助开发人员很快发现系统中的问题。

我们通常使用日志来确保当系统出现问题时问题能够得到解决。

在整体架构的系统中，跟踪日志很容易。

我们要求客户端(如 app、浏览器或 API invoker)在 HTTP 头中发送`x-request-id`，并在每个日志行中记录`x-request-id`。

但是如果系统是微服务架构，事情就完全不同了。

我们不仅在每个服务中都通过了`x-request-id`，而且需要将它带到下一个服务中。

最终，我们像 ELK 一样在日志收集系统中收集日志并搜索`x-request-id`；我们可以获得整个用户操作日志序列，从 A 服务到 B 服务和 C 服务，所发生的一切都出现在你的监视器上。

服务之间有许多集成方式，如 web API、gRpc 和消息队列。

问题是怎么做？怎么做呢？

在本文中，我将演示 Dotnet 中的解决方案，并使用 NLog 包。

我将把它分成两部分来解释解决方案:一部分是接收`x-request-id`，另一部分是发送`x-request-id`。

粗略的流程是从某处获取请求 ID，并将其放入`HttpContext.TraceIdentifier`。

当我们需要发送请求时，我们从`HttpContext.TraceIdentifier`获取请求 ID，并把它放在某个地方。

为什么我说在某个地方？因为不是每一种整合方式都有表头。

# 接收请求 ID

当我们在 HTTP 头中收到 x-request-id 时，我们开始一个日志记录器范围，并将请求 id 放入`HttpContext.TraceIdentifier`。

## Web API

中间件注册的顺序非常重要。

`RequestIdMiddleware`必须在`WebApiLogginMiddleware`之前，这样日志中间件才能在日志记录器的范围内。

## gRpc

gRpc 中接收和发送请求 ID 的方式可以存在于同一个类中，所以稍后在发送请求 ID 部分会解释这一点。

## 信息排队

我们将请求 ID 放入消息的内容中，这样请求 ID 仍然可以在分布式系统中传递。

因为不是每种消息队列都有可以放置请求 ID 的头，所以我们将请求 ID 放在内容中。

注意，因为消息队列的消费者不通过 HTTP，所以方式也没有`HttpContext`。

如果您创建一个新的`HttpConext`并将其放入`IHttpContextAccessor`中，将会有所帮助。

从消息内容中获取请求 ID，将其放入新的`HttpContext`，并将值`HttpContext`赋给`IHttpContextAccessor`的实现。

# 发送请求 ID

发送请求 ID 比接收它更容易访问。

我们从`HttpContext.TraceIdentifier`获取请求 ID，并将其放入头中。

## Web API

在 web API 调用中，我们使用`DeletaingHandler`来发送请求 ID。

唯一需要注意的一点是`HttpClientXRequestIdHandler`需要在 DI 容器中注册为 Transient。

## gRpc

我们在`UnaryServerHandler`方法中从元数据接收请求 ID，并将其放入`HttpContext.TraceIdentifier`，这与 Web API 的逻辑相同。

在发送请求 ID 的过程中，我们从`HttpContext.TraceIdentifier`中获取请求 ID，并将其放入元数据的头部。

要发送请求 ID，您必须在每个 gRpc 客户端中添加`RequestIdGrpcInterceptor`。

参见`Program.cs`中的详细说明。

## 信息排队

只需从`HttpContext.TraceIdentifier`获取请求 ID，并将其放入消息内容中。

# 在日志中打印请求 ID

我们已经完成了每个服务中请求 ID 的接收和发送；下一步是在日志文件中打印请求 ID。

以下示例用于 NLog 配置。

```
<target xsi:type="File" name="info" fileName="logs/${shortdate}.info.log" layout="${longdate}|${mdlc:request}|${message}"/>
```

打印请求 ID 的关键是日志渲染器`mdlc:request`。

在你的日志目标标记中添加渲染器`${mdle:requestid}`。

注意,“request”字符串需要与您在 logger BeginScope 中编写的相同。

有关“mdlc”渲染器的更多信息，您可以参考该文档。[https://github . com/NLog/NLog/wiki/Mdlc-Layout-Renderer # net-core-logging](https://github.com/NLog/NLog/wiki/Mdlc-Layout-Renderer#net-core-logging)。

# 结论

随着服务越来越多，我们需要一个解决方案来快速确定整个系统之间的业务逻辑的操作顺序。

向整个系统发送请求 ID 有助于跟踪日志。

也可以将用户 ID 发送给整个系统；它对每个服务中的允许列表或禁止列表都有帮助。

当我们收集整个系统的日志时，我们可能包含敏感数据，如身份号码、电话号码或银行账户等。

在下一篇文章中，我将演示如何屏蔽 NLog 包中的敏感数据。

希望这篇文章能解决你的问题。
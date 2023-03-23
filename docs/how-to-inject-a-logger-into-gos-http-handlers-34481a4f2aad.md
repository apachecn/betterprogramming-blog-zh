# 如何在 Go 的 HTTP 处理程序中注入一个日志记录器

> 原文：<https://betterprogramming.pub/how-to-inject-a-logger-into-gos-http-handlers-34481a4f2aad>

## 解开一个反模式

![](img/ace341a2b59cebe04c8cef57b21b9499.png)

Ashwini Chaudhary 在 [Unsplash](https://unsplash.com/s/photos/http?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

将记录器分配给包级变量是一个[反模式](https://dave.cheney.net/2017/01/23/the-package-level-logger-anti-pattern)。当您在包名称空间中声明`var log = mylogger.New()`时，您创建了对您使用的特定记录器品牌的紧密编译时依赖。这使得你的程序变得脆弱，难以修改。

解决方案是传递一个描述兼容记录器功能的接口，但在运行时之前忽略它的身份。但是那看起来像什么？具体来说，当您想将一个日志记录器传递给一个其签名不受您控制的函数时，会是什么样子呢？

`http.HandlerFunc`是一种记录必不可少但不直观的类型:

```
type HandlerFunc(ResponseWriter, *Request)
```

面对这种签名，一个可以从`HandlerFunc`内部访问的包级日志记录器似乎是一个简单的解决方案，但是随着程序的发展，它会回来咬你。我们的任务是:将一个日志接口注入到看起来和行为都像强大的`HandlerFunc`的东西中。

# 建立自己的处理器工厂

首先，类型声明:

接口`logger`定义了我们选择注入的任何日志记录器必须具备的能力，在本例中是`Printf`。记录器的具体类型并不重要；我们只关心它能做什么。

`loggingHandlerFunc`是这个解决方案的核心——一个看起来很像`http.HandlerFunc`的类型，但是它也接受一个实现我们的`logger`接口的参数。

但是我们如何将一个传入的`*http.Request`转发给一个`loggingHandlerFunc`？这就是`loggingHandler`的作用。`loggingHandler`嵌入了一个`logger`和一个`loggingHandlerFunc`，并定义了一个熟悉的方法:

因此，我们的`loggingHandler`实现了`http.Handler`，它需要单个方法`ServeHTTP(w http.ResponseWriter, r *http.Request)`。我们现在可以将一个`*loggingHandler`传递给一个`http.ServeMux`(或者您选择的任何 mux 包)，并且到您指定的路由的流量将通过`*loggingHandler`传递给嵌入式`loggingHandlerFunc`，并在此过程中拾取一个`logger`。

然而，这种方法会导致大量的手动`*loggingHandler`创建，并且在可能的情况下，您希望在许多路线上使用同一个记录器，您会发现自己在重复构建具有相似输入的`*loggingHandler`。让我们用工厂来简化它。

`loggingHandlerFactory`接受一个`logger`并返回一个闭包，当用`loggingHandlerFunc`调用该闭包时，返回一个新的`*loggingHandler`，其中包含正确的记录器。

在路线中使用`loggingHandlerFunc`变得像将它传递到工厂一样简单。更好的是，您编写的`loggingHandlerFunc`可以嵌入到您喜欢的任何结构中，而不仅仅是这里定义的`loggingHandler`。这种配置很容易改变，并且在封装之外没有连锁效应。

这与更直接但可能会限制您改变的自由的方法形成了某种对比:

这里，`loggingHandler`只嵌入了一个记录器，普通的旧的`http.HandlerFunc`被声明为`*loggingHandler`上的方法，允许它们访问它的记录器字段。

我发现这更容易理解，但是它将你的`http.HandlerFunc`绑定到了`loggingHandler`的一个实现上。如果`loggingHandler`必须改变，它可能会影响每一个连接到它的`HandlerFunc`。与第一种方法形成对比，在第一种方法中，`loggingHandlerFunc`不关心*它的*来自哪里，只关心它作为参数被传递。它确保我们的包不仅仅是与外部世界松散耦合，它在内部也是松散耦合的。

[下面是完整的例子](https://gist.github.com/AngusGMorrison/71ddf69083d075a8afa6e6e891d25d1d)。

# 一个可敬的(？)提及

有一种观点认为你可以在`handlerFunc`的请求`Context`的`Value`中*隐藏错误事件。当您的`http.HandlerFunc`返回时，位于链顶端的那个将负责再次将它们取出，并记录一个关于请求期间发生的所有事情的统一报告。*

还有一所学校会因为我向你建议而追杀我。

第一个论点是，`Values`是一个由`interface{}: interface{}`对组成的无定形袋子，它通过再次获取数据所需的类型断言，带来了大量潜在的错误。第二，让这一包无形式的数据在请求的整个生命周期中保持不变相当于线程本地存储，这对 Go 并发模型是不利的。

然而，如果你对这种异端邪说是如何实现的感到好奇(我知道，你是在找朋友)，彼得·波尔根的 ctxdata 是值得一看的代码。

[围绕 Go 中登录的争论](https://groups.google.com/g/golang-dev/c/F3l9Iz1JX4g/m/2TpGYsTkAQAJ)很吸引人(不，真的)。我强烈建议补上它。

感谢[戴夫·切尼](https://dave.cheney.net/)和[彼得·波尔根](https://peter.bourgon.org/)帮助我理解这件事。
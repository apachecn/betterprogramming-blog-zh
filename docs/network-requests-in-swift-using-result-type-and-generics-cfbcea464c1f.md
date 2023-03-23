# Swift 中使用结果类型和类属的网络请求

> 原文：<https://betterprogramming.pub/network-requests-in-swift-using-result-type-and-generics-cfbcea464c1f>

## 在提出网络请求时，如何利用 Swift 的*结果*类型和泛型

![](img/76d75b966a0ca20363762ce037221592.png)

[安娜·格鲁](https://unsplash.com/@gruu?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/error?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

`Result`是一个`enum`，有两种情况:`success`和`failure` *，*，这两种情况都是使用泛型实现的。

`Success`可以是任何东西，但是`failure` 必须符合`Error` 协议。(可以实现自己的错误类型，也可以直接用 Swift 的`Error`。)

Swift 中`Result`类型的引入让我们有机会以更干净、更安全的方式处理异步函数的结果。

让我们看看在一个简单的网络请求例子中使用`Result`和不使用`Result`的好处，这个例子对`success`和`error`值都使用了泛型类型。

我们将从实现一个`RequestError`类型和创建一个有两个功能的`NetworkService` 类开始。

它们都负责发出 URL 请求，区别在于一个使用`Result`来处理结果(双关语)，而另一个使用普通的完成处理程序。

正如你所看到的，使用`Result`使得代码更容易阅读。

最重要的是，完成处理程序方法容易出错，因为它的`success`和`error`值都是`Optional`类型，因此，下面两个例子是有效的(就编译器而言)，这将我们带入一个奇怪的状态:

使用我们简单的网络请求实现不应该出现这种错误，但是随着代码变得更加复杂(或者被不同地使用),肯定会有出错的可能。

如果我们看一看`NetworkService`消费者使用这两个函数时，我们可以得出关于代码清晰性和安全性的类似结论。

不仅使用`Result`的函数更干净，而且如果使用完成处理器方法的函数中有一个 bug，它有可能完全打乱我们应用程序的流程。

例如，让应用程序处于停滞状态(当两个值都不存在时)，或者继续执行流程，同时显示错误警告(当两个值都存在时)。

# 具有泛型的结果类型

上面的网络请求示例只在成功的情况下支持`String`值。但是如果我们也想支持其他值类型呢？仿制药来拯救！

首先，我们需要修改`makeUrlReqeust` 函数来支持泛型类型。

然后，我们必须修改我们的客户端代码。

在闭包内使用泛型类型的`Result`要求在创建闭包时指定泛型类型，否则，编译器将抛出以下错误:

![](img/7d0399926fbdae4e57d1c9788af58a3e.png)

最后，我们需要更改`decodedData`函数，以支持解码为除了`String`以外的其他值(查看下面一个可能的实现)，瞧，现在您可以使用任何您想要的值了！

# 结论

尽管本文中使用的例子很简单，但它清楚地展示了当我们处理网络请求的响应时,`Result`类型如何使我们的生活变得更容易。

感谢您的阅读，如果您有任何意见或问题，请告诉我！
# 在 Go 中使用函数作为值的 3 种不同方式

> 原文：<https://betterprogramming.pub/3-different-ways-to-use-function-as-values-in-go-6e4bbcdba4c3>

## Go 开发模式

## 在我们的 Go 项目中加入一些功能技巧

![](img/40f315050c8aa4f92a56e3b581c03ba7.png)

Jud Mackrill 在 [Unsplash](https://unsplash.com/s/photos/programming-coffee-people?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Go 是一种静态类型的编译语言。它就像任何其他静态类型语言一样，但是没有那么麻烦，而且非常简单。当谈到函数式编程风格时，它不像 Java 那样冗长。

借助于函数像任何其他值一样是一等公民的事实，以及 Go 支持鸭子类型的事实，我们可以很容易地在其中编写函数风格的代码。

在这篇文章中，我将讨论 3 种在 Go 中编码的方法。

# 中间件

在不同的方式中，我最喜欢这一种——最流行的处理程序模式。让我们看看它的实际效果。

下面，我们正在创建处理程序来处理应用程序中的事情。该功能符合 `http.Handler`接口:

我们还创建了一些中间处理程序/中间件来处理交叉问题，如身份验证和记录其他工作:

最后，在 main 函数中，我们创建了`ultimateHandler`,它将在每个请求的日志记录和认证等所有相关工作完成后执行。为此，我们在第 5 行中链接了多个处理程序:

基本上，当我们用`http.HandlerFunc`创建一个变量时，我们在创建一个包装器。当一个函数被发送给它时，它会自己调用。

# 动态功能

这是我喜欢尽可能在代码中使用的另一种模式。它可能看起来像创建函数类或流 API 的 Java 模式。但是我们也可以用最少的代码和更低的复杂度来使用这种编码方式:

## **分析**

*   在第 8 行，我们定义了一个函数的类型(`UnaryFunction`，一个带 1 个参数的函数)，这个函数将带一个`int`，并返回另一个 int。通过定义这一点，我们可以创建任何符合其结构的其他类型，凭借 duck typing，我们可以在 Golang 中实现动态功能。
*   第 18 行和第 19 行定义了两个函数，这两个函数与第 8 行定义的函数一致，但实现方式不同。第 18 行实现了`square`方法，第 19 行实现了`cubing`。

# 通用代码

我们可以用闭包来实现用 Golang 写一些通用代码。考虑以下代码:

我们来分析一下:

*   我们有两个服务:`SubscriberDetailsService`和`SubscribeEntitlementService`，它们返回一种类型的`ServiceCallback`。
*   这些`ServiceCallbacks`可以单独处理，也可以使用负载均衡器和作业调度器，比如 [rio](https://github.com/susamn/rio) 。

在我们的例子中，服务回调可以在任何地方被调用，但是当它被调用时，它给父对象控制调用机制，并且由于它们是相同的类型(`ServiceCallback`)，它们可以按照开发人员的意愿被收集、区分优先级或序列化。

仅此而已。感谢阅读。

我在 Golang 上的另一个帖子

[](https://medium.com/geekculture/be-in-the-flow-with-idiomatic-go-development-58058326bffe) [## 在发展惯用的流程中去

### 开发类似 Go 的代码，记住一些简单的规则

medium.com](https://medium.com/geekculture/be-in-the-flow-with-idiomatic-go-development-58058326bffe)
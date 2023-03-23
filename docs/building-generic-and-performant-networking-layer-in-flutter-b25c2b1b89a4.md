# 在 Flutter 中构建通用的高性能网络层

> 原文：<https://betterprogramming.pub/building-generic-and-performant-networking-layer-in-flutter-b25c2b1b89a4>

## 实施高性能和可扩展的网络层

![](img/40323cf9b6e5a2f8dcdef945f12408e2.png)

资料来源:undraw.co

如今，很难找到不执行任何 API 调用的应用程序。也可能是你的。幸运的是，Flutter 提供了开箱即用的包，让这个过程变得简单了一些。在 pub.dev: [HTTP](https://pub.dev/packages/http) 和 [DIO](https://pub.dev/packages/dio) 上可以很容易地找到这样的例子。

本质上，这两个包都允许您组装一个网络请求，然后执行它。将错误处理、JSON 解析和移动应用程序联网的所有其他方面留给您。

例如，您的 API 调用如下所示:

这段代码对于一个小的宠物项目来说完全没问题。但是当项目在规模阶段开始增长时，你开始注意到不便之处。

具体来说:

❌重复代码

❌阻塞主线程并丢弃 FPS

❌易错代码库

❌把越来越多的时间放在增加新的服务和 API 调用上

❌不可靠的错误处理

气味难闻。对吗？

在这里，我们将解决所有这些问题，并实现高性能和可伸缩的网络层。让我们从头开始。理想情况下，我们的 API 调用应该非常简单:

相当不错。对吗？

现在，让我们借助一个神奇的软件包开始一步一步地制作我们的网络层: [Freezed](https://pub.dev/packages/freezed) (又一个用于联合/模式匹配和复制的代码生成器)！

## 步骤 0:设计我们的数据模型。让它成为容易掌握的东西

## **步骤 1:创建灵活的请求体**

请求体封装数据与 API 调用一起发送，可以包含 JSON 对象、二进制数据、文本等。

## **步骤 2:创建一个请求**

我们的请求将包含为 API 调用提供服务时可能需要的公共参数:类型(Get、Post 等)、API 路径、作为主体传递的数据、可选的查询参数和标头(如果需要，可以覆盖全局指定的标头):

## **第三步。创建响应**

我们的响应对象将只包含从您的 API 接收的原始数据:

现在我们准备实现最有趣的部分:网络服务/层本身！

## **第四步。让我们用现有的东西来构建我们的服务原型**

我们的服务需要一个基本的 URL 和可选的 DIO 实例和 HTTP 头。如果没有提供，它将创建一个默认的 DIO 实例:

为了组成网络请求体，我们需要正确地转换所提供的数据。[的力量来了**冰封工会**的](https://pub.dev/packages/freezed#unionssealed-classes)式:

你可以在这里添加其他身体类型和转换，所以我把它留给你。

一切都准备好执行我们的请求和处理异常:

我们还可以添加一种方法来简化经过身份验证的 API 调用:

**让我们总结一下这里的情况:**

*   API 调用准备好了所有的头组合和封装的参数
*   API 调用由 DIO 包执行
*   从 DIO 返回的数据被转换为您提供的模型类型
*   异常被处理并包装在相应的 API 响应中

但是所有这些事情仍然发生在主线程上。我们现在要利用 Dart 中强大而简单的[隔离机制](https://dart.dev/guides/language/concurrency)来解决这个问题。

## **第五步。使网络层具有高性能！**

Dart 工作的组织方式是在隔离区之间传递数据，我们需要复制所有参数，然后调用全局定义的函数。我们将把所有数据封装在一个私有的`PreparedNetworkRequest`类中:

现在我们需要做的就是将请求执行调用以及异常处理转移到隔离函数中:

并在我们的网络服务中调用它:

哼！我们完成了编码。

最后，我们的`NetworkService`看起来是这样的:

## **总结我们今天取得的成绩**

✅无重复代码

✅没有阻塞主线程和丢弃 FPS

✅没有防错代码库

✅将添加新服务和 API 调用的时间减到最少

✅结构化和强类型错误处理

## **资源**

*   [使用 Async/Await 的 iOS 中的 RealHTTP 现代网络层](/modern-networking-layers-in-ios-using-async-await-ab98a4b5802c)
*   [Dart 中的并发](https://dart.dev/guides/language/concurrency)
*   [冻结飞镖包](https://pub.dev/packages/freezed)
*   [迪奥镖包](https://pub.dev/packages/dio)
*   [这里有完整的源代码](https://gist.github.com/dev4jam/ce094af46be6150cd0d20322e37dd958)

```
**Want to Connect?**To get hand-picked the latest tech stories subscribe on my [Telegram Channel](https://t.me/itjedidev) where I post daily.
```
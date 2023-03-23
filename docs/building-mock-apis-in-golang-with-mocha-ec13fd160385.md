# 用 Mocha 在 Golang 中构建模拟 API

> 原文：<https://betterprogramming.pub/building-mock-apis-in-golang-with-mocha-ec13fd160385>

## 一个用于存根和模仿 API 的新库

![](img/9525e86cebf3c521f6f004fb6b4388c4.png)

照片由[替代代码](https://unsplash.com/@altumcode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

开发者们好！最近，我发布了 [Mocha](https://github.com/vitorsalgado/mocha) ，这是一个用 Go 编写的库，用来构建模拟 API，灵感来自 Java 世界的 [WireMock](https://github.com/wiremock/wiremock) 。本文将对这个新项目进行简要概述。

处理依赖于外部资源(如 API)的代码是很常见的，在这些情况下编写有价值的测试可能具有挑战性。有一些方法可以做到这一点，比如我们可以模仿我们的 API 客户端，使用定制的`http.Transport`，使用真实的服务器，比如具有定制行为的`httptest.Server` *、*。

我喜欢使用实际的 HTTP 服务器的想法，比如来自 Go 标准库的`httptest.Server`,因为我们可以在受控的测试环境中模拟真实的集成。使用真实服务器的另一个优点是我们不会被任何特定的客户端实现所束缚。但是对于中型/大型项目，使用基本的服务器实现来设置适当的请求断言和响应存根可能是一个问题。这就是摩卡能帮上忙的地方。 *Mocha* 利用了`httptest.Server`，并使得为特定的请求匹配创建响应存根变得更加容易。

# 该项目

Mocha 是一个在 Golang 中构建模拟 API 的开源工具，其灵感来自 Java 的 [WireMock](https://github.com/wiremock/wiremock) 。Mocha 的工作原理是运行一个实际的 HTTP 服务器，并公开一个 fluent API，您可以轻松地为特定请求配置响应存根。

## 装置

```
go get github.com/vitorsalgado/mocha/v3
```

需要 **Go 1.18+** 。

## 使用

尽管 Mocha 可以在不同的场景中使用，但它的主要目的是用于 Go 测试。在典型的使用中，您可能需要导入这三个包:

*   `github.com/vitorsalgado/mocha/v3`为核心部件
*   `github.com/vitorsalgado/mocha/v3/expect`实现匹配功能
*   `github.com/vitorsalgado/mocha/v3/reply`实现响应存根构建功能

用法通常如下例所示:

在上面的例子中，使用 *Mocha 创建了一个真实的模拟服务器，并且每次这个 API 接收到一个 HTTP 请求时都使用下面的配置:*

```
*GET /customers/super-id
accept: application/json
content-type: application/json*
```

它将回复:

```
*200 OK 
{id:”super-id”, name:”nice-name”}*
```

我们正在测试的处理程序使用一个需要基本 URL 的 API 客户机。我们需要将这个基本 URL 改为指向我们的模拟 API。还可以断言模拟服务器是否收到了任何调用或特定数量的调用。

## 配置

Mochas 的函数接受第二个“可选”参数来定制实例。

```
m := mocha.New(t, Configure().Addr("127.0.0.1:3000").Build())
```

## 延迟响应

Mocha 可以通过添加延迟来模拟缓慢的响应。您可以指定 Mocha 在提供 HTTP 响应之前应该等待的时间。请参见下面的示例:

```
m.AddMocks(mocha.Get(expect.URLPath("/test")).
  Reply(reply.
    Accepted().
    Delay(5 * time.Second)))
```

## 回复

Mocha 提供了不同的方法来构建响应，包括使用定制函数来构建响应序列、随机响应和模板。
下面你可以看到一些回复的例子:

还有其他功能可用；有关更多详细信息，请查看项目文档和源代码:

[](https://github.com/vitorsalgado/mocha) [## GitHub-vitorsalgado/mocha:Golang 的 HTTP 模仿和期望工具

### 围棋 HTTP 服务器模拟工具。Mocha 创建一个 HTTP 服务器，并让您配置…

github.com](https://github.com/vitorsalgado/mocha)
# Swift 5 中高度可定制的网络层

> 原文：<https://betterprogramming.pub/highly-customizable-network-layer-in-swift-5-1e5c1e163674>

## 准备一个 API 请求，调用一个 API，并解析响应

![](img/8b7327f28f8ad54ab894721941e065ff.png)

由[约书亚·索蒂诺](https://unsplash.com/@sortino?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/network-coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

本文主要关注网络层的简单性、可定制性、**、**和模块化。我的方法已经在许多项目中得到验证和测试，我一直在更新我的网络层，以包含我在开发过程中面临的问题。

你可以从 [Github](https://github.com/waseemwk22/NetworkLayerSwift) 下载完整的源代码。

# 金科玉律

在您开始阅读本文之前:

1.  视图控制器不应该知道网络 API。他们甚至不应该关心数据是来自 API 还是本地数据库，例如 Coredata 或 SQLite。
2.  唯一应该调用网络 API 的层是服务层。
3.  视图控制器应该请求服务提供数据。
4.  服务不应该知道 API 服务器以及发送到 API 服务器或从 API 服务器接收的数据的格式。
5.  只有网络层应该知道这些细节。

这篇文章将涵盖的内容:

1.  准备一个 API 请求:**<API 请求>**
2.  **调用一个 API:**
3.  **解析响应:*<* 可编码 *>***

# **1.准备请求**

**每个网络请求都由某些属性标识:**

1.  ****端点:**网络 API 的路径**
2.  **HTTP 方法类型:GET、POST、PUT、DELETE、PATCH 等。**
3.  ****请求参数:** JSON 或字符串**
4.  **响应类型:JSON、XML 或字符串**

## ****终点****

**让我们以一个通过用户名`get-stories-by-username`返回故事的 API 为例**

```
// GET All stories by usernamehttps:www.medium.com/api/v1.0/stories/waseem_khan// server-url/api-path/api-version/resource-name/end-point// server-url    -> https:www.medium.com/
// api-path      -> api/
// api-version   -> v1.0/
// resource-name -> stories/
// end-point     -> waseem_khan
```

**大多数时候，任何 API 的 URL 都是我提到的格式。通常情况下，`server-url`保持不变，但是其余的属性会随着 API 的变化而变化。我们的网络层应该以支持每个 API 请求的定制的方式来编写。**

**让我们将网络请求封装在一个 Swift 类中。**

**ApiRequest**

**`**ApiRequest**`类是我们项目中所有 API 请求的父类，即我们将为每个 API 创建一个单独的 Swift 类，例如`**LoginApiRequest**`、`**SignupApiRequest**`、`**GetStoriesApiRequest**`、**、*。通过覆盖父类中所需的方法，可以完全定制每个请求。*****

## ****HTTP 方法类型****

**每个 API 可以是以下类型之一:GET、POST、PUT、DELETE、PATCH *。* `**HTTPMethod**` enum 代表 API 请求的类型。**

## ****请求参数****

**根据 API 的类型，您可能需要在主体中设置请求参数。例如，`LoginRequest`可以期待下面格式的请求参数。**

```
{
  "email": "waseem.khan@medium.com",
  "password": "mission#impossible"
}
```

**这些参数可以通过从基类`APIRequest`覆盖`LoginRequest` 中的函数`bodyParams()`来提供。**

## **响应类型**

**在`ApiRequest`**`<ResponseType: Codable>`***，*** 类属*`ResponseType`*类型的`Codable`是提供`Codable`模型对象来映射 API 返回的响应。我们可以在这里传递任何实现`Codable`接口的对象。`Array`、`String`、`Double`等数据类型，已经符合本协议。我们可以直接将来自 API 的响应映射到我们的一个模型类。例如，`get-stories-by-username` API 以下面的格式返回数据。我们关心钥匙`data`的反应。******

****对上述 API 的响应可以直接映射到故事数组，即`[Story]`。****

****为什么我们的模型类`Story`要实现`Codable`？****

```
**// A type that can convert itself into and out of an external representation.typealias Codable = [Decodable](https://developer.apple.com/documentation/swift/decodable) & [Encodable](https://developer.apple.com/documentation/swift/encodable)**
```

****我们将把 API get-all-stories 的 JSON 响应映射到`Story` *。*因此`Story`应该实现`Codable`来支持编码和解码。****

## ****让我们创建第一个网络 API 请求****

****让我们创建一个 API，从 medium.com 获取特定用户的故事。这个 API 期望请求体中有记录限制和页码(假设启用了分页)。****

****您将看到我们只覆盖了这个 API 的可配置属性。我们可以覆盖`apiPath()`*`apiVersion()`等。，如果需要。我们还提到这个 API 将返回一组故事`[Story]`。*****

# *****2.调用 API*****

*****我们已经创建了一个网络请求。现在让我们看看如何实际调用网络 API。我们将使用`Alamofire`与 API 服务器通信。让我们创建`NetworkApiClient`，它将负责与 API 服务器的连接。*****

# *****3.解析响应*****

*****到目前为止我们有什么？*****

1.  *****一个网络请求，即`get-stories-by-username`*****
2.  ****一个*`NetworkAPIClient`*向 API 服务器发送请求******

*****现在让我们看看如何解析从 API 返回的响应。*****

*****返回到`NetworkApiClient`并提供函数`successResponse()`的实现。*****

*****第 1 步:解析对 JSON 对象的响应(使用`SwiftyJson` )
第 2 步:实际的数据/结果在`data`键中，如上面通用 API 响应中所讨论的。第三步:这是实际的交易。*****

*****我们已经提供了 API 所期望的模型类的类型。在请求`get-stories-by-username`**的情况下，提到的类型是`[Story]`，即一个故事列表。`decodedValue`为`[Story]`类型。*******

# *******检验一切*******

*******没有实践的知识是无用的；没有知识的实践是危险的。*******

*******到目前为止我们有什么？*******

1.  *******一个网络请求，即`*get-all-stories-by-username*`*******
2.  ******网络 API 客户端:
    >向 API 服务器发送请求
    >解析 API 服务返回的响应******

******现在我们将看到如何使用上述知识来调用 API 并使用结果。******

******我们需要创建一个调用`get-stories-by-username` API 的`StoryService`类。`StoryService`负责所有与`Story`相关的 CRUD 操作。******

******`apiResponse.data`，在这里是一个故事的数组，也就是`[Story]`。我们可以使用这个列表来填充视图控制器中的`UITableView`数据。******

******我希望一切都很清楚。如有任何困惑，请留言。我很乐意帮忙。欢迎建议。******
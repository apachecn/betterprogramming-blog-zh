# 如何通过 React 以优化和可扩展的方式使用 Axios

> 原文：<https://betterprogramming.pub/how-to-use-axios-in-an-optimized-and-scalable-way-with-react-c56fa632e088>

## 实例、动词、超时等等

![](img/2b05fe7b2ca2912723feb52b67845a06.png)

Marc-Olivier Jodoin 在 [Unsplash](https://unsplash.com/s/photos/network?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我们可以看到如何在 React 应用程序中以优化和可扩展的方式使用 [Axios](https://axios-http.com/) 。
在构建基于 API 的 React 应用时，HTTP 客户端是需要添加到架构中的核心服务。React 有很多 HTTP 客户端库。在决定选择哪个的时候，Axios 可能是大多数开发者的首选。

Axios 是一个 HTTP 客户端库，具有许多优势特性。可以在浏览器和 [Node.js](https://nodejs.org/en/) 中使用。在本文中，我们将了解如何以可扩展和优化的方式利用所有高级 Axios 功能。

# Axios 实例

创建一个 Axios 实例对于一个大规模的应用程序更重要，因为所有的基本配置都在一个文件中。对基本配置的更改可以在单个文件中轻松完成，并且会在使用 Axios 实例的任何地方反映出来。查看下面的代码片段

基于上面的代码，我们已经使用默认配置配置了所有的基本设置，这将应用于每个使用`axiosClient`实例的请求，如下面的请求所示。

# 中轴动词

我们可以在基本配置文件中对 Axios HTTP 动词进行分组，如`GET`、`POST`、`DELETE`和`PATCH`，如下所示。

现在，我们可以在任何需要发出 API 请求的地方直接导入自定义函数，如下面的代码所示。

# 超时

Axios 超时选项允许以毫秒为单位设置请求超时。在 Axios 中配置超时很容易。正如我们之前看到的，它可以在基本配置本身中定义。

```
timeout: 2000 // Request will be timeout after 2 seconds.
```

# **拦截请求**

使用请求拦截，您可以在发送之前编写或执行请求。查看下面的代码片段。

这里，我们在发出请求之前添加了`contentType`头。默认情况下，请求拦截器是异步的，这可能会导致 Axios 请求执行的一些延迟。为了避免这种情况，我们使用了`synchronous: true`。

# 拦截响应

使用请求拦截，您可以在响应到达`then`之前编写或执行。响应拦截器可用于在令牌到期(401 状态)时注销用户，或者您可以刷新令牌并再次发出失败的请求，以使用户停留在同一页面，从而获得良好的 UX。

# 上传进度

您可以使用`onUploadProgress`选项非常容易地在 Axios 中跟踪上传进度。`onUploadProgress`允许处理上传的进度事件。这可用于向用户实时显示上传百分比，向他们确认上传正在进行中。查看下面的代码。

# 资源

[Axios GitHub 库](https://github.com/axios/axios)

# 结论

Axios 是 React 及其社区的一个很好的 HTTP 客户端包。我希望你已经发现这是有用的。感谢您的阅读。
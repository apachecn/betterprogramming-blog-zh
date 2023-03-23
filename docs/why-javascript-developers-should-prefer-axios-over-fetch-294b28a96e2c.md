# 为什么 JavaScript 开发人员更喜欢 Axios 而不是 Fetch

> 原文：<https://betterprogramming.pub/why-javascript-developers-should-prefer-axios-over-fetch-294b28a96e2c>

## 比较

## 向后兼容、监控上传进度等等

![](img/7524f1f09acd4dff24d48c092b44eeb8.png)

照片由 [Brixiv](https://www.pexels.com/@zante?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 从 [Pexels](https://www.pexels.com/photo/field-summer-animal-dog-4459859/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

在我的上一篇文章“深入了解 JavaScript 的 Fetch API ”中，我讨论了 Fetch API 的基础知识。但是值得承认的是,`fetch()`并不总是一个理想的解决方案，有时有更好的替代方法来发出 HTTP 请求。这里我来描述一下为什么 Axios 在开发上比`fetch()`好。这是我的第 36 篇中型文章。

# 概述和语法

## 取得

`Fetch()`是获取 API 中 JavaScript 窗口对象方法的一部分。它是内置的，所以用户不必安装它。`Fetch()`允许我们从 API 异步获取数据，而无需安装任何额外的库。

上面这段代码是一个简单的`fetch()` get 请求。在`fetch()`方法中，有一个强制参数，即`url`。`url`是用户希望从中获取数据的路径。然后`fetch()`方法返回一个承诺，可以解析响应对象或者用错误拒绝它。

`fetch()`方法中的第二个参数是选项，它们是可选的。如果用户不传递选项，请求总是得到，并从给定的 URL 下载内容。正如我之前提到的，promise 返回响应对象，正因为如此，用户需要使用另一种方法来获得响应的主体。根据正文的格式，用户可以使用几种不同的方法。

*   `response.json()`
*   `response.text()`
*   `response.blob()`
*   `response.formData()`
*   `response.arrayBuffer()`

最受欢迎的是`response.json()`。

可惜内置的`fetch()`函数不在 Node.js 里，但是有一个类似[的 polyfill，node-fetch](http://npmjs.com/package/node-fetch) 。在 node-fetch 和浏览器`fetch()`之间，存在几个已知的[变体](https://github.com/node-fetch/node-fetch/blob/master/docs/v3-LIMITS.md)。

## Axios

Axios 是一个 JavaScript 库，用于从 Node 或 XMLHttpRequest 或浏览器发出 HTTP 请求。作为一个现代化的库，它基于 Promise API。Axios 有一些优势，比如防止跨站点请求伪造(CSFR)攻击。为了能够使用 Axios 库，用户必须使用 CDN、npm、Yarn 或 Bower 安装它并将其导入到您的项目中。

上面这段代码是一个 get 方法和一个简单的响应和错误回调。当用户创建一个配置对象时，他们可以定义一堆属性。最常见的有`url`、`baseURL`、`params`、`auth`、`headers`、`responseType`、`data`。

作为响应，Axios 返回一个承诺，该承诺将通过响应对象或错误对象来解决。在响应对象中，有以下值:

*   `data` : 实际响应体
*   `status`:呼叫的 HTTP 状态码，如`200`或`404`
*   `statusText` : HTTP 状态为文本消息
*   `headers`:与请求中相同
*   `config`:请求配置
*   `request` : XMLHttpRequest (XHR)对象

用户需要使用`fetch()`中的两个承诺。用户可以避免样板文件，在 Axios 中编写更干净、更简洁的代码。

Axios 使用`data`属性，但是`fetch()`使用`body`属性来处理数据。`fetch()`的`data`已经字符串化。在`fetch()`中，URL 作为参数传递，但是在 Axios 中，URL 是在 config 对象中设置的。

# JSON

## 取得

使用`fetch()`方法，用户需要对响应数据使用某种方法。当用户发送请求正文时，用户需要对数据进行字符串化。

在上面这段代码中，有了响应，用户需要处理`response.json()`动作。在`fetch()`中处理 JSON 数据时，有一个两步的过程。用户需要首先发出实际的请求，然后在响应上调用`.json()`方法。

## Axios

在 Axios 中，用户在请求中传递数据或从响应中获取数据，并且数据被自动字符串化。因此，不需要其他操作。

在上面的例子中，你可以看到你只需要一个`then`。

数据的自动转换是 Axios 的一个很好的特性。

# 错误处理

## 取得

每次从`fetch()`方法得到响应时，您都需要检查状态是否为成功，因为即使不是，您也会得到响应。在`fetch()`的情况下，当且仅当请求无法完成时，承诺才会被解决。

`Fetch()`不抛出网络错误。因此，在使用`fetch()`时，您必须始终检查`response.ok`属性。您可以将这种错误检查提取到一个函数中，以使它更容易和更可重用。

## Axios

在 Axios 中，处理错误非常容易，因为 Axios 会抛出网络错误。如果会出现类似`404`的不良反应，承诺会被拒绝，会返回错误。因此，您需要捕捉一个错误，并且可以检查它是什么类型的错误。

# 下载进度

加载大型资产时，进度指示器对于网速较慢的用户非常有用。在以前实施的进度指标中。开发人员使用`XMLHttpRequest.onprogress`作为回调处理程序。

## 取得

要在`fetch()`中跟踪下载的进度，您可以使用`response.body`属性之一，一个`ReadableStream`对象。它逐块提供主体数据，并允许您及时计算消耗了多少数据。

上面的例子演示了使用`ReadableStream`在下载图像时向用户提供即时反馈。

## Axios

在 Axios 中，实现进度指示器也是可能的，而且更容易，因为有一个现成的模块可以安装和实现。叫做 [Axios 进度条](https://github.com/rikmms/progress-bar-4-axios/)。

# 上传进度

## 取得

在`fetch()`中，你不能监控你的上传进度。

## Axios

在 Axios 中，您可以监控上传进度。如果你正在开发一个视频或照片上传的应用程序，这可能会成为一个障碍。

# HTTP 拦截

当您需要检查或更改从应用程序到服务器或从服务器到应用程序的 HTTP 请求时(例如，身份验证、日志记录等)，拦截可能非常重要。

## 取得

`Fetch()`默认不提供 HTTP 拦截。有可能覆盖`fetch()`方法，并定义在发送请求期间需要发生什么，但这将需要更多的代码，并且可能比使用 Axios 的功能更复杂。您可以覆盖全局`fetch()`方法并定义自己的拦截器，如以下代码所示:

## Axios

Axios HTTP 拦截是这个库的关键特性之一——这就是为什么您不需要创建额外的代码来使用它。

在上面的代码中，`axios.interceptors.request.use()`和`axios.interceptors.response.use()`方法用于定义在发送 HTTP 请求之前要运行的代码。

# 响应超时

## 取得

`Fetch()`通过`AbortController`接口提供响应超时功能。

在上面的代码中，使用`AbortController.AbortController()`构造函数，您需要创建一个`AbortController`对象。`AbortController`对象允许您稍后中止请求。正如我在上一篇文章“深入了解 JavaScript 的 Fetch API ”中提到的，我们讨论了`signal`如何成为只读的`AbortController`的属性。`signal`提供了一种与请求通信或中止请求的方式。如果服务器在五秒钟内没有响应，则通过调用`controller.abort()`终止操作。

## Axios

通过使用 config 对象中可选的 timeout 属性，可以设置请求终止前的毫秒数。

JavaScript 开发人员选择 Axios 而不是`fetch()`的原因之一是设置超时的便利性。

# 并发请求

## 取得

要同时发出多个请求，可以使用内置的`Promise.all()`方法。只需将一组`fetch()`请求传递给`Promise.all()`，然后传递一个`async`函数来处理响应。

## Axios

您可以使用 Axios 提供的`axios.all()`方法来实现上述结果。将所有获取请求作为数组传递给`axios.all()`方法。使用`axios.spread()`函数将响应数组的属性分配给单独的变量，如下所示:

# 向后兼容

向后兼容也称为浏览器支持。

## 取得

`Fetch()`仅支持 Chrome 42+，Safari 10.1+，Firefox 39+，Edge 14+。完整的兼容表可在“[我可以使用](https://caniuse.com/?search=Fetch)吗？”为了在不支持`Fetch()`的 web 浏览器上实现类似于`fetch()`的特性，您可以将`fetch()`与类似`[windows.fetch ()](https://github.com/github/fetch)`的 polyfill 一起使用。

要使用获取聚合填充，请通过以下 npm 命令进行安装:

```
npm install whatwg-fetch --save
```

如果出于某种原因需要访问 polyfill 实现，可以通过 exports:

请记住，在一些旧浏览器中，您可能还需要 promise polyfill。

## Axios

Axios 不像`fetch()`。Axios 提供了广泛的浏览器支持。甚至像 IE11 这样的老浏览器也可以毫无问题地运行 Axios。Axios 的[文档](https://github.com/axios/axios#browser-support)提供了完整的兼容性表。

# 结论

对于大多数 HTTP 通信需求，Axios 在一个紧凑的包中提供了一个易于使用的 API。

HTTP 通信有一些替代库，比如 [ky](https://github.com/sindresorhus/ky) ，一个基于 window.fetch 的小巧优雅的 HTTP 客户端；superagent ，一个基于 XMLHttpRequest 的小型渐进式客户端 HTTP 请求库。

但是对于有大量 HTTP 请求的应用程序和那些需要良好的错误处理或 HTTP 拦截的应用程序，Axios 是一个更好的解决方案。

对于只有几个简单 API 调用的小型项目来说，`fetch()`可能是一个很好的解决方案。
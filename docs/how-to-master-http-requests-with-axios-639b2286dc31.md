# 如何用 Axios 掌握 HTTP 请求

> 原文：<https://betterprogramming.pub/how-to-master-http-requests-with-axios-639b2286dc31>

## 了解如何在 HTTP 请求中使用基于 JavaScript 的库

![](img/b41745bf53fdb85e4ed8a956d14c92ed.png)

作者照片。

Axios 是 JavaScript 开发者最喜欢的 HTTP 客户端之一。它用于从客户端和服务器端发送 HTTP 请求，尤其是 AJAX 请求。

在开发动态 web 应用程序时，从 JavaScript 端发送 HTTP 请求几乎是必不可少的。Axios 通过在 JavaScript 的`XMLHttpRequest`接口上提供一个易于使用的抽象来简化这项任务，提供了许多用户友好的功能和配置选项。

在本教程中，我们将带着代码示例和简化的解释，向您介绍使用 Axios 发送 HTTP 请求所需要了解的一切。

让我们以一个问题开始我们的介绍。

# 为什么要选择 Axios？

要回答这个问题，让我们来看看可供开发人员使用的 Axios 替代方案。

JavaScript 提供了一个内置接口`XMLHttpRequest`来处理 HTTP 请求。然而，用这个接口发送请求并不简单，需要编写太多行代码。

如果你用过 jQuery，你一定很熟悉`$_ajax`函数。它在`XMLHttpRequest`接口上提供了一个更简单易用的抽象，我们可以用更少的代码行来发送 HTTP 请求。但是随着像 jQuery 这样的库在过去几年中变得过时，开发人员需要一个本地 JavaScript 解决方案来创建 HTTP 请求。

现在，Fetch API 和 Axios 是用于发送这些请求的本地 JavaScript 中最流行的两种解决方案。然而，与 Fetch API 相比，Axios 有一个优势，因为它为开发人员提供了一些独特的功能。以下是其中的几个例子:

*   支持请求和响应拦截
*   取消请求的能力
*   支持旧浏览器(Internet Explorer 11)
*   JSON 数据的自动转换
*   XSRF 保护的客户端支持

由于其强大的功能集，开发人员现在开始倾向于使用 Axios 而不是 Fetch API 来发送 HTTP 请求。

# 安装 Axios

您可以在前端和后端安装和使用 Axios。如果您使用的是像 npm 这样的软件包管理器，安装会很简单:

```
npm install axios
```

如果您使用内容交付网络(CDN ),请在 HTML 中嵌入 Axios 脚本:

```
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
```

或者:

```
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

# 使用 Axios 发送 HTTP 请求

使用 Axios 发送 HTTP 请求就像将包含所有配置选项和数据的对象传递给`axios()`函数一样简单:

让我们仔细看看这里使用的配置选项:

*   `method`:必须发送请求的 HTTP 方法。
*   `url`:请求必须发送到的服务器的 URL。
*   `data`:在 POST、PUT 和 PATCH 请求的情况下，使用该选项提供的数据在 HTTP 请求的主体中发送。

要查看 Axios 请求功能可用的所有配置选项，请参考其[官方文档](https://www.npmjs.com/package/axios#creating-an-instance)。

# 专用请求功能

除了通用的`axios()`功能，Axios 还提供了专用功能来简化发送不同类型的请求。以下是这些功能的列表:

例如，我们可以通过将方法设置为“post”来使用`axios.post()`函数发送 POST 请求，而不是使用`axios()`函数

```
axios.post("/users", {
    username: "sam123",
    firstname: "sam",
    lastname: "smith"
});
```

我们可以使用`axios.get()`函数以类似的方式发送 GET 请求:

```
axios.get("/users", {
    params: {
        firstname: "sam"
    }
});
```

`params`配置选项用于设置 URL 中的查询参数。

# 发送多个并发请求

我们可以使用 Axios 并发发送多个请求。为此，我们必须将一组请求调用传递给`Promise.all()`函数:

```
Promise.all([
    axios.get("/users/sam123"),
    axios.get("/users/sam123/comments")
]);
```

# 用 Axios 处理响应

当我们向远程服务器发送 HTTP 请求时，我们会收到来自该服务器的包含特定信息的响应。我们可以使用 Axios 检索这个响应。

根据 Axios 的文档，返回的响应包含以下信息:

因为 Axios 是基于承诺的，所以响应作为承诺返回。所以我们需要使用`then()`和`catch()`函数来检索响应并捕捉错误(如果有的话)。

让我们看看如何处理 POST 请求返回的响应:

# 使用异步/等待

我们还可以使用`async/await`发送 HTTP 请求并处理响应，而不是承诺:

# 处理来自并发请求的响应

在上一节中，我们使用了`Promise.all()`方法来发送多个并发请求。当传递给该方法的所有请求都完成时，它返回一个带有每个请求的响应对象的承诺。我们可以使用传递给方法的数组中每个请求的索引来分隔响应:

# 处理错误

如果在使用 Axios 发送 HTTP 请求时发生错误，返回的 error 对象包含特定的信息来帮助我们找出错误发生的确切位置。正如您将在下面的示例中看到的，有三个地方可能会出现错误:

有了 Axios 提供的这些信息，您可以根据错误发生的位置适当地处理错误。

# 用 POST 请求发送数据

当使用 Axios 发送 POST 请求(还有 PUT 和 PATCH 请求)时，请注意我们是如何将普通的 JavaScript 对象作为数据传递的。默认情况下，Axios 会将这些 JavaScript 数据转换成 JSON。它还将`content-type`报头设置为`application/json`。

但是，如果将序列化的 JSON 对象作为数据传递，Axios 会将内容类型视为“application/x-www-form-urlencoded”(表单编码的请求体)。如果想要的内容类型是 JSON，您必须使用`headers`配置选项手动设置头。

```
const data = JSON.stringify({name: "sam"});
const options = {
    headers: {"content-type": "application/json"}
}axios.post("/users", data, options);
```

如果您想发送除 JSON 之外的任何其他类型的数据，您必须显式地转换数据并适当地设置`content-type`头。Axios 提供了两个配置选项(`transformRequest`和`transformResponse`)，分别用于在发送请求和接收响应之前进行转换。

# 转换数据

让我们看看如何在发送请求时使用这两个配置选项:

# 为请求设置自定义标题

您可以轻松地为使用 Axios 发送的请求设置自定义标题。您只需将包含自定义标头的对象传递给正在使用的请求方法:

```
const options = {
    headers: {"X-Custom-Header": "value"}
}axios.get("users/sam123", options);
```

# 设置配置默认值

您可以设置默认配置选项，以便将它们应用于您使用 Axios 发送的每个请求。这样，您就不必重复设置所有请求都通用的选项。

例如，如果您想设置用于每个请求的基本 URL 或授权头，可以很容易地实现:

```
axios.defaults.baseURL = "https://example.com";
axios.defaults.headers.common["Authorization"] = AUTH_TOKEN
```

## 设置实例的配置默认值

有时，由于我们发送的请求的差异，为每个 HTTP 请求设置默认配置变得不现实。根据用户权限，我们可以向两个不同的 API 或不同的授权令牌发送请求。

在这种情况下，虽然我们不能找到每个请求共有的配置选项，但是我们也许能够找到不同请求组共有的选项。Axios 为我们提供了一种分别为这些不同的组设置默认配置的方法:通过创建实例。

我们可以创建单独的实例，并为每个实例设置默认配置。然后，我们可以使用这个实例对象代替 Axios 对象来发送请求:

# 拦截请求

HTTP 请求拦截是 Axios 中最有趣的特性之一。它允许你拦截程序发送和接收的请求，并在操作完成之前执行一些常见的任务。这个特性简化了与 HTTP 请求相关的后台任务的处理，比如日志记录、身份验证和授权。

为请求或响应定义拦截器是一项简单的任务:

这里，`config`对象就是我们传递给 Axios 函数的确切的`config`对象或者它的一个别名。因此拦截器可以完全访问请求配置及其数据。

设置请求拦截器后，每次程序发送新请求时，消息“请求已发送”都会记录到控制台。

我们可以用类似的方式设置响应拦截器:

现在，每次收到响应时，消息“Response received”都会记录到控制台。

我们也可以为实例而不是 Axios 对象定义拦截器:

# 取消请求

Axios 提供的另一个有趣的特性是我们可以随时取消请求。

在 web 开发的某些情况下，我们可能会发现远程服务器对我们请求的响应不再重要。在这种情况下，我们可以通过简单地取消请求来节省系统资源。Axios 为我们提供了实现这一目标的方法。

如果您想取消某个请求，它应该有一个在创建请求时创建的`cancel`令牌。我们可以使用这个令牌随时取消请求。

取消过程是这样实现的:

值得注意的是，我们可以使用一个`source`对象同时取消几个请求。当调用`source.cancel`时，其`cancel`令牌是使用给定源创建的所有请求都被取消。

# 防范 XSRF 攻击

跨站点请求伪造(XSRF)是攻击者用来渗透 web 应用程序并执行恶意任务的一种技术。在这些攻击中，攻击者伪装成受信任的用户，欺骗应用程序执行对他们有利的操作。

这可能会危及用户的隐私和安全，如果用户拥有高级权限，甚至会导致攻击者获得整个系统的访问权限。

Axios 通过在创建请求时嵌入额外的身份验证信息，提供了一种防止此类攻击的方法。它是这样实现的:

```
const options = {
  xsrfCookieName: 'XSRF-TOKEN',
  xsrfHeaderName: 'X-XSRF-TOKEN',
};axios.post("/users", options);
```

# 摘要

在本教程中，我们讨论了作为 web 开发人员使用 Axios 发送 HTTP 请求时需要了解的一切。正如您已经看到的，Axios 使处理 HTTP 请求和响应的任务变得非常简单。

它提供了许多配置选项来根据我们的需要改变默认行为。它还提供了许多方法来简化发送 HTTP 请求的任务。在 Axios 的帮助下，您可以以超乎想象的速度在应用程序的后端和前端实现 HTTP 请求发送特性。

因此，如果您还没有，请将 Axios 添加到您的 JavaScript 库组中，并使用它轻松实现您的 HTTP 请求发送任务。
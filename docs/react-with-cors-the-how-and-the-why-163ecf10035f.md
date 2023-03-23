# 与 CORS 反应:如何和为什么

> 原文：<https://betterprogramming.pub/react-with-cors-the-how-and-the-why-163ecf10035f>

## 向不同域中的服务器发出请求

![](img/5dfaef57dc9e741d0988dbc829d1e87c.png)

金属齿轮连接——图片由[比尔·牛津](https://unsplash.com/@bill_oxford?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/connection?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。

像许多其他人一样，我最近开始建造一些东西。我决定创建一个网络应用程序，来解决我已经感觉了很长一段时间的痒。另外，我想增加我对 React 和一些核心 Ruby 库的了解。因此，我将它实现为一个 React SPA，服务器是一个机架应用程序。我没有使用任何 Ruby 框架，只是使用了小型的非常专业的库。我决定写下我学到的东西，以增加我的学习经验，并让其他人加入到这种经验中来。

这第一篇文章是关于跨原产地资源共享(CORS)。

# 什么是 CORS？

CORS 是一种允许您向不同域的服务器发出 ajax 请求的技术。如果你想直接在你的客户端上使用 API，这是非常有用的——如果你正在编写一个 [Jamstack](https://jamstack.org/) web 应用，这是绝对需要的。

但是这在其他用例中也是有用的。如果您将前端放在不同于后端域的域中，您需要让后端对前端可用，这正是我的用例。

让我们来探索为什么和如何！

# CORS 是如何在幕后工作的？

我们首先要考虑为什么这是必要的。每个浏览器都有一个叫做“同源策略”的安全机制这种机制确保了潜在的恶意脚本无法执行对随机域的随机 API 调用。

## 什么叫做同源？

根据定义，`protocol`、`port`和`host`必须相等才能被认为是同一个原点。这清楚地表明，您可以使用任何查询参数调用服务器上的任何路由，但是您不能调用另一个子域，因为子域是不同的主机。

所以`https://my-awesome-app.com`无法访问`https://api.my-awesome-app.com`是因为主机不同，但是`https://my-awesome-app.com/api/v1/...`任何路由都是可用的。

我们如何为我们的服务器/客户端停用同源策略？

我们必须设置一些 HTTP 头来告诉客户端一些资源可供他们使用。相关标题包括:

*   `[Access-Control-Allow-Origin](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)`，必须设置为请求资源的域。根据我们上面的例子，这可以设置为`Access-Control-Allow-Origin: [https://my-awesome-app.com](https://my-awesome-app.com)`。
*   其他一些`Access-Control-*`头可以限制一些资源或者设置一些权限参数，进一步增加服务器的安全性。

浏览器通过向服务器发送飞行前 HTTP OPTIONS 请求来确保它可以访问服务器，并因此请求报头。完成请求后，当服务器通过发送所需的头批准请求时，浏览器将发送实际的请求。

# 你如何使用 CORS 和 React？

在 React 应用程序中有两个常用的选项来发出 HTTP 请求:

*   [axios](https://github.com/axios/axios)
*   [获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

让我们看看如何设置客户端来发出 CORS 请求。

# 使配置在整个应用程序中可用

首先，我们如何使配置对整个应用程序可用？axios 有一个创建新实例的功能。当我们保存这个实例时，我们可以配置它，并在我们需要的任何组件中访问它。使该实例可用的最佳方式是使用上下文。

对于 fetch，我们也可以编写一个上下文，我们可以配置一些函数，然后使用我们自己编写的请求函数。

# Axios 配置

我们需要配置什么？其实不多。最重要的配置如下:

```
baseURL: 'http://api.my-awesome-app.com' 
```

从这一点开始，axios 将总是使用这个 URL 来启动请求。并且浏览器确保它自己发出初始 HTTP 选项请求。

当使用 API 时，您可能还需要向服务器发送一个令牌来验证客户端。标准割台是`Authorization`割台。通常，令牌前面会有一个`“Bearer: "`字符串。但这取决于你使用的 API。

## 旁注

如果您在另一边，并且必须配置服务器，那么设置一些额外的头—即`Access-Control-Allow-Credentials`是很重要的。为什么会这样呢？如前所述，浏览器用一个选项请求询问服务器是否允许它访问资源，服务器告诉浏览器它可以访问哪些资源以及它可以使用哪些头。

在 Ruby 中，有一个`rack-cors` gem 使得服务器配置非常容易。

# 获取配置

为了将`fetch`用于 CORS 请求，我们将`mode`选项设置为`cors`。我们不需要设置默认的 URL，但是我们可以在请求中设置 URL。就像在`axios`的例子中一样，这不会发送任何`Authorization`头，但是通过`headers`选项，我们可以设置服务器允许的任何头。完全配置的请求可能如下所示:

# 结论

正如我们所见，在浏览器中使用 CORS 非常容易。这将为我们提供许多新的可能性，并使开发者和用户的生活更加轻松。

*不是中等会员？* [*在这里报名*](https://grnt-grdwhl.medium.com/membership) *并支持我的写作过程！*
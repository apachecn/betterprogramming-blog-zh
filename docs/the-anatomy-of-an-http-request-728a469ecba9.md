# HTTP 请求的剖析

> 原文：<https://betterprogramming.pub/the-anatomy-of-an-http-request-728a469ecba9>

## 原始 HTTP 请求是什么样子的

![](img/374e28c46cc6d0adc369f93b59f426e6.png)

照片由 [NASA](https://unsplash.com/@nasa?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

HTTP 请求定义了以下内容:

*   **方法**(必需)——(例如:`GET`)
*   **主持人**(必选)——(例如:[www.bing.com](http://www.bing.com))
*   **路径**(必选)——(示例:/搜索)
*   **HTTP 版本**(必需)——(例如:HTTP/2)
*   **表头**(可选)——(例如:`Content-Type: application/json`)
*   **查询字符串**(可选)——(例如:`?q=test`)
*   **车身**(可选)——(例如:`{“q”: “test”}`)

# HTTP 概述

HTTP 是用于通过互联网将数据从一台机器传输到另一台机器的众多协议(通信策略)之一。这是浏览器主要用于与网站通信的协议。

例如，当你去[www.wikipedia.org](https://www.wikipedia.org/)的时候，一个 HTTP 请求被创建并传输到维基百科的服务器，服务器依次渲染并传输一个 HTTP 响应给浏览器。

HTTP 协议是一种“基于文本的协议”，这意味着这种策略使用人类可读的字符作为其通信手段。

因此，作为一个例子，在 bing.com 网站上搜索“test”的请求通过前往[https://www.bing.com/search?q=test](https://www.bing.com/search?q=test)看起来像这样:

```
GET /search?q=test HTTP/2
Host: www.bing.com
User-Agent: curl/7.54.0
Accept: */*
```

很酷，对吧？让我们把这个要求分解一下。

# 主持人

```
GET /search?q=test HTTP/2
**Host:** [**www.bing.com**](http://www.bing.com)
User-Agent: curl/7.54.0
Accept: */*
```

在上面的代码中，带有“Host”的第二行定义了向哪个网站发出请求。在这种情况下，请求将被发送到 Bing 网站。如果把它改成“www.google.com ”,这个请求就会发送到 google。

明白了吗？

# 该方法

```
**GET** /search?q=test HTTP/2
Host: [www.bing.com](http://www.bing.com)
User-Agent: curl/7.54.0
Accept: */*
```

在上面的请求中，“GET”是 HTTP 方法，这是请求指定*它*一般*想要做什么*的一种方式。

以下是一些常见的 HTTP 方法及其用途:

*   `GET` —检索数据，如博客文章。
*   `POST` —创建数据，就像一篇新的博客文章。
*   `PUT` —替换数据，如现有的博客文章。
*   `DELETE` —删除数据，如现有的博客文章。

因此，我们使用`GET`方法的示例请求说:“请为我检索一些数据。”

还有其他 HTTP 方法可以在这些 MDN web 文档中看到。

# 小路

```
GET **/search**?q=test HTTP/2
Host: [www.bing.com](http://www.bing.com)
User-Agent: curl/7.54.0
Accept: */*
```

在上面，`/search`的第一行是小路。加上方法，路径告诉网站*请求*具体*想做什么*。

在这种情况下，`GET /search`说:“请为我检索搜索结果。”

# 头球

```
GET /search?q=test HTTP/2
Host: [www.bing.com](http://www.bing.com)
**User-Agent: curl/7.54.0
Accept: */***
```

在上面,`Host`下面的行是标题。标题是帮助网站确定对请求做出何种响应的附加数据。

例如，`Accept: */*`告诉网站任何类型的响应都可以。这可以变得更加具体。

`User-Agent: curl/7.54.0`告诉网站使用哪个应用程序发出请求。

例如，如果你从 Mac 版本的 Firefox 发出请求，这通常会像`Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:68.0) Gecko/20100101 Firefox/68.0`这样。

在上面的例子中，命令行工具 *cURL* 被用来发出请求。

任何额外的标题可以在以下行中提供。一种著名的头是“cookie”头，它用于在每次请求时向网站传递可重用的数据。

例如，如果登录，用户的“会话”可以每次传递到网站，因此用户不必每次发出请求时都传递用户名和密码。

# 查询字符串

```
GET /search**?q=test** HTTP/2
Host: [www.bing.com](http://www.bing.com)
User-Agent: curl/7.54.0
Accept: */*
```

在上面的例子中，`?q=test`是查询字符串，它向网站提供额外的信息来帮助网站完成它的请求。

记住，在这个例子中，我们告诉 Bing:“请为我检索搜索结果。”我们还没有指定要搜索什么的任何参数。查询字符串提供了这些参数。

让我们来看看:

`?q=test`

在上面的例子中，`q`是一个自变量，`test`是一个值。这被传递到网站的服务器，并提供关于如何满足请求的附加信息。

在我们的请求中，我们现在说:“请为我检索一个搜索的结果，其中搜索值是‘test’。”

如果我们需要在查询字符串中传递额外的值，我们用一个&符号(`&`)将它们分开，如下所示:

`?q=test**&**query_argument_2=value_2**&**query_argument_3=value=3`

有道理吗？

# 请求正文

```
GET /search?q=testHTTP/2
Host: [www.bing.com](http://www.bing.com)
User-Agent: curl/7.54.0
Accept: */*
```

`{ “q”: “test” }`

查询字符串不是向网站传递附加信息的唯一方式。请求体也是这样做的一种方式。

这是典型的**而不是**与`GET`请求一起使用，但是为了举例，我在上面增加了它。通常，它与`POST`和`PUT`等方法一起使用，因为它们指定了要记录的数据，所以对数据传输要求更高。

在上面，在标题和一个空行之后，`{ “q”: “test” }`是请求体，它为网站提供额外的信息来帮助它完成请求，很像一个查询字符串。

在正文中传递数据对查询字符串有一些好处，也有一些缺点。

## 优势

*   没有数据大小限制。查询字符串有最大大小限制(特定于浏览器)，因此不利于传输大量信息。
*   数据在更少的地方被记录和检索，因此更安全。将敏感数据(如密码)放入查询字符串是有问题的(请阅读本文[中的更多内容】](http://blog.httpwatch.com/2009/02/20/how-secure-are-query-strings-over-https/))。
*   更适合发送其他数据传输格式，如 [JSON](https://www.json.org/) 和 XML。虽然这些格式可以放在查询字符串中，但通常不会这样做。带有 JSON 或 XML blobs 的查询字符串看起来非常难看。

## 不足之处

*   请求正文对用户隐藏，不能轻易与他人共享。查询字符串适用于“嘿，查看此链接”的情况，因为值存储在链接中，可以很容易地复制/粘贴给其他人。

# HTTP 版本

```
GET /search?q=test **HTTP/2**
Host: [www.bing.com](http://www.bing.com)
User-Agent: curl/7.54.0
Accept: */*
```

在上图中，第一行的`HTTP/2`是 HTTP 版本。不同版本的 HTTP 以不同的方式处理机器之间交互的细节。目前常用的版本是 HTTP/1.1 和 HTTP/2。

您可以在 MDN web docs 上阅读[了解更多关于不同的 HTTP 版本](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Evolution_of_HTTP)。

# 摘要

HTTP 是一种基于文本的协议，浏览器大量使用它与网站进行通信。

HTTP 请求指定主机、方法、路径、标头、HTTP 版本、查询字符串和请求正文。
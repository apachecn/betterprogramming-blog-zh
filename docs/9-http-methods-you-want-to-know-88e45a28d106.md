# 你可能想知道的 9 种 HTTP 方法

> 原文：<https://betterprogramming.pub/9-http-methods-you-want-to-know-88e45a28d106>

## 创建 CRUD 操作

![](img/b47c0600c0b0c62e1af475eec0e2fe6b.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com/s/photos/http?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在我的另一篇文章“命名 REST API 端点的 10 个最佳实践”中，我简要地提到了 HTTP。超文本传输协议，通常称为 HTTP，据说是一种标准。它用于客户端和服务器之间的通信。它是如何通过客户端和服务器之间的请求-响应协议来工作的。

它是如何工作的一个简单例子:

假设您的浏览器(客户端)向服务器发送一个 HTTP 请求(针对您试图访问的任何站点)。然后服务器向客户端返回一个响应。此响应包含有关您的请求的状态信息，可能包含也可能不包含所请求的内容，具体取决于目的。

如上面的快速示例所述，这就是请求-响应协议。HTTP 有方法或者动词，比如常用的 POST，GET，PUT，DELETE。这四个分别对应于创建、读取、更新和删除(CRUD)操作。正如我所说的，这些只是一些常用的可用方法，所以这意味着还有许多其他方法不经常使用。继续读下去，你可能会看到更多这样的例子。

以下是我将要谈到的方法:

1.  得到
2.  邮政
3.  放
4.  删除
5.  头
6.  修补
7.  选择
8.  连接
9.  找到；查出

它们的名称也区分大小写，必须使用大写字母。(我个人认为这是一个被忽视的好玩的事实。)

在我们深入了解每种方法的细节之前，有一点需要知道，有些方法，比如 GET 和 HEAD，按照惯例只具有检索功能，没有其他功能。因此，它们也被认为是安全的。

同样因为约定，其他方法，比如 POST、PUT 和 DELETE，可能是不安全的*动作。*

除了安全-不安全属性，方法还具有*幂等*属性。简单来说，当一个方法被应用时，当无论应用多少次，结果都没有超出其初始应用的变化时，该方法被视为幂等的。

像 GET、HEAD、PUT 和 DELETE 这样的方法总是预期地返回相同的结果，因此它们本质上是幂等的。当然，前提是没有并发操作在同一组资源上执行。否则，结果不应偏离其最初的应用。

现在您已经了解了这两个属性，让我们更深入地了解每种方法。

# 1.得到

GET 是 API 和网站中最常见、最广泛使用的方法。每当您试图访问一个网站时，这基本上是一种从指定的资源或服务器请求数据或检索数据的行为，您正在调用此方法。

因为这只是检索数据，所以它不应该对数据或任何其他数据有任何其他影响。

下面是 GET 的头的例子。

请求标题示例:

```
GET /hello.html HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
Host: [www.example.com](http://www.example.com)
Accept-Language: en-us
Accept-Encoding: gzip, deflate
Connection: Keep-Alive
```

回复标题示例:

```
HTTP/1.1 200 OK
Server: Apache/2.2.14 (Win32)
ETag: "34aa387-d-1568eb00"
Vary: Authorization,Accept
Accept-Ranges: bytes
Content-Length: 88
Content-Type: text/html
Connection: Closed
```

响应数据示例:

```
<html>
<body>
<h1>Hello, World!</h1>
</body>
</html>
```

从上面的例子中，我们可以看到当您请求`hello.html`时的标题，然后是您得到的响应的标题，然后是将被加载到您的浏览器上的内容响应。

视觉上是怎么做到的？它基本上是在你的浏览器 URI 栏上调用[www.example.com/hello.html](http://www.example.com/hello.html)。

有道理吗？请记住，使用 GET 请求只是为了检索资源表示/信息，而不是以任何方式修改它。因为 GET 方法不会改变资源的状态，所以它被认为是一种安全的方法。

此外，GET APIs 必须在每次被调用时产生相同的结果，无论发出多少个相同的请求。因此，GET 方法也是幂等的。如果资源的状态没有被服务器上的任何其他修改方法更改，则情况确实如此。

例如，如果我们多次调用 GET[http://www.example.com/customers/12345/orders](http://www.example.com/customers/12345/orders)，它将总是为带有`ID: 12345`的特定客户返回相同的订单，除非客户最终以某种方式改变了订单，如果是这样，从那时起结果将会不同。

还有一些关于 GET 的其他注意事项:

*   GET 请求可以被缓存。
*   GET 请求保留在浏览器历史记录中。
*   GET 请求可以加入书签。
*   在处理敏感数据时，不应使用 GET 请求。
*   GET 请求有长度限制。
*   GET 请求仅用于请求数据(而非修改)。

那么收益呢？返回或响应状态应该是`200 (OK)`，就像上面的响应标题示例中显示的那样。

然而，当出现错误时，它通常会返回一个`404 (NOT FOUND)`或`400 (BAD REQUEST)`。

# 2.邮政

该方法也相当常见，广泛应用于 API 和网站中。它用于向服务器发送数据，并创建或更新资源。

当您在线提交表单时，您最常遇到这种情况。在幕后，它将您的数据输入发送到服务器。文件上传也属于这一类。

实际上，它最常用于创建新资源。这是因为还有另一种非常常用的方法来更新资源。

POST 既不安全也不幂等。它不安全，因为它会修改数据。它也不是幂等的，因为它不安全。发出两个相同的 POST 请求很可能会导致两个资源包含相同的信息。即使不完全相同，也会产生两种资源。

例子如下。

请求标题示例:

```
POST /feedback.html HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
Host: [www.example.com](http://www.example.com)
Content-Type: text/xml; charset=utf-8
Content-Length: 88
Accept-Language: en-us
Accept-Encoding: gzip, deflate
Connection: Keep-Alive
```

请求数据示例:

```
<?xml version="1.0" encoding="utf-8"?>
<string >string</string>
```

回复标题示例:

```
HTTP/1.1 200 OK
Server: Apache/2.2.14 (Win32)
ETag: "34aa387-d-1568eb00"
Vary: Authorization,Accept
Accept-Ranges: bytes
Content-Length: 88
Content-Type: text/html
Connection: Closed
```

响应数据示例:

```
<html>
<body>
<h1>Request Processed Successfully!</h1>
</body>
</html>
```

从我们的角度来看，我们可能会这样称呼它:POST[www.example.com/feedback.html](http://www.example.com/feedback.html)。

对于回复内容，您可能会收到也可能不会收到，这取决于创建者。但是，您仍然会收到响应标头，以指示它是否是一个成功的请求。

关于 POST 方法的其他一些注意事项:

*   从不缓存 POST 请求。
*   发布请求不会保留在浏览器历史记录中。
*   发布请求不能加入书签。
*   POST 请求对数据长度没有限制。

对 POST 的响应也是不可缓存的，除非该响应包括适当的缓存控制或`Expires`头字段。

对于返回，它可以返回以下三种 HTTP 状态:

*   `201 (Created)`带有一个`Location`标题，链接到新创建的资源
*   `200 (OK)`，这可能不会产生可被 URI 识别的资源
*   `204(No Content)`，这也是另一个明确表示没有资源返回的

对于错误情况，如果资源已经存在，它可以返回`404 (Not Found)`或`409 (Conflict)`。

# 3.放

PUT 是另一种在 API 和网站中也非常常见和广泛使用的方法。它用于向服务器发送数据，并创建或更新资源。

但是，还记得我说过 POST 更适合用于创建资源吗？PUT 方法主要用于更新现有资源。如果要更新的资源不存在，那么 API 可以决定是否创建新的资源。

因为它修改了服务器上的状态，所以它是不安全的。但是，它是幂等的。这就是 POST 和 PUT 的区别。这是因为多次调用 PUT 请求总是会产生相同的结果。相比之下，重复调用 POST 请求会产生多次创建相同资源的副作用。

例如，如果对资源调用 PUT 使资源内的计数器递增，则该调用不再是等幂的。有时会发生这种情况，它可能被视为不是等幂的。但是，建议让 PUT 请求保持幂等，并强烈建议对非幂等请求使用 POST。

以下是 PUT 的例子。

请求标题示例:

```
PUT /hello.html HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
Host: [www.example.com](http://www.example.com)
Accept-Language: en-us
Connection: Keep-Alive
Content-type: text/html
Content-Length: 182
```

请求数据示例:

```
<html>
<body>
<h1>Hello, World!</h1>
</body>
</html>
```

回复标题示例:

```
HTTP/1.1 201 Created
Server: Apache/2.2.14 (Win32)
Content-type: text/html
Content-length: 30
Connection: Closed
```

响应数据示例:

```
<html>
<body>
<h1>The file was created.</h1>
</body>
</html>
```

从我们的角度来看，调用大概是这样的:把[www.example.com/hello.html](http://www.example.com/hello.html)。

就像 POST 一样，响应中的主体也是可选的。PUT 方法也是不可缓存的。

对于 HTTP 状态，成功案例将相应地通知用户:

*   `201 (Created)`如果创建了资源
*   `200 (OK)`如果现有资源被更新并且可能带有内容响应
*   `204 (No Content)`如果现有资源被更新而没有内容

对于错误情况，如果没有找到输入 id 或者输入 ID 无效，可以返回`404 (Not Found)`。否则，您可以返回`405 (Method Not Allowed)`，除非您想要更新/替换整个集合中的所有资源。

# 4.删除

删除是另一种在 API 和网站中非常普遍和广泛使用的方法。它用于删除指定的资源。

DELETE 方法用于请求服务器删除位于给定 URL 指定位置的文件。这个很简单。

删除操作是幂等的。如果删除资源，它将被删除。对该资源反复调用 DELETE 以同样的方式结束:资源消失了。

比方说，如果调用 DELETE 减少了一个计数器(在资源内)，那么 DELETE 调用不再是等幂的。这是因为数据已被更改。但是，您通常不会遇到 DELETE 的这种用法。

以下是它的例子。

请求标题示例:

```
DELETE /hello.html HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
Host: [www.example.com](http://www.example.com)
Accept-Language: en-us
Connection: Keep-Alive
```

回复标题示例:

```
Response header example:
HTTP/1.1 200 OK
Server: Apache/2.2.14 (Win32)
Content-type: text/html
Content-length: 30
Connection: Closed
```

响应数据示例:

```
<html>
<body>
<h1>Resource deleted.</h1>
</body>
</html>
```

对此的调用大致如下:删除[www.example.com/hello.html](http://www.example.com/hello.html)。

此方法也不可缓存。

成功案例的三种可能回报是:

*   `200 (OK)`带应答体
*   `204 (No Content)`无应答体
*   `202 (Accepted)`如果动作已经排队

然而，当第二次调用资源上的 DELETE 时，它通常会返回一个`404 (NOT FOUND)`,因为它已经被删除了，因此无法再找到。

对于某些人来说，这使得删除操作不再是幂等的。但是，资源的最终状态是相同的。

# 5.头

就我个人而言，我很少看到这种方法被使用。但是，它的行为类似于 GET 方法，只是它没有响应体。

HEAD 方法在功能上类似于 GET，只是服务器回复时没有实体主体(基本上只有头信息)。服务器不发送报头后的任何数据。换句话说，如果 GET `/orders`返回订单列表，那么 HEAD `/orders`将发出相同的请求，但不会返回任何列表。

HEAD 请求对于在实际发出 GET 请求之前检查 GET 请求将返回什么非常有用，比如在下载大文件或响应正文之前。

它也是一个安全的幂等方法，就像 GET 方法一样。

下面是 HEAD 的例子。

请求标题示例:

```
HEAD /hello.html HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
Host: [www.example.com](http://www.example.com)
Accept-Language: en-us
Accept-Encoding: gzip, deflate
Connection: Keep-Alive
```

回复标题示例:

```
HTTP/1.1 200 OK
Server: Apache/2.2.14 (Win32)
ETag: "34aa387-d-1568eb00"
Vary: Authorization,Accept
Accept-Ranges: bytes
Content-Length: 88
Content-Type: text/html
Connection: Closed
```

无响应数据示例。

对于 HTTP 状态，它们与 GET 方法中的状态非常相似。

# 6.修补

这也是另一个我不常看到的，但知道它还是很好的。它也用于更新，就像游戏中的“补丁”一样。然而，这和 PUT 是有区别的，PATCH 更像是更新-修改，而 PUT 更像是更新-替换类型的更新。

因此，补丁请求只需要包含对资源的更改，而不是整个资源。它应用差异(部分更新)而不是替换整个资源。同时，如果您要替换整个资源，则使用 PUT。

补丁既不安全也不幂等。然而，补丁请求可以以幂等的方式发出，这也有助于防止在相似的时间范围内同一资源上的两个补丁请求之间的冲突产生不良结果。

对于示例，让我们看一下数据内容，以便更好地理解。假设我们有这样的数据:

```
{
“name” : “Mark”,
“isUser” : false,
“numberOfDogs” : 8
}
```

然后我们想用这个来更新它:

```
{
“numberOfDogs” : 10,
“numberOfCats” : 0
}
```

结果大概是这样的:

```
{
“name” : “Mark”,
“isUser” : false,
“numberOfDogs” : 10,
“numberOfCats” : 0
}
```

请注意它是如何修补、更新旧值和添加新字段的。

对于 PUT，使用相同的现有数据和输入数据，输出请求将只包含:

```
{
“numberOfDogs” : 10,
“numberOfCats” : 0
}
```

这是因为 PUT 更像是更新-替换，而 PATCH 更像是更新-修改。

在不存在的资源上发送修补请求将会失败，并且不会创建任何资源。

请注意，如果您决定在应用程序中使用补丁 API，会遇到一些列出的挑战:

*   浏览器、服务器和 web 应用程序框架对补丁的支持并不普遍。IE8、PHP、Tomcat、Django 和许多其他软件都缺少或中断了对它的支持。
*   补丁请求的有效负载不像上传请求那样简单。

# 7.选择

这个我个人只用了一次。它用于请求有关可用通信选项的信息。它描述了目标资源的通信选项。

基本上，您可以在指定的 URL 上找到 web 服务器支持的 HTTP 方法和其他选项的列表(星号是指整个服务器，还是只与 URL 相关的特定服务器)。

以下示例请求主机`<host>`上运行的 web 服务器支持的方法列表:

请求标题示例:

```
OPTIONS * HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```

回复标题示例:

```
HTTP/1.1 204 No Content
Server: Apache/2.2.14 (Win32)
Allow: GET,HEAD,POST,OPTIONS
Content-Type: httpd/unix-directory
```

这个方法是安全的、等幂的，并且不可缓存。

# 8.连接

如果您使用该工具，则无法在 Postman 中找到此方法。它的用途是建立到由给定 URI 标识的服务器的隧道。

它基本上被客户端用来通过 HTTP 建立到 web 服务器的网络连接。就是这样。

以下示例请求与主机`<host>`上运行的 web 服务器建立连接:

请求标题示例:

```
CONNECT www.example.com HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```

回复标题示例:

```
HTTP/1.1 200 Connection established
Server: Apache/2.2.14 (Win32)
```

标题状态返回仅仅是`200 “Connection Established”`。我想说它非常像选项，标题看起来非常简单明了。

# 9.找到；查出

此方法沿着到目标资源的路径执行消息环回测试。

基本上，它用于将 HTTP 请求的内容回显给请求者，以便在开发时进行测试/调试。

它可能看起来像下面这样。

请求标题示例:

```
TRACE / HTTP/1.1
Host: [www.example.com](http://www.example.com)
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```

回复标题示例:

```
HTTP/1.1 200 OK
Server: Apache/2.2.14 (Win32)
Connection: close
Content-Type: message/http
Content-Length: 39TRACE / HTTP/1.1
Host: [www.example.com](http://www.example.com)
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
```

# 结论

在创建 API 时，有时了解每种 HTTP 方法的差异是有好处的。这是为了创建具有更准确目的的更准确的 API。通常初学者容易混淆所有这些，因此他们只是对所有东西都使用 GET，这当然会在处理敏感数据或任何数据时带来安全问题。有时候，随着你的进步，你仍然会对某些方法感到困惑，比如什么时候使用哪种方法。

我希望你已经从这篇文章中学到了一些东西，受到了启发。如果你觉得这有帮助，请随意评论或与其他人分享。

感谢阅读！
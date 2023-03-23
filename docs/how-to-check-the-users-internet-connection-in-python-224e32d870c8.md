# 如何在 Python 中检查用户的互联网连接

> 原文：<https://betterprogramming.pub/how-to-check-the-users-internet-connection-in-python-224e32d870c8>

## 检查互联网连接的 4 种不同方法

![](img/bdc8fcf9656764cbfdfc7d2727ba6cc3.png)

莱昂·塞伯特在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

通过阅读本文，您将学会用 Python 定义自己的函数来检查互联网连接。如果您正在处理依赖互联网连接的昂贵操作，强烈建议您事先检查互联网连接。

让我们进入下一节，开始编写一些 Python 代码。

# 履行

在这篇文章中，我将提供四种不同的方法来检查互联网连接。它们中的每一个都使用不同的模块，并且有各自的优点和缺点。

## ' httplib '

基于[官方文件](https://docs.python.org/2/library/httplib.html) , `httplib`定义:

> “…实现 HTTP 和 HTTPS 协议客户端的类。它通常不被直接使用——模块`urllib`用它来处理使用 HTTP 和 HTTPS 的 URL。”

添加以下进口声明。

```
try:
    import httplib
except:
    import http.client as httplib
```

定义一个接受两个输入参数的函数。为其他方法定义的所有函数都遵循相同的输入参数，只是略有不同。我将使用`www.google.com`作为默认值。如果您遇到问题，请根据您的偏好将其更改为任何 URL。

```
def checkInternetHttplib(url="www.google.com", timeout=3):
```

基于输入参数创建一个`HTTPConnection`。

```
conn = httplib.HTTPConnection(url, timeout=timeout)
```

请求只获取标题。理论上，与获取所有响应数据的方法相比，它应该快得多。如果成功，返回`True`；否则返回`False`。

```
try:
    conn.request("HEAD", "/")
    conn.close()
    return True
except Exception as e:
    print(e)
    return False
```

完整的代码如下:

## '插座'

基于[官方文件](https://docs.python.org/3.8/library/socket.html),`socket`模块:

> “…提供对 BSD *插座*接口的访问。它可以在所有现代的 Unix 系统、Windows、MacOS 以及其他平台上使用。”

添加以下进口申报:

```
import socket
```

用下列输入参数定义一个函数。请注意，我使用了`host`而不是 URL 作为第一个参数的名称。还有一个额外的参数叫做`port`。`8.8.8.8`是 Google ( `google-public-dns-a.google.com`)的公共 DNS，可以通过 TCP 使用端口`53`访问。

```
def checkInternetSocket(host="8.8.8.8", port=53, timeout=3):
```

设置默认超时，并使用以下代码建立套接字连接:

```
try:
    socket.setdefaulttimeout(timeout)
    socket.socket(socket.AF_INET, socket.SOCK_STREAM).connect((host, port))
    return True
except socket.error as ex:
    print(ex)
    return False
```

你可以在下面的要点找到完整的代码。

## '请求'

基于[官方文件](https://2.python-requests.org/en/master/),`requests`模块:

> “…是一个优雅而简单的 Python HTTP 库，为人类而建。它允许您非常容易地发送 HTTP/1.1 请求。不需要手动添加查询字符串到你的网址，也不需要对你的文章数据进行格式编码。”

添加以下进口申报:

```
import requests
```

用下列输入参数定义一个函数。

```
def checkInternetRequests(url='http://www.google.com/', timeout=3):
```

在函数中实现以下代码。为了加快推断时间，我们将使用`head`而不是通常的`get`。

```
try:
    r = requests.head(url, timeout=timeout)
    return True
except requests.ConnectionError as ex:
    print(ex)
    return False
```

检查以下要点以获得完整的代码。

## ' urllib.request '

基于[官方文件](https://docs.python.org/3.8/library/urllib.request.html#module-urllib.request),`urllib.request`模块:

> “…定义有助于在复杂世界中打开 URL(主要是 HTTP)的函数和类，包括基本和摘要式身份验证、重定向、cookies 等。”

添加以下进口声明。

```
import urllib.request
```

定义一个接受以下输入参数的函数。

```
def checkInternetUrllib(url='http://google.com', timeout=3):
```

在函数中实现以下代码:

```
try:
    urllib.request.urlopen(url, timeout=timeout)
    return True
except Exception as e:
    print(e)
    return False
```

以下要点包含完整的代码。

# 结论

让我们回顾一下今天所学的内容。

我们从使用`httplib`模块检查互联网连接开始。我们只做了一个简单的请求来获取标题，因为这样效率更高。

接下来，我们尝试使用`socket`模块实现另一个功能。我们使用 Google 的公共 DNS 和端口`53`作为请求的主机。一般情况下，这个方法在执行时间上应该是最快的。

我们还测试了名为`requests`的高级库来发送 HTTP1.1 请求。如果您使用的是`get`方法，返回的响应包含所有数据，与`httplib`相比会慢很多。建议使用`head`方法来加快推断时间。

最后，我们使用`urllib.request`模块向同一个 URL 发出一个简单的请求。`urrlib.request`从属于`requests`模块。

感谢你阅读这篇文章。希望在下一篇文章中再见到你！

# 参考

*   `[urllib.request](https://docs.python.org/3.8/library/urllib.request.html#module-urllib.request)` [文档](https://docs.python.org/3.8/library/urllib.request.html#module-urllib.request)
*   `[socket](https://docs.python.org/3.8/library/socket.html)` [文档](https://docs.python.org/3.8/library/socket.html)
*   `[httplib](https://docs.python.org/2/library/httplib.html)` [文档](https://docs.python.org/2/library/httplib.html)
*   `[requests](https://2.python-requests.org/en/master/)` [文档](https://2.python-requests.org/en/master/)
*   [堆栈溢出的示例代码](https://stackoverflow.com/questions/3764291/checking-network-connection)
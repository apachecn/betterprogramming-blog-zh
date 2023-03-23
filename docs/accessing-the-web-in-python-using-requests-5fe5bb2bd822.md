# 使用请求在 Python 中访问 Web

> 原文：<https://betterprogramming.pub/accessing-the-web-in-python-using-requests-5fe5bb2bd822>

## 用 Python 制作 HTTP 请求的快速指南

![](img/1eda6da3f5e7c45b1b2039c148b844c0.png)

由[尼古拉斯·皮卡德](https://unsplash.com/@artnok?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

请求库是一个为 Python 构建的简单 HTTP 库。它有很多特性，允许发送带有自定义头和 SSL 验证的请求。

**目录:**

1.  [什么是 HTTP？](#3817)
2.  [获取请求](#c3ae)
3.  [分析响应](#1a85)
4.  [发布请求](#db4e)
5.  [饼干](#5963)
6.  [会话](#9ca2)

**什么是 HTTP？** HTTP(超文本传输协议)是一组协议，使用这些协议服务器和客户端之间能够进行通信。它基于请求-响应模型工作。

交流有不同的方法。以下是最常见的方法:

*   **获取:**从服务器检索数据
*   **发布:**发布数据到服务器
*   **PUT:** 替换服务器上现有的资源
*   **删除:**删除指定的资源。

现在让我们来看看如何将请求库与不同的 HTTP 方法一起使用:

在开始之前，安装请求库

```
pip install requests
```

将请求库导入 python 脚本，如下所示

```
import requests
```

**获取请求:**

*   一个简单的 GET 请求:
    返回值将是一个包含来自服务器的响应信息的响应对象。

```
response = requests.get('https://httpbin.org/get')
```

*   获取带有参数的请求:

```
parameters = **{**'key1'**:** 'value1'**,** 'key2'**:** 'value2'**}**
response = requests.get('https://httpbin.org/get', params=parameters)
```

*   获取带有自定义标题的请求:

```
headers = {'Bearer':*Authorization_token*}
response = request.get('https://httpbin.org/get', headers=headers)
```

*   使用`stream`参数获取请求:
    这用于防止大量响应一次全部加载到内存中。

```
response = request.get(url_to_download_large_file, stream=True)
```

**分析响应:**

发送的请求将收到来自服务器的响应。它将存储在一个响应对象中。响应对象提供与响应相关的所有信息。让我们看看下面几个:

*   **查看来自服务器响应的内容**:

**文本响应:**使用`text` 属性可以访问内容。
Requests 使用报头中接收的编码格式来解码内容。

```
response = requests.get('https://httpbin.org/get')
print(response.text)#Result
{
  "args": {
    "key1": "value1", 
    "key2": "value2"  
  },...
```

**非文本响应:**可以使用`content`属性以字节形式访问内容。默认情况下， **gzip** 和 **deflate** 编码被解码。

```
response = requests.get('https://httpbin.org/get')
print(response.content)# Result
b'{\n  "args": {\n    "key1": "value1", \n    "key2": "value2"\n  }, \n  "headers": {\n    "Accept": "*/*", \n 
   "Accept-Encoding": "gzip, deflate",...
```

**JSON 响应:**如果来自服务器的响应是 JSON 格式的，那么可以使用`json()`方法轻松解码

```
response = requests.get('https://httpbin.org/get')
print(response.json())#Result
{'args': {'key1': 'value1', 'key2': 'value2'}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Host': 'httpbin.org', 'User-Agent'...
```

Requests 还提供了通过`raw`属性查看来自服务器的原始响应的选项。为此，我们必须在初始请求中传递`stream=True`。

*   **下载图像或大文件:**

为了处理响应中发送的大文件，我们可以使用`iter_content()`方法和`chunk_size`关键字参数。区块大小指定了一次要读取的字节数。

```
response = request.get(url_to_download_large_file, stream=True)
with open**(**filename**,** 'wb'**)** as f**:**
    for chunk in response.iter_content**(**chunk_size=128**):**
        f.write**(**chunk**)**
```

*   **查看编码格式:**

`encoding`属性提供了所使用的编码方法。我们可以将属性重新分配给不同的编码，每当您调用`response.text`时，请求将使用新的`response.encoding`值。

```
response = requests.get('https://httpbin.org/get')
print(response.encoding)#Result
utf-8#Reassign a new encoding
response.encoding = 'ISO-8859-1'
```

**响应对象中的其他有用属性:**

*   `status_code`:提供响应的 HTTP 状态码。
*   `elapsed_time`:发送请求到收到响应之间经过的时间。
*   `url`:给出发送响应的实际 URL。这有助于识别请求是否被重定向。
*   `cookies`:该属性包含服务器发回的 cookies。
*   `headers`:服务器发送的头。
*   `history`:该属性用于检查请求是否被重定向。该属性将包含为完成请求而创建的响应对象列表，从最早的到最新的。
    **注意:**为了避免重定向，我们可以在请求中传入`allow_redirects=False`。

响应对象还包含请求的细节。这可以使用响应对象的`request`属性来访问

```
params = {'key1': 'value1', 'key2': 'value2'}
response = requests.get("https://httpbin.org/get", params=params)
print(response.request.__dict__)#Result 
{'method': 'GET', 'url': '[https://httpbin.org/get?key1=value1&key2=value2'](https://httpbin.org/get?key1=value1&key2=value2'), 'headers': {'User-Agent': 'python-requests/2.26.0'...
```

**帖子请求:**

*   发送表单编码数据的简单 POST 请求:
    要发送表单编码数据，我们将使用`data`关键字。

```
data = **{**'key1'**:** 'value1'**,** 'key2'**:** 'value2'**}** response = requests.post**(**"https://httpbin.org/post"**,** data=data**)** print(response.text)#Result
{
  "args": {},
  "data": "",
  "files": {},
  "form": {
    "key1": "value1",
    "key2": "value2"
  },...
```

*   包含一个键的多个值的 POST 请求:

```
data = **{**'key1'**:** ['value1'**,** 'value2']**} 
or** data = [('key1','value1'),('key1','value2')]
response = requests.post**(**"https://httpbin.org/post"**,** data=data**)** print(response.text)#Result
{
  "args": {},
  "data": "",
  "files": {},
  "form": {
    "key1": [
      "value1",
      "value2"
    ],,,
```

*   发送多部分编码文件的 POST 请求:
    要发送多部分编码文件，我们使用`files`关键字。

```
files = **{**'file'**:** open**(**file**,** 'rb'**)}**
response = requests.post**(**'https://httpbin.org/post'**,** files=files**)**
```

*   发送 JSON 编码数据的 POST 请求:
    可以使用关键字`json`发送 JSON 数据

```
data = **{**'key1'**:** 'value1'**,** 'key2'**:** 'value2'**}** response = requests.post**(**"https://httpbin.org/post"**,** json=data**)** print(response.text)
```

上面将发送一个内容类型为`application/json`
**的 POST 请求。注意:**如果使用了`data`或`files`关键字，将忽略`json`关键字。

**饼干:**

*   在请求中发送 Cookies:

```
cookies = {'my-cookie':'value'}
response = requests.get**(**'https://httpbin.org/cookies'**,** cookies=cookies**)** print(response.text)#Result
{
  "cookies": {
    "my-cookie": "value"
  }
}
```

Cookie 在一个请求 Cookie Jar 中返回，它的作用就像一个字典，但也提供了一个更完整的接口，适合在多个域或路径上使用。

我们可以创建 RequestsCookieJar 的一个实例，并将 cookies 设置为针对特定路由发送。在请求过程中，只会发送这些 cookies。

```
cookie_jar = requests.cookies.RequestsCookieJar()
cookie_jar.set('my-cookie1', 'value1', domain='httpbin.org', path='/cookies')
cookie_jar.set('my-cookie2', 'value2', domain='httpbin.org', path='/anything')
cookie_jar.set('my-cookie3', 'value3', domain='httpbin.org', path='/cookies')
response1 = requests.get('https://httpbin.org/cookies', cookies=cookie_jar)print(response1.text)#Result
{
  "cookies": {
    "my-cookie1": "value1",
    "my-cookie3": "value3"
  }
}
```

**会话:**

会话用于跨请求保持某些参数。使用会话实例发送的 cookies 也会跨请求持久化。由于使用了 urllib3 的连接池，对同一台主机的多个请求会导致性能显著提高。

会话对象拥有与请求 API 相同的所有方法。

```
s = requests.Session()
s.cookies.update({'my_cookie1':'value1'})
response = s.get('http://httpbin.org/cookies')
print(response.text)#Result
{
  "cookies": {
    "my_cookie1": "value1"
  }
}response = s.get('http://httpbin.org/cookies',cookies={'my_cookie2':'value2'})
print(response.text)#Result
{
  "cookies": {
    "my_cookie1": "value1",
    "my_cookie2": "value2"
  }
}
```

正如我们所看到的，cookie 将被设置在会话级，作为请求的一部分发送的任何新 cookie 都将被附加上会话级 cookie。

使用会话的通常模式是使用`with`块，因为一旦退出上下文管理器，会话就会自动关闭

```
with requests.Session() as s:
    s.cookies.update({'my_cookie1':'value1'})
    response = s.get('http://httpbin.org/cookies')
    print(response.text)
```

我们已经介绍了请求库的基础知识，还研究了如何发送 GET 和 POST 请求。类似地，PUT 和 DELETE 请求也可以通过使用请求 API 上的相应方法来发送。

这样，您现在应该能够通过传递 cookies、标头以及发送/下载文件向服务器发出基本请求。

## **参考资料(官方 Python 文档)**

[请求](https://docs.python-requests.org/en/latest/user/quickstart/)

```
**Want to Connect?**If you like to read more of my articles do follow me on [LinkedIn](https://www.linkedin.com/in/hari-hara-subramanyam-s-b2692636).
```
# Python 中 REST API 的客户端缓存

> 原文：<https://betterprogramming.pub/client-side-caching-of-a-rest-api-in-python-7f2ce483efb8>

## 一个简单的缓存，用于在客户端缓存 REST API 响应。这减少了网络负载和 API 调用，提高了性能

![](img/15e7e0388b95f22f56e3ebbec3ef40ed.png)

扬·安东宁·科拉尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们生活在一个支持 API 的世界里。越来越多的应用程序建立在开放的 API 之上。有时频繁调用同一个 API、同一个端点和同一个参数，会导致相同的响应。

在客户端缓存上执行搜索并没有产生一个适合我使用的好的解决方案，所以我决定构建我自己的非常简单的解决方案。在这个例子中，它缓存来自 REST apis 的 JSON 结果。但是 JSON 部分是可选的。

缓存构建在 python 函数中，该函数执行 web 调用并将生成的 JSON 内容翻译成字典。当代码中的某个地方需要 REST 调用时，使用这个函数。

`json_api_call`对指定的主机和基本 url 执行 HTTPS 请求。`params`参数必须包含 URL 编码的参数，例如`start=5.110%2C52.088&end=5.113%2C52.088&units=km`。

第二个版本增加了缓存机制。请求 URL 用作键，响应的 JSON 解码值是值。如果缓存返回 None(第 17–18 行)，则进行 API 调用，结果存储在缓存中(第 24 行)。通过使用主机名、基本 URL 和参数，我们得到了一个唯一的字符串。如果 API 调用的头包含使调用唯一的相关信息，可以将它添加到键中。对于当前目的，这不是必需的。

# **首次天真实现**

缓存的第一个简单实现存储一个[key，value]对，并将其无限期地保存在缓存中:

`add`函数将[key，value]对添加到字典中。`get`方法检查这个键是否存储在字典中，如果是，返回相关的值。

# **优化实施**

这种实现有两个主要缺点。首先,[key，value]无限存储，因为没有到期时间。第二，它基于(长)字符串比较键，这些字符串可能只在结尾之后不同。

第一个问题可以通过添加过期时间(秒)来解决。一个[key，value]对在这段时间后变得无效，并且不会被`get`方法返回，而是从字典中删除。[键，值]对用到期时间扩展为[键，到期，值]。

第二个问题可以通过使用字符串的散列而不是字符串本身作为密钥来解决。当添加一个项目时，散列被用作键。这导致了以下版本的`Cache`对象:

在构造类的过程中，会指定过期时间的默认值。通过指定`timeout`参数(第 14–16 行),可以在将项目添加到缓存时覆盖该默认值。第 28 行创建了一个默认过期时间为 60 秒的缓存。

从缓存中检索值时，会检查到期时间。如果该值仍然有效，则返回该值(第 20–23 行)。如果过期时间已过，则从字典中删除该值(第 25 行),并返回`None`。

# **最后的话**

我希望你喜欢这篇文章。要获得更多灵感，请查看我的其他文章:

*   [对 CSV 文件中的列执行功能](https://towardsdev.com/perform-a-function-on-columns-in-a-csv-file-a889ef02ca03)
*   [根据你的活动跟踪器的日志创建热图](https://towardsdatascience.com/create-a-heatmap-from-the-logs-of-your-activity-tracker-c9fc7ace1657)
*   [用 Python 删除文本中的个人信息](https://towardsdatascience.com/remove-personal-information-from-text-with-python-232cb69cf074)
*   [使用 Python 的并行 web 请求](https://towardsdatascience.com/parallel-web-requests-in-python-4d30cc7b8989)
*   [所有公共交通工具都通向乌得勒支，而不是罗马](https://towardsdatascience.com/all-public-transport-leads-to-utrecht-not-rome-bb9674600e81)

*免责声明:本文包含的观点和看法仅归作者所有。*
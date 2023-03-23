# Twitter 解析的乐趣

> 原文：<https://betterprogramming.pub/fun-with-twitter-parsing-2ab3b729d375>

## 仅使用几行代码从 Twitter 账户中提取媒体信息

![](img/59d882f6171d21ab375e29faa7d5d31f.png)

莎拉·库菲在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

# 介绍

流行的社交媒体平台 Twitter 包含了丰富的信息。不仅来自个人推文的文本有助于挖掘社交信息，推文和转发中包含的图像和视频也可以让人们了解帐户处理通常在互联网上浏览或分享的内容。

我有一个朋友让我给他一个脚本，可以下载任何账户的所有推文、图片和视频。他特别感兴趣的是获取账户负责人在他们的时间线上发布的图片和视频。由于 Python 是我最喜欢的语言，我决定编写一个小的实用程序脚本，从 Twitter 时间轴下载媒体文件。

就像 Python 一样，有一个生命、宇宙和一切事物的库。关于宇宙和一切，我都是在开玩笑。无论如何，在今天的文章中，我将向你展示，你可以用大约 100 行代码轻松地完成一些事情。我们将使用名为 [Tweepy](https://www.tweepy.org/) 的精彩 Python 库。

首先，启动一个终端并安装以下软件(如果您还没有安装的话):

```
pip install tweepy
```

# 从用户时间线获取推文

Twitter 的 API 参考和文档可以在 https://developer.twitter.com/en/docs/api-reference-index 的[找到。我们对用户时间线端点特别感兴趣。](https://developer.twitter.com/en/docs/api-reference-index)

```
GET statuses/user_timeline
```

正如文档中提到的，这将返回由用户发布的最新 tweets 的集合，由`screen_name`或`user_id`参数指示。

仅当经认证的用户“拥有”时间线或者是拥有者的经批准的追随者时，才可以请求属于受保护用户的用户时间线。

返回的时间线相当于用户在 Twitter 上的个人资料。

这种方法最多只能返回 3200 条用户最近的推文。无论请求此资源时`include_rts`是否设置为 false，用户对其他状态的本地转发都包括在此总数中。

为了对这个 API 端点发出经过验证的请求，用户需要到[https://dev.twitter.com/apps/new](https://dev.twitter.com/apps/new)创建一个新的 Twitter 应用。

创建完成后，您可以转到 API Keys 选项卡，在那里您将找到消费者密钥和消费者秘密条目。确保复制它们。创建一个名为. twitter.json 的文件，并将其放在您的主目录中。

我们首先需要确保我们的脚本可以使用这些密钥并创建一个经过身份验证的客户端。这可以通过以下方式完成:

```
from tweepy import OAuthHandlerauth = OAuthHandler(consumer_key, consumer_secret)
api = tweepy.API(auth)
```

API 对象将用于访问用户时间线。

# 页码

Twitter 的好人们在他们的 API 中加入了分页。如果你不知道什么是分页，它基本上是 API 如何用数据块来响应，而不是一次性发送整个响应。这可以防止大量数据通过网络发送。

为了执行分页，我们必须为每个请求提供一个 page/cursor 参数。这里的问题是这需要大量的样板代码来管理分页循环。为了使分页更容易，需要的代码更少，Tweepy 有了`Cursor`对象。你像这样使用它:

```
for status tweepy.Cursor(api.user_timeline).items(num_items):
    # process status here
    process_status(status)
```

如果`num_items`没有通过，默认情况下 Twitter 将返回超过 3200 条推文的光标。

太棒了。现在让我们在代码中使用`Cursor`对象。

# 获取图像和视频

我们有兴趣解析每条推文，检查它是否有任何图像和视频，然后下载这些工件。与 tweet 相关联的媒体对象存在于名为`extended_entities`的嵌套属性中。

为了访问图片或视频的 URL，我们首先检查推文中是否存在`extended_entities`属性。如果是，那么我们迭代对应于`extended_entities`对象中属性`media` 的条目。我们检查每个媒体对象的类型是`media_url`还是`video`或`animated_gif`，分别表示它是图像还是视频。我们将每个 URL 存储在单独的列表中。

方法`process_tweet` 是我们定义提取图像和视频 URL 的逻辑的地方。

现在您有了两个列表，一个用于视频，一个用于图像，您可以很容易地使用 Python [wget 库](https://pypi.org/project/wget/)将它们下载到磁盘。

使用多线程来并行化下载媒体 URL 的任务是很诱人的，因为这主要是磁盘 I/O，CPU 开销很小。

完整的代码可以在 GitHub 上找到[。](https://github.com/sauravbiswasiupr/twitter-media-downloader)
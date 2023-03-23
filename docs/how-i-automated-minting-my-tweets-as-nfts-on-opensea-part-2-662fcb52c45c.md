# 我是如何在 OpenSea 上将我的推文自动生成为 NFT 的——听 Twitter 事件和排队工作

> 原文：<https://betterprogramming.pub/how-i-automated-minting-my-tweets-as-nfts-on-opensea-part-2-662fcb52c45c>

## 用 Webhooks 定制我的 OpenSea 系统——第 2 部分

![](img/e6382e54ca93dde29f7b6b27bb0a7115.png)

图为[舒巴姆·达吉](https://unsplash.com/@theshubhamdhage?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

这是“我如何在 OpenSea 文章上自动将我的 Tweets 生成为 NFT”系列的第二部分。在本文中，我将讨论如何使用 NodeJS 和 ExpressJS 创建 OpenSear-API webhook 监听器。

要阅读本系列文章的第一部分，请查看以下链接:

[](/how-i-automated-minting-my-tweets-as-nfts-on-opensea-854c50a44467) [## 我如何在 OpenSea 上自动将我的推文制作成非功能性文件

### 下面是我如何创建自己的系统来上市和铸造非功能性交易

better 编程. pub](/how-i-automated-minting-my-tweets-as-nfts-on-opensea-854c50a44467) 

在我的第一篇文章中，(我不希望这成为一个系列文章，但我的故事太大了，无法在一篇文章中讲述)我讨论了我如何创建 Twitter 帐户活动 API webhooks。在本文中，我将讨论如何创建 OpenSear-API，它本质上是一个 webhook 监听器。

首先，对于这个 API，我们需要创建两个端点。

```
GET /callback
```

这个端点用于向 Twitter 帐户活动 API 注册和认证侦听器端点，并在需要时重新认证。

```
POST /callback
```

Twitter 帐户活动 API 使用这个端点来发送 webhook 事件。

示例 JSON 有效负载发送到 POST/回调

上面的要点是一个发送到 POST /callback 端点的示例 JSON 有效负载。你可以从这里找到更多。

[](https://developer.twitter.com/en/docs/twitter-api/premium/account-activity-api/guides/account-activity-data-objects) [## 帐户活动数据对象

### 订阅用户执行以下任何操作时的推文状态负载:推文、转发…

developer.twitter.com](https://developer.twitter.com/en/docs/twitter-api/premium/account-activity-api/guides/account-activity-data-objects) 

所以，让我们开始构建 OpenSear-API 吧。为此，让我们使用 NodeJS 和 ExpressJS，因为在任何地方使用 [JavaScript 都很酷。然而，如果我继续谈论如何使用 Node 和 Express 创建 API，这将变得极其冗长，更不用说令人厌烦了。要学习如何轻松创建 API，您可以遵循](https://medium.com/swlh/javascript-in-space-7855a7ecf81b)[我的 Node 指南是简单文章系列](https://medium.com/swlh/node-is-simple-part-1-b87a8dc390c7)。

长话短说，让我们创建这个文件夹结构。

```
.
├── controllers
│   └── index.js
├── errors
│   └── index.js
├── keys
│   └── index.js
├── middleware
│   └── index.js
├── models
│   └── index.js
├── services
│   └── index.js
├── utilities
│   ├── async-wrapper.d.ts
│   └── async-wrapper.js
└── index.js
```

`/index.js`文件是我们 Express API 的入口点。让我们如下创建它。

[/index.js](https://github.com/Niweera/opensear/blob/main/index.js)

现在我们需要创建`controllers`和`services`，它们将处理 API 请求。

[/控制器/index.js](https://github.com/Niweera/opensear/blob/main/controllers/index.js)

在这个控制器文件中，我们定义了 API 端点`GET /callback`和`POST /callback`，相关的服务将在一个服务文件中创建。`asyncWrapper`用于创建一个包装器来捕捉错误，并使用我们自己的错误处理中间件来处理这些错误。

[/utilities/异步包装器. js](https://github.com/Niweera/opensear/blob/main/utilities/async-wrapper.js)

为了简化开发，我们可以使用数据类型声明。

[/实用程序/异步包装器. d.ts](https://github.com/Niweera/opensear/blob/main/utilities/async-wrapper.d.ts)

现在让我们创建`validator`中间件。

[/中间件/验证器. js](https://github.com/Niweera/opensear/blob/main/middleware/validator.js)

让我们创建`models`文件。

[/models/index.js](https://github.com/Niweera/opensear/blob/main/models/index.js)

对于模型验证，我们使用最强大的模式描述语言和 JavaScript 数据验证器💪。我们从[文档](https://developer.twitter.com/en/docs/twitter-api/premium/account-activity-api/guides/account-activity-data-objects)中得知，Twitter 账户活动 API 发送的每个有效载荷都有一个`for_user_id`密钥。所以我们把它作为验证来检查。(这不是必需的，但我添加这个只是为了我可以使用`joi`包。)此外，我将使用定制的中间件为这些端点增加一层安全性。现在让我们创建`middleware`函数来处理安全性和其他重要的 Express 特性。

[/中间件/index.js](https://github.com/Niweera/opensear/blob/main/middleware/index.js)

**中间件；不能和他们一起生活，不能没有他们。**

让我们讨论一下这里使用的中间件。

1.  Body-parser——Body parser 中间件用于解析请求体，并将其附加为`req.body`。

2.mor gan—mor gan(Dexter mor gan)是一个用于 NodeJS 的 HTTP 请求记录器中间件。

3.cors——当您需要指定哪些资源可以访问该 API 时，可以使用 CORS 包。这也被称为跨来源资源共享。

[](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) [## 跨来源资源共享(CORS) - HTTP | MDN

### 跨源资源共享(CORS)是一种基于 HTTP 头的机制，允许服务器指示任何源…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 

4.头盔-头盔用于通过设置各种 HTTP 头来保护 Express 应用程序。

现在让我们转到最重要的中间件。`TwitterValidator`。

[/中间件/twitter-validator.js](https://github.com/Niweera/opensear/blob/main/middleware/twitter-validator.js)

在这个`TwitterValidator`中，我们检查 GET /callback 端点是否被真正的 Twitter API 命中。为了帮助我们做到这一点，Twitter API 为我们提供了`x-twitter-webhooks-signature`头，我们可以在那里检查收到的数据。

安全性；那又怎么样？

1.  保护`GET /callback`端点

正如官方指南中提到的那样，我们所要做的就是获得`crc_token`查询参数和`nonce`查询参数的值，然后创建下面的字符串。

```
`crc_token=${token}&nonce=${nonce}`
```

然后我们需要使用 HMAC·SHA-256 算法用上面的字符串和 TWITTER_CONSUMER_SECRET 创建散列。然后我们将这个散列与来自`twitter-webhooks-signature`头的值进行比较。(这里我没有使散列比较时间攻击安全。从理论上讲，定时攻击可能会泄露两个散列的类型和长度信息，但不会泄露它们的真实值。)如果匹配，那么我们可以说 GET 请求已经被 Twitter 发送了。如果不是，就是有人在试图滥用你的 API！

2.固定`POST /callback`端点

这是最棘手的部分，我挣扎了很久。从来没有人告诉我(我指的是[官方指南](https://developer.twitter.com/en/docs/twitter-api/enterprise/account-activity-api/guides/securing-webhooks))我应该使用原始缓冲体来获取 hash 以检查`twitter-webhooks-signature`值。我试了一次又一次，试图对照`JSON.stringify()`(纤维化)的身体来检查，但毫无效果。所以后来我发现，在应用`bodyParser.json()`中间件之前，我必须使用原始体。所以我为`bodyParser`中间件创建了`verifyPostRequest`定制验证器。它所做的是使用 HMAC SHA-256 算法创建原始主体和 TWITTER_CONSUMER_SECRET 的散列，并对照`twitter-webhooks-signature`值检查该散列。如果匹配，那么我们可以说 POST 请求是由 Twitter 发送的。如果不是，有人试图滥用您的 API😂！

既然所有其他的中间件都被处理了，让我们继续讨论另一个非常重要的中间件。让我们来构建`ErrorHandling`中间件。

[/中间件/错误处理. js](https://github.com/Niweera/opensear/blob/main/middleware/error-handling.js)

在错误处理中间件中，我们检查由我们的`asyncWrapper`捕获的错误是否是我们在`errors/index.js`中创建的错误，如果不是，我们使用默认的错误处理程序来处理它。下面是我们定义自定义错误类的`errors/index.js`文件。

[/errors/index.js](https://github.com/Niweera/opensear/blob/main/errors/index.js)

现在我们已经完成了 OpenSear-API Express 应用程序的几乎所有基本部分，让我们继续创建在我们前面定义的控制器中使用的服务。

[/services/index.js](https://github.com/Niweera/opensear/blob/main/services/index.js)

在我们的服务文件中，我们处理两个端点。

1.  `GET /callback`端点——我们创建了`services.getHandler()`函数，这样我们就可以创建 CRC 令牌并将其发送回 Twitter API。为此，我们创建了一个对象，

```
{
  response_token: `sha256=${hmac}`
}
```

其中`hmac`是使用 TWITTER_CONSUMER_SECRET 和来自 Twitter API 的查询字符串提供的`crc_token`创建的，并像我们之前所做的那样创建一个散列。Twitter 账户活动 API 将像下面这样到达我们的`GET /callback`端点。

```
/callback?crc_token=$token&nonce=$nonce
```

因此，如果 Twitter API 接受我们的`response_token`有效，那么我们就成功了。我们可以通过 webhook 监听器监听 Twitter 事件。

2.`POST /callback`端点——我们创建`services.postHandler()`函数来检查我们想要关注的 Twitter 事件是否真的发生了。在我的例子中，我检查 Twitter 事件是否是我创建的 tweet，并且它在 tweet 正文中包含单词`wordle`。如果 Twitter 事件是我要找的，我会在这里排队找工作。我在这里省略了作业排队功能，因为这取决于您对作业排队的想象。对于这项任务，我建议通读以下内容。

[](https://openbase.com/categories/js/best-nodejs-job-queues-libraries) [## 2022 年 10 大最佳 Node.js 作业队列库| Openbase

### 2022 年 10 个最佳 Node.js 作业队列库对比:batch-cluster、generator-hapi-api-stack、kinetik……

openbase.com](https://openbase.com/categories/js/best-nodejs-job-queues-libraries) 

现在我们已经完成了 OpenSear-API 的实现。我们可以通过运行我在上一篇文章中提到的以下命令来检查这一点。

```
$ node --experimental-specifier-resolution=node twitter-cli.js create-webhook$ node --experimental-specifier-resolution=node twitter-cli.js create-susbscription
```

首先，我们创建了 webhook，然后我们为 Twitter 用户添加了订阅。然后我们可以创建一条包含单词`wordle`的 tweet。至于 OpenSear 系统，我们现在已经完成了前半部分。现在我们可以使用 OpenSear-API 基于 tweet 对作业进行排队。

但是我们需要一个工人来承担这项工作，铸造和出版 NFT。我将把它留到本系列的第 3 部分。所以让我们在下一篇文章中继续我们的讨论，我将讨论我如何创建用于处理作业的 OpenSear-Worker。

[](https://github.com/Niweera/opensear) [## GitHub-Niweera/open sear:open sear 是一个帮助我创建并列出我的 NFT 的系统…

### OpenSear 是一个帮助我在 opensea.io marketplace 上创建和列出我的 NFT 的系统。看看我的 NFT 收藏吧

github.com](https://github.com/Niweera/opensear)
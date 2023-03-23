# 如何记录大量的请求

> 原文：<https://betterprogramming.pub/how-to-log-guzzle-requests-fbc16c99a8fd>

## 更新中间件的简单方法

![](img/1c9653d5c783560083c0d5c0908a5bc9.png)

当您构建 API 消费者时，您应该记录您的 API 请求和响应。否则，会让人觉得你在盲目工作。

我将解释如何配置一个 Guzzle 中间件来记录对文件的请求。这对于调试和历史记录非常有用。

这样做的方法是用我们的日志中间件传递一个新的`HandlerStack`到我们的`Client`实例中。

创建新的`Guzzle`实例的语法如下:

```
$client = new \GuzzleHttp\Client([
    'base_uri' => "[http://www.example.com/api](http://www.example.com/api)",
    'handler' => $handlerStack,
    ]),
]);
```

`$handlerStack`将最终持有处理日志记录的中间件。

# 设置日志文件

在设置中间件之前，我需要实例化处理将日志消息写入文件的东西。我将使用 Monolog 包来处理这个问题。这很容易达到我想要的标准，在我的默认`laravel.log`旁边有一个每日日志文件。

在我的`AppServiceProvider`中，我将设置一个助手函数来创建记录器:

# 设置中间件

Guzzle 的日志中间件需要一个日志记录器(我刚刚在上面创建的)和一个控制记录内容的`MessageFormatter`实例。

我将设置一个助手函数来创建中间件:

```
private function createGuzzleLoggingMiddleware(string $messageFormat)
{
    return \GuzzleHttp\Middleware::log(
        $this->getLogger(),
        new \GuzzleHttp\MessageFormatter($messageFormat)
    );
}
```

`$messageFormat`是一个字符串，它“使用变量替代请求、响应和其他事务数据来格式化日志消息。”可能的替代物[可以在这里看到](https://github.com/guzzle/guzzle/blob/master/src/MessageFormatter.php#L12)——但是我想出了一些方便的替代物:

记录请求:{ method } { uri } HTTP/{ version } { req _ body }
记录响应:响应:{code} — {res_body}

# 创建句柄堆栈

既然我已经有了一种方法来轻松地创建 Guzzle 日志中间件的实例，我可以创建一个新的`HandlerStack`来将中间件推送到:

```
$handlerStack = \GuzzleHttp\HandlerStack::create();$handlerStack->push(
    $this->createGuzzleLoggingMiddleware($messageFormat)
);
```

# 分别记录请求和响应

我决定将请求和响应记录在两个独立的日志条目中。似乎实现这一点的唯一方法是将多个日志中间件推到风口浪尖，所以我设置了一个 helper 函数来创建一个`HandlerStack`,其中包含我想要记录的一组消息格式字符串中所有需要的日志中间件。

我们做到了！现在，向我们的 Guzzle 客户端添加多个记录器就像下面的代码一样简单:

```
$client = new Client([
    'base_uri' => "[http://example.com](http://example.com)",
    'handler' => $this->createLoggingHandlerStack([
        '{method} {uri} HTTP/{version} {req_body}',
        'RESPONSE: {code} - {res_body}',
    ]),
]);
```

# 关于处理大吃大喝反应的简短说明

在设置了日志记录器之后，我意识到我不能再访问响应体了——原来这是我的错误。

之前，我使用以下代码行访问响应内容:

```
$this->client->get($endpoint)->getBody()->getContents();
```

但是结果是，这得到了“作为字符串的主体的剩余内容”——并且由于中间件已经访问了主体来记录它，所以没有任何剩余的内容要检索。

访问响应内容的正确方法是将主体转换为字符串:

```
(string) $this->client->get($endpoint)->getBody();
```

```
**Want to Connect?**[Follow me on Twitter](https://twitter.com/MichaelStivala) as I continue to document what I learn on my software-building journey.
```
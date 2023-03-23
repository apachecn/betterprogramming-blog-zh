# 使用 Mitmproxy 和 Httpie 改进 API 开发和测试

> 原文：<https://betterprogramming.pub/using-mitmproxy-and-httpie-to-improve-api-development-and-testing-3c6c8b0b82c9>

## 在测试和开发 API 时，有两个工具帮助我提高了生产率

![](img/50ca3e81b2312e80a8027af4469bc49e.png)

由[亚辛·哈尔法利](https://unsplash.com/@yassine_khalfalli?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在开发 API 时，深入检查每个端点(或查询)是非常重要的。这是出于各种原因，包括安全性、效率、用户体验等等。

我最近在为一个个人项目开发一个 API [，发现自己不得不决定是使用 Postman 还是 Burp Suite。这些都是测试您的终端和篡改它们接收的数据的优秀工具，因此您可以在边缘情况下确保出色的性能和安全性。](https://medium.com/xycloo/stex-our-blockchain-based-latex-editor-with-real-time-compiling-aa020b0fc32c?source=rss------cryptocurrency-5)

然而，即使我已经有了使用这两种工具的经验，我还是决定去寻找更容易定制和更适合我的需求的替代工具，主要是:

*   轻松制作请求
*   修改传出的请求并与之交互
*   评估和过滤轻量级测试的响应

我也不想花时间自己编写易于使用的代理，这是我只在针对非常具体的用例时才做的事情，比如总是自动修改对域`test.testing.com`的传出请求。

在搜索了各种 GitHub repos 之后，我决定使用 httpie 和 mitmproxy(以及 mitmdump)来帮助我开发用于 [sTeX](https://medium.com/xycloo/stex-our-blockchain-based-latex-editor-with-real-time-compiling-aa020b0fc32c?source=rss------cryptocurrency-5) 的 API。

API 的开发尚未完成，但我可以说我对这两个工具的结合感到满意，并希望与读者分享这种体验。

# [HTTPie](https://github.com/httpie/httpie)

我敢打赌，你们大多数人都听说过和/或使用过 cURL。cURL 是 client URL 的缩写，也是一个跨协议传输数据的命令行工具。我喜欢 cURL，并且在开发和安全研究中经常使用它，但是当我开始使用 httpie 时，我明白了为什么创建者认为这样的项目适合与 cURL 竞争。

cURL 被设计成具有更多的功能，但是这些功能在日常 API 测试中很少有用。另一方面，httpie 是一个命令行工具，可以轻松地与 HTTP 或 HTTPS 上的任何东西进行交互，它被设计得尽可能简单，让事情变得像做馅饼一样容易。

它的缺省值确保您在手动创建 API 请求时拥有最流畅的体验，例如，这个 httpie 命令:

```
http http://127.0.0.1 test=data
```

转换为以下 HTTP 请求:

```
POST / HTTP/1.1
Accept: application/json, */*;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Content-Length: 16
Content-Type: application/json
Host: 127.0.0.1{
    "test": "data"
}
```

这种语法非常适合大多数 API，它确保用户在发送请求之前不必输入很长的命令。当然，或者至少在我看来，cURL 仍然是 CLI 请求制作领域的王者，但是 httpie 的简单性和工作流程使它更适合大多数测试任务。

# [MITMProxy](https://github.com/mitmproxy/mitmproxy)

MITM 听起来可能很熟悉，如果不是，让我提醒你:主在中间。这意味着 mitmproxy 实际上位于您的应用程序和它们发送数据的服务器之间。这种行为也被称为代理拦截器，并被所有现代(或非现代)API 测试工具所使用。

在 API 测试中使用这样的工具很简单:在发出请求后，您可以轻松地修改、调整和监控 API 收到的数据。

我专门使用两种工具:

*   mitmproxy
*   mitmdump

## MITMProxy

这个工具将所有东西都保存在内存中，当我想要监控我发出的 httpie 请求时，我会使用它(这些请求通常是一堆请求，是测试 API 不同区域的 bash 脚本的直接结果)。这是我在旅途中使用的工具，用于快速监控 API 如何响应不同的用户输入。例如，您可以根据漏洞有效负载的单词列表来测试 API。感谢 [mitmproxy 的过滤器](https://docs.mitmproxy.org/stable/concepts-filters/)所做的出色工作，我能够有效地监控这些请求。

## MITMDump

另一方面，mitmdump(行为类似于伟大的 tcpdump)将所有流保存在文件中，这使得它更适合于结构化测试，并在以后使用 API 自己的响应来进行进一步的测试。它还支持向请求和响应添加自定义脚本，尽管这是我更喜欢用自己的脚本来完成的事情。

这三个工具合在一起非常适合我。我用 httpie 发出请求，并使用 mitmproxy(或 mitmdump)代理来监控和分析请求和响应。

像 Postman 这样的工具工作得很好，但有时提供了太多我们不需要的功能，缺乏一些定制选项，不是免费和/或开源的，或者我们只是想从 GUI 中解脱出来，切换到命令行。

我个人从现在开始会更多的使用 httpie，mitmproxy 和 mitmdump 组合。可能的话，我还会创建一个简单的后端，以方便地分享结果，并能够像在邮差合作。

感谢阅读！
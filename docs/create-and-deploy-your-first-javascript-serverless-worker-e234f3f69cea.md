# 创建并部署您的第一个 JavaScript 无服务器工作器

> 原文：<https://betterprogramming.pub/create-and-deploy-your-first-javascript-serverless-worker-e234f3f69cea>

## 免费构建和部署无服务器工作器的初学者指南

![](img/6c26cd8b6ee4000fdd5c1d57d80cbf8d.png)

照片由[Viktorija lankauskait](https://unsplash.com/@viktorijalankauskaite?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

上周我写了一篇关于我在使用 JavaScript 时最常用的工具[的文章。Cloudflare 工人和牧马人都在名单上。今天，你将了解我为什么以及如何使用它们。](/my-favourite-tools-for-working-with-javascript-84eb836750b0)

您将学习部署一个无服务器的 worker，这在许多情况下非常有用。

例如，您可以在一个简单的工人请求中压缩 IPFS 锁定，并允许用户上传到 IPFS，同时仍然像在传统后端中一样屏蔽您的 API 键。

我还计划把这个系列做成一个系列，涵盖在 CF workers 上创建 API，并解释诸如处理 CORS、与原生 KV store 交互等主题。如果您真的想构建可伸缩的无服务器 API，请继续关注。

# 什么是 Cloudflare Workers？

先说基础的，什么是 CF 工作者，他们是怎么工作的？

CF workers 提供了一个创建应用程序的环境，而无需维护基础设施。它们直接由 Cloudflare 网络运行，该网络由分布在全球各地的各种服务器组成。此外，运行时提供了在现代浏览器上实现的大多数 API。

作为无服务器的直接结果，它们非常容易设置，并且由于 Cloudflare 的设计，工作脚本也很容易构建。

每当向工作域(或 cf 管理的域)发出请求时，Cloudflare 都会将事件传递给工作域的处理程序。从那里，工作脚本可以按照脚本中的代码计算响应。

# 设置

您首先需要一个 Cloudflare 帐户。然后还得装牧马人(最好版本> 2.0，`npm install -g wrangler`)。Wrangler 是一个 CLI 工具，我们将使用它来测试和发布我们的无服务器应用程序(以及 KV stores 等其他应用程序)。).

现在一切都准备好了，只需要创建一个新项目。为了简单(在这种情况下也是为了效率)，我们将开始一个空白项目。打开你的终端，输入:`wrangler init serverlessworker`，然后进入目录。`wrangler init`命令会询问一些事情，你可以按照自己的意愿去做，也可以按照我在本文中使用的配置来做:`use git? > n, create package.json? > Y, use typescript? > n, create worker at index.js? > fetch bundle` 。

# 基本处理程序

正如我之前介绍的，根据请求，一个事件被传递给我们的 worker 的处理程序。`index.js`文件是处理这个事件的地方。默认情况下，该处理程序如下所示:

```
export default {
 async fetch(request) {
  return new Response("Hello World!");
 },
};
```

您可以看到处理程序将请求作为参数，然后返回一个`Response`。您将总是使用 request 参数，除非您的工作人员只需要执行一项任务，并且每次请求都是相同的任务，这是一种不太可能的情况。

因为我们想要构建一个完成两项任务的 worker，所以我们需要像 URL 模式、头(如果您计划使用身份验证/授权)、主体、方法等数据。

例如，假设我们想要返回一个端点，该端点返回关于我们工人的信息:

您可以看到我们正在查看请求的 URL 是否与端点匹配(为了简单起见，我显式地匹配了整个 URL，而没有构造对象，只对端点进行了 are)。

如果它匹配`/info`端点，它将返回工人信息，如果不是默认的 hello world。我们不打算构建我们的路由器，但是这个例子展示了一个非常基本的 cf worker handler 应该是什么样子。您可以继续用`wrangler dev`测试它(记得将 URL 改为您的 worker)。

# 简单的工人

在这一段中，我们将创建一个非常简单的 worker 函数，并以类似 API 的格式发布。我们将创建一个端点，它用一个数组作为 JSON 主体接受 POST 请求，然后返回规范化的(求和)数组。当然，让一个工人只执行这样的任务是没有效率的，但是这是一个很好的例子来展示一个非常简单的工人如何工作:

*   接收用户数据
*   用修改的数据响应

要做的第一件事是路由我们工人的流量，例如，我们希望`/info`端点返回工人信息，而`/normalize`端点返回规范化数据。为此，我们将使用`itty-router`，您可以使用`npm install itty-router`安装它。这款路由器专为 cf 工作人员打造，完全符合我们的目标。

首先要注意的是，在第 32 行，我们监听我们在介绍性段落中讨论的 fetch 事件，并用路由器的输出进行响应。在第 12 行，我们创建了路由器，然后我们将使用它来构建 URL 和方法模式以匹配请求。注意第 15 行的`/info` GET 端点，以及第 29 行返回 404 错误的通配符端点。

`/normalize`端点只接受 post 请求，它的回调函数使用请求事件作为参数。在第 21 行，我们读取请求的 JSON 主体，然后将`body.array`(这是我们将随请求一起发送的数组)传递给`normalized()`函数。最后，在第 24 行，我们返回一个带有规范化数组的响应对象。

继续运行`wrangler dev`来测试它，在本地主机上启动 worker，并用下面的 JSON 主体创建一个 post 请求:

```
{"array":[1,2,3,4,5]}
```

我将使用 httpie ( [，正如我在本文](/using-mitmproxy-and-httpie-to-improve-api-development-and-testing-3c6c8b0b82c9)中解释的那样):

```
~/.../serverlessworker ❯ http localhost:8787/normalize array=\[1,2,3,4,5\]HTTP/1.1 200 OK
...
{
  "array": [
    0.0625,
    0.125,
    0.1875,
    0.25,
    0.3125
  ]
}
```

现在是时候将我们的工作人员部署到 Cloudflare 网络了，除非您需要更低的延迟或其他自定义功能，否则您将能够免费完成这些工作。

# 发布您的员工

这是最简单的一步，事实上，你只需要做两件事:

1.  用牧马人登录 cloud flare:`wrangler login`
2.  发布工人:在工人的根目录下，`wrangler publish`

```
~/.../medium/serverlessworker ❯ wrangler publish
Delegating to locally-installed version of wrangler @ v2.0.17
⛅️ wrangler 2.0.17 (update available 2.0.19)
-------------------------------------------------------
Retrieving cached values for account from node_modules/.cache/wrangler
Total Upload: 1.61 KiB / gzip: 0.88 KiB
Uploaded serverlessworker (2.73 sec)
Published serverlessworker (6.65 sec)     serverlessworker.tdep.workers.dev
```

现在您已经设置好了，如果您愿意，您可以使用下面的 httpie 命令来测试我的 worker 并规范化一个数组:

```
http serverlessworker.tdep.workers.dev/normalize array=\[1,2,3,4,5\]
```

对于这篇文章来说，这已经足够了，如果你对无服务器工人感兴趣，我建议继续关注，因为我计划就这个主题写更多(和更多中级)的文章。

感谢阅读！
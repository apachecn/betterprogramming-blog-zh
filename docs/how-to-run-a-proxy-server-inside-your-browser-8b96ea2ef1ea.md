# 如何在浏览器中运行代理服务器

> 原文：<https://betterprogramming.pub/how-to-run-a-proxy-server-inside-your-browser-8b96ea2ef1ea>

## 您甚至不需要本地后端—只需要一名服务人员

![](img/00047f62313077e15579c4f0103bd59b.png)

照片由[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当我调试我最近开发的一个视频聊天应用程序时，我不得不在本地运行整个链条。这意味着在本地运行前端——以及与之通信的两个 REST APIs，它们都是用 Java 编写的。

现在，我不是 Java 开发人员，所以让这两个 API 在我的开发机器上运行是一个挑战。最重要的是，我在一个公司代理后面，这个客户要求我在他们提供的 Windows 机器上工作。

我太幸运了。

我通常使用 Node.js 后端，它运行和配置起来要容易得多，但即使是这样，安装和运行起来也会很麻烦。运行一个本地 web 服务器来为您的前端服务是非常容易的，但是当涉及到代理甚至提供模拟响应时，事情很快就变得复杂了。

那么，如何在没有任何后端的情况下在浏览器中运行代理服务器呢？

输入服务人员。

# 一场静悄悄的、被低估的革命

出于某种原因，服务人员从来没有像 React 和 Redux 那样令人兴奋。它们并没有真正的联系，但是它们对前端开发的影响是深远的。

服务工作者通常与渐进式 web 应用程序结合使用，并允许拦截进出网站的所有网络流量。

这意味着你可以拦截你的应用程序发出的任何请求，并以你想要的任何方式响应它。

服务人员的主要用例是使网站离线工作，但是因为您可以拦截网络流量，所以您也可以使用它来运行代理服务器或提供模拟响应，就在您的浏览器中。

# 它是如何工作的

每当您的网站发出请求时，就会在服务人员上分派一个`fetch`事件。在其事件处理程序中，您可以检查请求并采取适当的措施:

```
self.addEventListener('fetch', e => {
  console.log('request: ', e.request);
});
```

`FetchEvent`的`request`属性包含制作的`Request`。您可以检查请求的 URL 和方法(也许还有标题),以确定它是哪种请求。基于此，您可以创建一个`Response`,并将其发送回用户。

为了将响应发送回用户，我们使用了`FetchEvent`的`respondWith`方法，该方法将一个`Promise`解析为一个`Response`。

例如，如果您想在用户离线时为来自您网站的每个请求提供某种错误页面，您可以这样做:

```
self.addEventListener('fetch', e => {
  e.respondWith(Promise.resolve(
    new Response('<h1>Offline</h1>', {
      status: 200,
      statusText: 'OK',
      headers: {
        'Content-type': 'text/html'
      }
    })
  ))
});
```

`Response`构造函数将一个响应体作为它的第一个参数，它可以是字符串、blob 或 buffer 等等，将一个`init`对象作为它的第二个参数，它可以包含一个`status`、`statusText`和`headers`对象。

这让你有能力以任何你想要的方式回应来自你的网站的任何请求。

## 服务工作者作为代理服务器

假设您有一个调用位于`https://api.your.domain/api/v1/*`的 REST API 的应用程序，您需要将所有流量代理到位于`https://api.your.domain/api/**v2**/*`的新版本 API。

您可以使用服务工作器来检查请求的 URL，并在它是对旧版本 API 的调用时代理它:

```
self.addEventListener('fetch', e => {
  const {url} = e.request; if(url.includes('https://api.your.domain/api/v1/') {
    const newUrl = url.replace('/api/v1/', '/api/v2/'); e.respondWith(fetch(newUrl));
  }
});
```

这里，我们只是检查 URL，看它是否包含旧版本 API 的路径，如果包含，我们用新路径替换旧路径，向新 URL 发出请求，并返回响应。

您可以使用相同的方法将请求转发到完全不同的域，例如，将本地运行的 API 的请求转发到在测试环境中运行的 API。

这是一个非常简单的代理服务器，但是您可以想象您可以创建一些非常复杂的逻辑来进行更高级的代理。

## 作为模拟服务器的服务工作者

一个更有趣的用例是使用一个服务工作者作为模拟服务器。这在用 REST API 测试前端时特别有用，因为 REST API 可能还不可用，甚至还没有完全开发出来，或者在运行单元测试时特别有用。服务工作者将再次拦截针对 API 的请求，并提供预定义的模拟响应。

模仿响应可能特别麻烦，因为这意味着运行和维护某种后端只是为了获得正确的响应。当涉及到多个 API 时，这可能意味着在测试感兴趣的前端之前要运行多个终端窗口。

有一个服务人员作为模拟服务器意味着只需启动前端应用程序就可以得到您需要的一切。

假设你有一个博客，你从一个 API 获取帖子。然后你可以这样嘲笑对方的回答:

```
const postings = [
  {
    id: 12345, 
    title: '...', 
    body: '...'
  },
  {
    id: 56789, 
    title: '...', 
    body: '...'
  },
  ...
]; self.addEventListener('fetch', e => {
  const {url} = e.request; if(url.includes('/blogpostings') {
    e.respondWith(
     Promise.resolve(new Response(
       JSON.stringify(postings), {
         headers: {
           'Content-Type': 'application/json'
         }
       })) )
  }
});
```

我们只需定义一个发布对象数组，并将其作为 JSON 响应。

这里的示例只是提供了一个预定义的响应，但是在实际的应用程序中，您可能会根据请求的 URL 和方法在配置文件中查找请求，然后决定将什么作为响应。

您可以在这样的文件中指定所有类型的响应。例如:

*   JSON 响应(如上例所示)
*   错误，如 404 或 500
*   文件—例如，模拟下载
*   重新寄送
*   延迟响应—例如，模拟延迟

这种方法的好处是，您只需要维护一个包含从请求到响应的映射的配置文件。

不再需要运行和维护后端—只需添加服务人员，您就可以开展业务了。

# 如何实现这一切

[我创建了一个 repo，其中包含一个服务人员，负责处理所有这些事情](https://github.com/DannyMoerkerke/sw-proxy)。它包含一个带有说明的自述文件和一个包含各种使用案例的演示页面。

希望这能鼓励你使用服务人员的全部能力，比如缓存、推送通知，甚至让你的网站离线工作。

服务工作者是对 JavaScript 的巨大补充，在所有主流浏览器中都得到支持。没有理由不在你的应用中使用。
# 如何使用 TypeScript 避免达到 API 速率限制

> 原文：<https://betterprogramming.pub/how-to-avoid-hitting-api-rate-limits-using-typescript-8f6ab20aaa69>

## 创建 TypeScript 类以批量发送请求

![](img/14a174b7d5a733433abe90695c8a2f17.png)

照片由 [Aron 视觉效果](https://unsplash.com/ja/@aronvisuals?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/limit?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

速率限制是 API 安全性的重要组成部分，有助于防止恶意活动并减少服务器资源的压力。然而，对于 API 用户来说，它们也会带来麻烦——不仅仅是不完美的代码会带来问题。如果没有适当的策略，合法流量的峰值也会导致可怕的`429 "Too Many Requests"`错误状态。

那么，我们如何确保我们没有发送太多的请求呢？在本文中，我们将研究一个简单的模式，该模式将帮助我们将异步请求分批到间隔中，这些间隔可以配置为我们正在使用的 API 的特定速率限制。下面的示例是使用 TypeScript 编写的。

# 构建类构造函数

当使用速率限制时，我们通常会被限制在某个时间段内的某个数量的请求:比方说，每五秒钟二十个请求。类语法为管理我们的请求提供了一种有用的方式，并允许我们为可能使用的每个 API 创建一个单独的实例。

至少，我们的类应该允许我们设置给定时间间隔内允许的请求数量以及每个时间间隔持续的时间(以毫秒为单位)。

注意，我们还创建了一个属性`queuedRequests`，我们可以用它来跟踪给定时间间隔内排队的请求数量。

以这种方式构建`RequestScheduler`类将允许我们创建具有不同 API 需求的多个实例。例如:

# 添加计划方法

接下来，让我们创建一个`schedule`方法，该方法接受一个函数，如果该函数超过了给定间隔中允许的最后一个函数，则通过`setTimeout`添加一个延迟，以等待新的间隔开始。

# 最后一堂课

我们的整个类可能看起来像这样。在下面的代码片段中，我还为构造函数添加了一个`debugMode`选项，这样我们就可以记录一些关于函数何时被触发的有用信息——以确保该方法正在工作！

# 测试它的工作情况

让我们测试一下我们的代码吧！首先，我们将用相对较少的请求在调试模式下创建一个新的类实例，这样更容易看到发生了什么。

```
const requestScheduler = new RequestScheduler({
  requestsPerInterval: 3,
  intervalTime: 5000,
  debugMode: true,
});
```

接下来，我将创建一个异步函数来模拟 HTTP 请求。

```
async function mockHttpRequest() {
  return new Promise((*resolve*) => {
    resolve("Hello World");
  });
}
```

最后，我将尝试执行这个函数 11 次，将每次执行包装在`schedule`方法中。

```
(async () => {
  for (let i = 0; i < 11; i++) {
    await requestScheduler.schedule(mockHttpRequest);
  }
})();
```

运行这段代码，我们会看到类似如下的日志:

```
RequestScheduler: 0.818ms #1  mockHttpRequest
RequestScheduler: 3.459ms #2  mockHttpRequest
RequestScheduler: 4.751ms #3  mockHttpRequest
*--- RequestScheduler: Wait 5000ms ---* RequestScheduler: 5.007s  #4  mockHttpRequest
RequestScheduler: 5.010s  #5  mockHttpRequest
RequestScheduler: 5.012s  #6  mockHttpRequest
*--- RequestScheduler: Wait 5000ms ---* RequestScheduler: 10.014s #7  mockHttpRequest
RequestScheduler: 10.017s #8  mockHttpRequest
RequestScheduler: 10.019s #9  mockHttpRequest
*--- RequestScheduler: Wait 5000ms ---* RequestScheduler: 15.021s #10 mockHttpRequest
RequestScheduler: 15.024s #11 mockHttpRequest
```

成功！如果你想自己玩这个代码，请查看[这个代码笔](https://codepen.io/BretCameron/pen/ZEoLOgQ)。(一定要打开控制台看日志！)

# 真实世界的用例

这种模式的强大之处部分在于我们可以用它来协调应用程序中的请求。我发现这种方法有用的一个真实例子是在 Next.js 应用程序的构建步骤中，我在构建时静态生成 HTML，部分依赖于第三方 API。

我的网站有一个第三方内容管理系统(用于托管博客文章)和第三方申请人跟踪系统(用于托管工作列表)。每个 API 都有自己的速率限制，对每个 API 发出请求的查询可以在许多不同的文件中找到。此外，Next.js 在幕后控制了很多关于何时以及如何构建页面的信息。

为了处理这个问题，我导出了下面的`RequestScheduler`实例:

```
export const cmsScheduler = new RequestScheduler({
  requestsPerInterval: 10,
  intervalTime: 1_000,
});export const atsScheduler = new RequestScheduler({
  requestsPerInterval: 50,
  intervalTime: 10_000,
});
```

然后在页面的构建步骤中，我可以调用，例如:

```
const blogPosts = await cmsScheduler.schedule(getBlogPosts);
const jobListings = await atsScheduler.schedule(getJobListings);
```

现在，随着网站的扩展，我不再需要担心达到我们的第三方 API 的速率限制！

*感谢阅读！敬请关注更多内容。*
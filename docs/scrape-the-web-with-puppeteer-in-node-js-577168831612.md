# 用 Node.js 中的 Puppeteer 抓取网页

> 原文：<https://betterprogramming.pub/scrape-the-web-with-puppeteer-in-node-js-577168831612>

## 编写不可检测的网页抓取工具

![](img/cb8e76d6f9b0241345df68bade168e0e.png)

变得像这只猫一样不可察觉——照片由 [Milica Spasojevic](https://unsplash.com/@milica_spasojevic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

最近，我想从几个网页上搜集机票价格，以找到最便宜的航班。不幸的是，这些网站有非常好的网页抓取保护，我一直被封锁。

这使我试图建立一个不可检测的网络刮刀。在本文中，我将向您展示我是如何做到的。

# 操纵木偶的人

要从网页中抓取数据，您只需运行一个`GET`请求并解析返回的 HTML。这有两个缺点。

*   JavaScript 内容不会被呈现。
*   该请求很容易被标记为机器人发出的请求。

因为这些缺点，我打算用[木偶师](https://github.com/puppeteer/puppeteer)。木偶师是一个 npm 包，允许你直接控制一个 Chrome 浏览器。

这样，JavaScript 内容将被呈现，我的请求看起来像是来自普通的 Chrome 浏览器。

为了安装 Puppeteer，我运行以下命令。

```
npm install puppeteer
```

对于这个例子，我将在 [Brainyquote 励志引语页面](https://www.brainyquote.com/topics/motivational-quotes)上收集所有的励志引语。

让我们打开浏览器，进入励志名言页面。

为了运行这个，我使用`node`来运行`main.js`文件。

```
node main.js
```

然而，现在这个程序还没有做任何有用的事情。

让我们通过搜索页面上的所有`.b-qt`元素来获取报价。

我使用`page.$$eval`函数来搜索所有的`.b-qt`元素。在回调函数中，我返回元素的`textContent`。

当我再次运行该程序时，我得到以下输出。

```
[
‘It does not matter how slowly you go as long as you do not stop.’,
‘The secret of getting ahead is getting started.’,
‘Optimism is the faith that leads to achievement. Nothing can be done without hope and confidence.’,
‘The will to win, the desire to succeed, the urge to reach your full potential… these are the keys that will unlock the door to personal excellence.’
]
```

我已经开始觉得更有动力了。

# 让傀儡师无法察觉

网络抓取是一个猫捉老鼠的游戏。

一方面，网站不断更新其安全性，以确保没有机器人抓取它们的页面。

另一方面，程序员不断添加更多的隐形功能，使机器人看起来像真正的用户。

对于木偶师来说，有一个[隐身插件](https://github.com/berstend/puppeteer-extra/tree/master/packages/puppeteer-extra-plugin-stealth)实现了很多浏览器隐身的招数。

让我们安装它并将其添加到脚本中。

```
npm install puppeteer puppeteer-extra puppeteer-extra-plugin-stealth
```

就是这样，现在很难发现傀儡浏览器是一个抓取机器人。

# 代理 IPs

如果您设置了一个服务器，并且每五秒钟运行一次这个脚本，那么您很可能仍然会被阻塞。这是因为恰好每五秒钟就有一个来自同一个 IP 地址的请求访问该网站。

为了克服这个问题，您可以设置一个代理 IP 池，并通过不同的代理服务器运行每个请求。这样，检测你的网页抓取器将变得非常困难。

你可以像这样用木偶连接到代理。

现在，这将把应用程序连接到一个代理。

要在每次运行时连接到不同的代理，您应该设置一个代理池，并在程序开始时获取一个新的 IP，如下所示:

现在你几乎不可能被发现了。

# 结论

我展示了如何从网页上抓取数据，同时又很难被发现。

请注意，从网页上抓取数据是一个道德灰色区域，您不应该将它用于受保护或受版权保护的数据。
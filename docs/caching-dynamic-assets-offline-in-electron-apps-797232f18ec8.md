# 在电子应用程序中离线缓存动态资产

> 原文：<https://betterprogramming.pub/caching-dynamic-assets-offline-in-electron-apps-797232f18ec8>

## *为什么我创建了一个定制的 ExpressJS 缓存中间件*

![](img/8e32bdc8d03a4168f842c06d0b5d06b0.png)

[米尔科维](https://unsplash.com/@milkovi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

**通过 npmjs.com****获取资产-文件-缓存-中间件** [**。**](https://www.npmjs.com/package/express-asset-file-cache-middleware)

开发定制的[电子](https://electronjs.org/)应用程序来满足不同寻常的需求有时会带来令人惊讶的收获。当我被委托一个项目时

*   便携式，用于贸易展览，
*   适应性强，可分发给各种客户，以及
*   可扩展，能够部署到数量尚不确定的设备上，

我知道要满足所有这些需求需要很大的创造力。

通常，您会将所有资产与应用程序捆绑在一起并进行复制和分发，但在这种情况下，这不是一个选项，因为我预先知道它们两者都会有许多最后的更改。此外，这个概念需要转移到其他项目，所以我决定用一个无头 CMS，在这种情况下， [Contentful](https://www.contentful.com/) 。

这一技术决策同时提供了

*   适应性，很明显，因为客户可以按照自己的意愿和速度交换内容
*   可伸缩性，因为既然电子应用程序只是简单地打包了一个 Chromium 浏览器，我就只是提供了另一个 web 应用程序，它会在新内容可用时下载新内容。

留给我的是可移植性的问题，因为该软件需要提供真正的离线功能，以防不稳定的互联网连接或其他奇怪的情况。

在这篇文章中，我将概述我处理这种情况的三种连续的方法。

# 第一幕:服务人员

第一步是雇佣服务人员在集成 Chromium 浏览器中预先缓存内容。由于应用程序前端只是一个用 [create-react-app](https://github.com/facebook/create-react-app) 创建的 React 应用程序，并且它打包了 [Workbox](https://developers.google.com/web/tools/workbox) (以 [workbox-webpack-plugin](https://developers.google.com/web/tools/workbox/modules/workbox-webpack-plugin) 的形式)，因此测试它似乎是值得的。

在 create-react-app 中，这种行为是选择加入的，所以需要将`index.js`中的`serviceWorker.unregister()`改为`serviceWorker.register()`:

```
*// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers:* [*https://bit.ly/CRA-PWA*](https://bit.ly/CRA-PWA)serviceWorker.unregister();
```

当我这样做的时候，通过检查 DevTools，很快就发现这并没有像预期的那样工作。事实证明，服务工作者配置`create-react-app`创建的预缓存仅限于 JavaScript 文件和样式表。因为我需要缓存资产，而且是远程资产，所以需要一种不同的方法。

所以我需要更深入地研究 webpack 配置，这只有通过从`create-react-app`弹出才有可能。我的方法是创建一个 git `release`分支并在那里弹出，这样当我调整配置时，团队中的每个人都可以继续开发一个未弹出的版本。

媒体文件最好在运行时缓存，而不是在应用程序加载之前缓存(预缓存)，因为它们会大大延迟应用程序的初始启动时间。此外，由于客户端可以通过 headless CMS 更新内容，这相当于编写一个爬虫来预缓存所有的媒体文件，这是不可行的。

这就是为什么我求助于配置`webpack.config.js`的`GenerateSW`部分的`runtimeCaching`部分:

工具箱-web pack-插件运行时缓存配置

在我们在接近生产的环境中进行测试之前，这种方法似乎是可行的，例如，使用真实的资产数量和规模。添加越来越多的资产，很明显，运行时缓存中的项目被逐出并从 CDN 服务器加载，而它们本应该从缓存中取出。出于对正在发生的事情的好奇，我得出的结论是，对于服务工作者缓存的大小，必须有一定的磁盘配额。这具有直观的意义，但是对于这种没有记录的限制，只有很少的在线信息:

> “规范中没有定义可用于服务工作线程缓存的数量。根据设备和存储条件的不同，流量会因浏览器而异。”([https://love 2 dev . com/blog/what-the-service-worker-cache-storage-limit/](https://love2dev.com/blog/what-is-the-service-worker-cache-storage-limit/))

参见此图，了解如何根据可用(空闲)磁盘空间计算限制:[https://love 2 dev . com/img/consensus-storage-capacity-1697 x666 . png](https://love2dev.com/img/consensus-storage-capacity-1697x666.png)

因此，使用标准的英特尔 NUCs，您通常会以<30 GB (free disk space), which gives you only about 500 MB of storage space. Download a few HD video files, and you’re already over your quota.

# Act 2: Disk Cache Using Express Middleware, First Attempt

Whether or not this was really the root cause for the evictions taking place, it was certainly an unsatisfying situation for our use case. Since a large amount of disk space remained unused, and there would not be a single other app running on the NUCs designated for this project, I decided to go ahead and try to solve that on Electron’s main process, more precisely in the supporting ExpressJS server.

So I started researching existing middlewares but didn’t find one matching our requirements. Most of them, like [http-代理-中间件](https://github.com/chimurai/http-proxy-middleware)告终，它们是为缓存或代理 JavaScript 文件、SVG 图形或样式表等文本资产而定制的。

在我继续描述我实现的解决方案之前，让我们更仔细地看看问题是由哪些组件组成的:

*   对 Contentful 的资产 CDN 的请求必须由 Express 服务器代理，才能被缓存。
*   资产应该缓存到唯一的幂等路径。
*   缓存目录和缓存键应该是可配置的，但有一个后备。
*   内容类型和大小应该可以从缓存中恢复。

第一个要求很容易通过定制快捷路线来满足:

资产代理路由，包括对文件缓存中间件的调用

惟一的缓存路径可以通过散列缓存键来构造(很明显，它对于资产必须是惟一的)。为了提供更快的文件系统访问，缓存的资产存储在两个目录级别的深度，其名称也通过对哈希进行除法和模除来检索:

资产缓存路径算法

然后，中间件试图在指定的路径上找到缓存的资产。如果成功，它将在一个`res.local`变量中传递它，否则将它写入路径，然后传递它。我们应该为一种边缘情况做好准备:如果`fs.writeFileSync`在写入磁盘的过程中失败，我们应该删除文件，以避免出现包含部分数据的缓存条目。

基本缓存算法

细心的读者会注意到这个版本的中间件并不是真正的离线解决方案，因为有一个必要的`HEAD`请求，甚至在缓存的执行路径中，来确定资产的内容类型和长度。当然，这并不是一个理想的解决方案。

# 动作 3:编码和存储元数据

因此，我们需要一种方法来存储与每个资产相关的元数据。一种方法是引入数据库，无论是 SQLite 还是平面文件。然而，这将会干扰实现可移植解决方案的目标。因此，我们没有这样做，而是决定将该信息以 base64 编码格式存储在文件名本身中:

在资产文件名中编码元数据

这非常有效，如果是为了这个项目，我们会称之为一个总结并完成它。因为我已经预见到这可能对其他未来的项目有用(我的或者甚至是其他感兴趣的项目)，所以我考虑是否可以将它提取到自己的 npm 包中([，我最终做了](https://www.npmjs.com/package/express-asset-file-cache-middleware))。然而，要做到这一点，最终需要在元素塞满设备硬盘之前将其清除。

# Coda:最近最少使用的驱逐策略

目前，这是通过设置缓存目录大小的硬性上限来实现的。要做到这一点，需要两个独立的递归操作:

LRU 缓存驱逐算法

首先，每当一个新文件被写入缓存时，用`cacheDir`和`maxSize`调用`evictLeastRecentlyUsed`(以字节为单位)。如果文件夹大小(使用 [get-folder-size](https://www.npmjs.com/package/get-folder-size) )超过了最大大小，它将找到最近最少使用的资产并递归地删除它，直到文件夹大小缩小到足够大。

`findLeastRecentlyUsed`函数本身也被递归调用，因为我们需要遍历所有当前目录，并找到具有最早访问时间的目录(`atime`)。

最后(要点中没有显示，但是可以随意地[浏览源文件](https://github.com/julianrubisch/express-asset-file-cache-middleware/blob/c5eb3f84f17249ab29b17b138c7123bf950d6f26/index.js#L147)，每当中间件读取并返回一个文件时，我们必须`touch`它，以更新`atime`。

# 结论

这留下了最后一个有待回答的问题。为什么我选择实现自己的解决方案，而不是从一堆现成的 npm 包中拼凑出一个中间件，比如带有[缓存管理器](https://www.npmjs.com/package/cache-manager)和适当的[后端](https://github.com/hotelde/node-cache-manager-fs)的 [express-cache-middleware](https://www.npmjs.com/package/express-cache-middleware) ？

在这样的决定中总会有权衡，所以让我简单总结一下我的考虑:

首先，你在一个项目中凝聚的依赖越多，你就越有可能在未来的夜晚试图解决一个不兼容的问题，这个不兼容问题突然出现在一个你可能甚至不知道你已经存在于你的树中的依赖中。下面是桑迪·梅斯的一句名言:

> "依赖那些变化比你少的东西."

对于 JavaScript 包，我敢说它们比你的软件变化更频繁的可能性并没有那么低。

第二，就像代码中的变量一样，每个项目都有一个范围和生命周期。对于这个特殊的项目，很明显会有其他客户的迭代；甚至某种产品化的服务也在讨论之中。如果是一次性的，我可能会毫不费力地混合和匹配现成的模块，但所有已经存在的定制(如 LRU 驱逐)可能会让我陷入配置兔子洞，可能会导致我不得不推出自己的结论。

显然，这完全是你自己的喜好和/或经验的问题。在许多项目中，你没有时间和金钱来做这样的定制实现，或者很可能有一个客户不听你的建议，以不受管理的依赖形式聚集技术债务迟早会回到你身边。至少，我会花时间对候选包进行彻底的调查，以了解它们的开源维护状态。

# 链接

[https://www . npmjs . com/package/express-asset-file-cache-middleware](https://www.npmjs.com/package/express-asset-file-cache-middleware)

[https://create-react-app . dev/docs/making-a-progressive-we b-app/](https://create-react-app.dev/docs/making-a-progressive-web-app/)

[https://developers.google.com/web/tools/workbox](https://developers.google.com/web/tools/workbox)
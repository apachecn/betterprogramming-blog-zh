# 如何让视频在 Safari 中与盖茨比和服务人员一起工作

> 原文：<https://betterprogramming.pub/how-to-get-videos-to-work-in-safari-with-gatsby-and-service-workers-9e1f099249ac>

## 向站点添加服务人员会中断 Safari 和 iOS 设备中的视频

![](img/eb231f6420f761a16cd2a6c923a17f3a.png)

托马斯·罗素在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我正在做一个网站，在那里[英雄组件](https://en.wikipedia.org/wiki/Hero_image)有视频，在添加`[gatsby-plugin-offline](https://www.gatsbyjs.org/packages/gatsby-plugin-offline/)`之前，一切都很好。我做了一些浏览器测试，所有浏览器似乎都没问题，视频也没问题。然后，我决定是时候添加服务人员，并使网站可安装和离线工作。我添加了插件，并用 Chrome 和 Android 测试了一切。一切都是应该的！但后来我用 iPad 打开，看到视频根本不播放，甚至不加载。

这看起来很奇怪，因为视频是用`<video>` HTML 标签实现的，它们是标准的 MP4 文件。幸运的是，我只添加了服务人员，所以我开始怀疑这与此有关。

我看到了杰里米·凯斯的文章，他描述了自己如何遇到同样的问题。他提到了一个解决方案，并在 Phil Nash 的[帖子中做了更深入的解释。看起来 Safari 需要服务人员来支持*字节范围请求*来播放媒体。正如 Safari 的](https://philna.sh/blog/2018/10/23/service-workers-beware-safaris-range-request/)[文档](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariWebContent/CreatingVideoforSafarioniPhone/CreatingVideoforSafarioniPhone.html#//apple_ref/doc/uid/TP40006514-SW6)所说:

> 为 iOS 托管媒体文件的 HTTP 服务器必须支持字节范围请求，iOS 使用该请求在媒体回放中执行随机访问

他们都用不同的方法来解决问题。Phil 修复了服务人员缓存视频文件的问题，但是 Jeremy 选择总是从网络上加载视频，从不缓存。

我将向您展示如何用`gatsby-plugin-offline`实现这两种解决方案。

# 从缓存中获取视频

`gatsby-plugin-offline`使用[工具箱](https://developers.google.com/web/tools/workbox)生成维修工人。幸运的是，对于如何提供视频和音频，Workbox 已经有了一个[高级配方](https://developers.google.com/web/tools/workbox/guides/advanced-recipes?authuser=0#cached-av)。这正是我们将如何实现它。我们只需将它添加到 Gatsby 生成的服务工人中。

首先，我们需要将属性`crossOrigin="anonymous"`添加到我们的 HTML `<video>`标签中:

```
<video src="movie.mp4" **crossOrigin="anonymous"**></video>
```

其次，我们创建一个文件，该文件将被附加到生成的服务工作者文件中。姑且称之为`sw-range-request-handler.js`。我们将把它放到项目的根文件夹中。

该文件的内容将是:

我们匹配所有请求的 MP4 文件，并使用`CacheFirst`策略从缓存中搜索视频文件。如果没有缓存匹配，那么文件将从网络上提供。

如果你看看[工具箱高级配方](https://developers.google.com/web/tools/workbox/guides/advanced-recipes?authuser=0#cached-av)中的例子，你会发现插件函数的用法有点不同。这是因为到目前为止，`gatsby-plugin-offline`使用的是 Workbox v.4，但是这个例子是针对 v.5 的。

您将看到我们也没有从 Workbox 导入任何函数。这是因为我们只将我们的文件内容添加到生成的服务工作者文件中，其中所有的插件都已经添加到了`workbox`对象中。

当`gatsby-plugin-offline`更新到 Workbox 的 v.5 时，我们需要更新我们如何使用插件:

现在我们需要使用`gatsby-plugin-offline`配置中的`[appendScript](https://www.gatsbyjs.org/packages/gatsby-plugin-offline/#available-options)`选项将我们的文件附加到服务工作者。将`options`对象添加到`gatsby-config.js`:

```
{
  resolve: `gatsby-plugin-offline`,
  **options: {
    appendScript: require.resolve(`./sw-range-request-handler.js`),
  }**,
},
```

现在运行`gatsby build`命令并查看`public/sw.js`文件，你会看到在最后是我们的代码。更新服务人员后，在 Safari 和 iOS 设备中显示视频将再次工作。

但这将我们所有的视频缓存到缓存 API 存储中。当你有很多文件或者文件很大时，从用户的设备上占用那么多空间可能不是一个好主意。当用户离线时，视频真的那么重要吗？

# 从网络获取视频

要从不将视频缓存到存储器，而只从网络上获取，您需要覆盖 Workbox 配置。这可以通过在`options`对象中覆盖`[workboxConfig](https://www.gatsbyjs.org/packages/gatsby-plugin-offline/#overriding-workbox-configuration)`对象来实现。

首先，我们仍然应该将`crossOrigin="anonymous"`属性添加到我们的 HTML `<video>`标签中:

```
<video src="movie.mp4" **crossOrigin="anonymous"**></video>
```

其次，我们修改`gatsby-config.js`文件来覆盖现有的`workboxConfig`:

我们使用`NetworkOnly`策略来获取我们的 MP4 文件。永远不会从缓存中搜索它。

但是请注意，我们的代码现在会覆盖`gatsby-plugin-offline`的默认缓存。最好将它添加到[现有的缓存选项列表](https://www.gatsbyjs.org/packages/gatsby-plugin-offline/#overriding-workbox-configuration)中，这样其他所有内容都将被缓存，并且使用正确的策略。

# 结论

起初，当页面有服务人员时，理解为什么视频不播放是非常混乱的。但是这两个解决方案应该可以解决这个问题。这个问题不仅仅发生在使用 Gatsby 时，其他情况也有解决方案。

如果你正在使用 Workbox，那么看看他们的[高级食谱](https://developers.google.com/web/tools/workbox/guides/advanced-recipes?authuser=0#cached-av)。如果你使用纯服务工作者实现，那么看看杰里米·基思的文章“[Safari 中的服务工作者和视频](https://adactio.com/journal/14452)”或者菲尔·纳什的文章“[服务工作者:小心 Safari 的范围请求](https://philna.sh/blog/2018/10/23/service-workers-beware-safaris-range-request/)”他们对这个问题做了更深入的解释。

谢了。
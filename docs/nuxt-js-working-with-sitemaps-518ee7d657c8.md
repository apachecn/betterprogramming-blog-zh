# 在 Nuxt.js 中使用站点地图

> 原文：<https://betterprogramming.pub/nuxt-js-working-with-sitemaps-518ee7d657c8>

## 创建路由、页面和动态路由的站点地图

![](img/0aa998a0f12a1329903c5f608a012ed4.png)

Nik Shuliahin 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

嘿，读这篇文章的人！在这篇文章中，我将研究使用[站点地图模块](https://github.com/nuxt-community/sitemap-module)在 [Nuxt](https://nuxtjs.org/) 中创建站点地图的不同选项。

如果你想创建一个包含所有路线和页面的站点地图，并添加动态路线，请求你的后端 API，或者甚至你想用一个站点地图索引文件创建多个站点地图，这个模块将为你提供你想要的一切。

# 内容

*   站点地图模块选项。
*   带路线的简单单一网站地图。
*   从 API 请求的站点地图。
*   多个索引的站点地图。
*   多语言网站的网站地图。

# 模块选项

以下是所有可用的选项:

[](https://github.com/nuxt-community/sitemap-module#sitemap-options) [## nuxt-社区/站点地图-模块

### 为 Nuxt.js 项目自动生成或提供动态 sitemap.xml！模块基于 awesome sitemap.js…

github.com](https://github.com/nuxt-community/sitemap-module#sitemap-options) 

*   `route` (array | function)
    您可以在那里手动添加路线，以明确地放入站点地图，或者使用该属性从 API 请求路线，这样它就可以从您的 DB 中动态生成。

获取路由的静态路由属性和函数

*   `path` (string)
    这是站点地图的路径。默认为`/sitemap.xml`表示正常，如果使用`sitemaps`属性则为`/sitemapindex.xml`。
*   `hostname` (string)
    如果您定义了`hostname`，它将被所有路由覆盖，而不管应用程序域。模块逻辑是，如果没有定义主机名，应用程序检查当前请求是否是 HTTPS，并获取当前应用程序域。然后，模块在此基础上构建一个`hostname`。如果你使用 [nuxt-i18n](https://github.com/nuxt-community/nuxt-i18n) 并且你的应用有几个域，模块应该根据请求域明确定义当前应用。
*   `cacheTime`(数字)
    这是你之前定义的 sitemap `routes`的缓存 TTL。
*   `exclude` (string[])
    在这里，您可以为您的 Nuxt 应用程序排除一些静态路由:

使用通配符排除

或者，特别是对于站点地图索引，当您不想为每个站点地图复制静态路由时，您可以这样做:

排除静态路由

*   `filter` (function)
    你可以通过一个函数来过滤 Nuxt 从你的路线中自动生成的所有选项。示例:[https://github . com/nuxt-community/sitemap-module # filter-optional-function](https://github.com/nuxt-community/sitemap-module#filter-optional---function)
*   `gzip`(布尔)
    Zip 与否，由你决定。

# 带路线的简单单一网站地图

所以，通常如果你有自己的作品集网站或类似的网站，你会希望生成一个只有静态路线的网站地图。假设您的网站有这样的结构:

页数:

*   博客
*   项目
*   邮件
*   等等。
*   `Index.vue`

最后，你只想把它放在网站地图里。您可能会得到这样的结果:

简单的站点地图配置

# 带有动态路线的网站地图

我说的动态路由是什么意思？这里有一个类似`/blog/_post.vue`的路线，当你有一个页面的元素或者别的什么的时候。

当然，在这种情况下，模块不能自己生成这些 id，这时`routes`属性就派上用场了:

带有动态路线的网站地图

# 几个指数

现在，有了一些静态路由和动态路由，也许为它们建立单独的站点地图是有意义的。

当你有很多动态 URL，并且你想把它们放在不同的站点地图中时，这就变得更加方便了。下面是我的实现:

实现一个函数来创建多个站点地图

在后端，基于`index`参数，您需要更改`OFFSET`请求元素。

# 多语言网站的网站地图

这里，唯一需要担心的细节是不要设置`hostname`，这样域将从到达 Nuxt 服务器的请求中获取。

另外，如果你的域名托管是通过`CNAME`或`Load balancer`，那么到达 Nuxt 的请求将不是 HTTPS，而是简单的 HTTP。

在这种情况下，您需要确保您的`x-forwarded-proto`在请求中被设置为 HTTP。然后，该模块将识别出原始请求是 HTTP，并将一个 HTTPS 链接放到站点地图上。

在 [clusterjobs.de](https://clusterjobs.de) 上，我们有这个选项，当我们需要一个动态的多索引站点地图，并对每个语言域做出响应时。最后，这个模块非常方便。我一年前开始使用它，只有路线和静态路线选项，它成长了很多。

希望它是有用的，它改进了您的 Nuxt 应用程序，或者鼓励您使用这个令人难以置信的框架！
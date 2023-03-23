# 用 Next.js 提升你网站的搜索引擎优化

> 原文：<https://betterprogramming.pub/boost-the-seo-of-your-websites-with-next-js-2ea29f5ae67>

## 如何添加关键字、元标签等

![](img/b5cc170fc05b58a57b0710ddc6e03ac8.png)

由 [Merakist](https://unsplash.com/@merakist?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄

**在 https://twitter.com/antondevv****的推特上关注我，保持反应和打字的前沿。**

享受中等？点击 [**此处**](https://medium.com/@anton.franzen/membership) 可获得 [**对介质**](https://medium.com/@anton.franzen/membership) 的完全访问权限

很快你的网站就可以被抓取了，你的搜索引擎优化也组件化了，还有如何使用 robots.txt 选择哪些页面不应该被抓取

next.js 的工作方式也是 SSR 的工作方式是这样的:页面上的所有内容、所有部分、标题段落等都显示在网页的源代码中，使搜索引擎爬虫可以访问它。

对其工作原理的简短解释是，当您为生产构建 web 应用程序时，所有的 Html 页面都会生成，稍后当您向 Html 页面发出请求时，它已经预先呈现为来自服务器。

但是如果我们做一个客户端 SPA，页面的内容不会在源代码中显示。您将看到生成内容的主要元素。

# 那么我们如何在 next.js 中做 SEO 呢？

Next.js 有一个叫做`next/head`的东西，它允许我们在页面的头部添加元素，比如标题、作为关键字的元标签等等。这是一件非常好的事情，因为这意味着我们可以使 web 应用程序 SEO 友好，特别是当所有内容都显示在源代码中时。

# 下一个/头

使用此`import Head from 'next/head'`到任何页面。

在示例`return()`中的`index.js`中，我们可以添加`<Head>`

在它里面，我们可以添加`<title> <meta>`等，它看起来像这样:

但我们不想在每一页都导入 Head 并这样做。所以还是做个组件吧。在组件文件夹中创建`Meta.js`并添加:

现在我们可以在我们的任何文件中使用这个组件，并将我们想要的道具传递给它！好极了。我们也可以设置`defaultProps`如果我们愿意，我加了一些。

当然，我们所有的组件和页面都应该是语义 html 元素，图片应该有`alt=””`关键字，等等。这是专门针对 next.js 的，关于我们如何通过为页面添加关键字和其他元标签来提高 SEO，但是我们在构建应用程序时需要考虑这些事情。默认情况下，`lang=””`设置为英文，所以如果我们的网站是英文的，我们不必做任何更改。

此外，当你在你的网络应用上工作时，你可以在谷歌浏览器上点击 Lighthouse 和 generate 进行审计。这将告诉你你的网站和搜索引擎优化的分数。我之前用 next.js 为一个客户做了这个，审计显示 100%的搜索引擎优化。它还可以告诉你，如果你错过了一些可以使搜索引擎优化更好。

最后但同样重要的是，我们如何选择哪些页面不应该被搜索引擎抓取？假设我们有一个账户页面，我们可能不希望搜索引擎抓取它。

首先，在你的公共文件夹中创建一个名为`robots.txt`的文件。

在该文件夹中添加以下代码:

接下来，你需要安装`npm install sitemap`

在`/api.`中创建一个名为`site-map.js`的文件

将以下代码添加到文件中:

现在，如果你转到`url/api/site-map`，你会看到一个 XML 文件，向网络爬虫指定它应该爬什么，什么是重要的，等等。我们指定我们的根路由具有最高优先级，我们可以添加我们想要被抓取的所有路由，我们也可以指定动态路由作为 block posts 等，但这相当复杂，所以你可以在这里了解更多:[https://www.npmjs.com/package/sitemap](https://www.npmjs.com/package/sitemap)

这就是我接下来做 seo 的方法，希望能帮到你！
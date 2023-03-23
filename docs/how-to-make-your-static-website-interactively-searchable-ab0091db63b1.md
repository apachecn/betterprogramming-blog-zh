# 如何让你的静态网站可以交互式搜索

> 原文：<https://betterprogramming.pub/how-to-make-your-static-website-interactively-searchable-ab0091db63b1>

## 让我们创建一个客户端搜索索引

![](img/060f3d1e8933c9afebebf2895fbb1136.png)

由[马库斯·温克勒](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

是的，你*可以*在你的静态网站上拥有交互式搜索功能！这里不需要服务器或付费订阅。感谢开源的 [Lunr](https://lunrjs.com/) 和 [Hugo 静态站点生成器](https://gohugo.io/)的强大功能，你只需要一个模板和一些 JavaScript 就可以创建一个客户端搜索索引。

我的一些读者很友好地告诉我，你觉得我的博客很有用，但是有些事情你不知道。在我最近实现搜索功能之前，我一直是自己最不开心的用户。

我的博客供所有人阅读，但它也是我个人的互联网大脑。当我试图重新发现一些我可能有先见之明记录下来的知识时，我经常会打开我写的一篇文章。没有搜索，再次找到它需要几次点击和更多的猜测。现在，我以前的所有发现都唾手可得，随时可以投入到未来更多的工作中。

如果你想让你自己的互联网大脑更有用，下面是你如何在你的静态 Hugo 网站上实现你自己的搜索功能。

# 获取 Lunr

虽然您可以[通过 npm 安装 lunr.js](https://lunrjs.com/guides/getting_started.html) 或者从 CDN 中包含它，但是我选择出售它以最小化对网络的影响。这意味着我将库放在 Hugo 的`static`目录中，从我自己的站点文件中托管它。

你可以通过缩小`lunr.js`来为你的访问者节省一些带宽，我就是通过[从源文件](https://github.com/olivernn/lunr.js)下载 lunr.js 并在文件上使用[JS&CSS Minifier Visual Studio 代码扩展](https://github.com/olback/es6-css-minify)来做到这一点的。这使得大小从 97.5 KB 下降到 39.35 KB，降幅约为 60%。

将此另存为`static/js/lunr.min.js`。

# 创建部分搜索表单

要轻松地将您的搜索表单放在网站上您喜欢的任何位置，请在`layouts/partials/search-form.html`将表单创建为部分模板:

在其他模板中包含您的搜索表单，包括:

```
{{ partial "search-form.html" . }}
```

# 创建搜索页面

为了让你的搜索有用，你需要一个触发搜索的方法。您可以创建一个(静态！)`/search`响应 GET 请求、运行搜索并显示结果的页面。

下面是如何为一个搜索页面创建 Hugo 模板文件，并让它呈现出来。

用下面最少的标记创建`layouts/search/list.html`，假设您是从基础模板继承而来的[:](https://gohugo.io/templates/base)

为了让 Hugo 呈现模板，必须有一个匹配的内容文件。创造`content/search/_index.md`来满足这个要求。文件只需要最少的[前置素材](https://gohugo.io/content-management/front-matter)渲染:

```
---
title: Search me!
---
```

您可以运行`hugo serve`并导航到`/search`来查看是否一切如预期构建。

有几个库可以帮助您构建搜索索引和实现 Lunr。你可以在 Hugo 网站找到它们[。然而，如果你想完全理解这个过程，你会发现，由于 Hugo 的静态站点处理能力，没有额外的依赖，这样做并不复杂。](https://gohugo.io/tools/search/)

# 构建您的搜索索引

下面是如何使用 Hugo 的模板渲染能力为 Lunr 构建一个索引进行搜索。使用`range`循环浏览您想要搜索的页面，并在一系列文档中获取您想要的参数。一种方法是创建`layouts/partials/search-index.html`,使用:

当 Hugo 渲染你的站点时，它会像构建列表页面一样构建你的搜索索引，用它的参数为每个页面创建一个文档。

拼图的最后一块是处理搜索过程的代码:接受搜索查询，让 Lunr 执行搜索，并显示结果。

# 执行搜索并显示结果

创建`static/js/search.js`来保存将它们联系在一起的 JavaScript。这个文件有三个主要任务:获取搜索查询，用 Lunr 执行搜索，并显示结果。

# 用 JavaScript 获取查询参数

感谢`[URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)`，这部分很简单:

```
const params = new URLSearchParams(window.location.search)
const query = params.get('q')
```

# 使用 Lunr 搜索查询

为 Lunr 定义并配置一个[索引。这告诉 Lunr 你想搜索什么，你可以选择更重要的元素。](https://lunrjs.com/guides/getting_started.html#creating-an-index)

然后，您可以使用以下命令执行搜索并存储结果:

```
const results = idx.search(query)
```

# 显示结果

您将需要一个函数来构建一个结果列表，并将它们显示在您的搜索页面上。回忆一下您在`layouts/search/list.html`中赋予`ul`元素的`id`，并将其存储为一个变量:

```
const searchResults = document.getElementById('results')
```

如果一个搜索产生了一些结果，您可以对它们进行迭代，并为每个结果构建一个`<li>`元素:

对于您的每个结果，这会生成一个类似于以下内容的列表项:

```
<li>
<p>
<a href=".../blog/add-search-to-hugo-with-lunr/">
Add search to Hugo static sites with Lunr
</a>
</p>
<p>Yes, you can have an interactive search feature on your static site!...</p>
</li>
```

如果没有结果，则插入一条消息:

```
else {
searchResults.innerHTML = 'No results found.'
}
```

# search.js 的完整代码

下面是`static/js/search.js`的完整版:

# 让它走

现在已经有了 Lunr、搜索索引和显示结果的代码。因为这些都包含在`layouts/partials/search-index.html`中，所以用一个搜索表单在所有页面上添加这个部分。在您的页脚中，放置:

```
{{ partial "search-index.html" . }}
```

你可以通过在我的博客上试用[来看看这是什么样子。](https://victoria.dev/blog)

# 让它跑得更快

因为你的站点是静态的，所以可以把你的搜索索引[预先构建成一个 JSON 数据文件供 Lunr 加载。这就是前面提到的那些库](https://lunrjs.com/guides/index_prebuilding.html)[可能有用的地方，因为 JSON 格式的搜索索引需要在运行`hugo`之外构建，以生成您的站点。](https://gohugo.io/tools/search/)

您可以通过缩小资产和最小化计算开销或在代码中阻止 JavaScript 来最大化您的搜索速度。

# 静态网站也可以搜索！

我希望这能帮助你让你的互联网大脑对自己和他人更有用！如果您今天没有时间实现搜索功能，也不要担心。当你访问[我的博客](https://victoria.dev/blog)并搜索这篇文章时，你可以再次找到这篇教程！
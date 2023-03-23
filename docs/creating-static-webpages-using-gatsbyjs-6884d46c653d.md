# 使用 GatsbyJS 创建静态网页

> 原文：<https://betterprogramming.pub/creating-static-webpages-using-gatsbyjs-6884d46c653d>

## *看看为什么服务器渲染的网站如此震撼*

![](img/ef710e18c6df16f0397f85b3b47e7f68.png)

照片由 [Zach Vessels](https://unsplash.com/@zvessels55?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/static?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。

现在的网页加载速度很慢。他们需要获取所有 JavaScript 文件、所有图像，并调用许多不同的 API。但是，当我们继续创建更复杂的网站时，我们的用户变得越来越不耐烦，几乎要求页面在几秒钟内加载。否则，他们会点击离开。

[Gatsby](https://www.gatsbyjs.com/) 通过在服务器上预加载所有东西来关注性能。它创建服务器渲染的网页，网页上已经有了所有的内容。页面不需要调用任何 API，这也提高了安全性。

# 为什么是静态？

那么，为什么你会关心，为什么你会创建一个服务器渲染的静态网站呢？在某些情况下，您可能不想使用服务器呈现的页面。但是在你这样做的情况下，有几个使用它的理由:

*   性能——因为所有东西都加载到服务器上，所以服务器只需要提供一个样式 HTML 文件和所有必要的样式表和 JavaScript 文件。它不需要等待 API 调用，这大大加快了加载速度。
*   安全性——在构建时呈现静态 HTML 意味着没有服务器和可访问的数据库。这将攻击发生的可能性降至几乎为零。
*   盖茨比有很好的开发经验和很大的社区。
*   静态和服务器渲染的页面生态系统正在慢慢成长。

所以让我们自己来看看它是如何工作的。开始工作再容易不过了，这对我们有好处。

# 装置

我们将把 Gatsby 用于我们的服务器渲染网页。另一个很好的服务器端渲染选项是 [Next.js](https://nextjs.org/) 。

Gatsby 使用 CLI 工具进行安装。让我们使用 NPM 安装它:

```
npm install -g gatsby-cli
```

Gatsby 项目通常使用启动器启动。Gatsby 在他们的 GitHub 上有很多各种项目的启动程序。“Hello-World”启动器是最简单的启动器，也是我们将使用的启动器。

```
gatsby new project-title [https://github.com/gatsbyjs/gatsby-starter-hello-world](https://github.com/gatsbyjs/gatsby-starter-hello-world)
```

安装 CLI 工具后，我们可以使用`gatsby new`创建一个新项目。然后，我们定义一个项目名，并向其中一个启动器添加一个 GitHub 链接。最后，我们可以通过从新的项目文件夹运行`gatsby develop`来启动我们的服务器。然后，它将建立我们的静态页面，并提供给`localhost:8000`。

# 你的第一页

您可能已经查看了生成的文件。如果你看到了`src/pages/index.js`，你就会看到我们正在使用一个基于 [React](https://reactjs.org/) 的库。事实上，最小的页面只有普通的 React 代码。

我们导入 React 并导出一个功能组件。我们可以使用钩子和 React 的所有其他特性。让我们给页面添加一些样式和新元素。它的工作原理和 React 一样。如果您想添加不是页面的新组件，您可以将它们添加到`src/components`文件夹中。

如果我们想添加一个新页面，我们可以用另一个名字创建一个新的 JavaScript 文件，比如`about.js`。它将包含与主页大致相同的代码。

我们还添加了一些简单的样式作为示例。如果您浏览到`localhost:8000/about`，您将看到新页面。如果它还没有加载，您可能需要重新启动服务器，虽然不应该这样。

对于链接，你需要使用`Link`组件。这是 Gatsby 中处理导航的组件。

```
import { Link } from "gatsby"
...
**<Link to={`/about`}><span>About</span></Link>**
```

但是，尽管这些页面是静态的，并且在提供给用户时不依赖于 API 或其他数据，但在构建时它们*可以*依赖这些。

# 获取数据

Gatsby 使用 [GraphQL](https://graphql.org/) 查询从各种来源检索数据。GraphQL 是一种由脸书开发的查询语言，专注于获取您需要的数据，而不需要所有非重要数据来减缓请求。

最容易获得的数据是来自`gatsby-config.js`的站点元数据。您可以添加和删除使用 GraphQL 检索的站点数据和配置。

我们以标题的形式添加简单的站点元数据。但这可以是任何东西，比如字幕、按键和其他文本或对象。

然后，我们将更新我们的 About 页面，使用 GraphQL 查询来使用这些元数据。数据将被检索并放入您可以从道具中检索的`data`对象中。

这只是 Gatsby 的冰山一角，只是静态 web 应用程序的开始。当你慢慢完善，了解更多关于 Jamstack，Gatsby，或者 Next.js 的东西，你会发现它可以像你想象的那么强大。

Gatsby 甚至提供了使用 Markdown 文件创建页面的可能性。这对于非技术人员可能负责内容的博客或其他网站来说是非常强大的。我写了另一篇关于这个话题的文章。

# 结论

服务器渲染网页正慢慢变得越来越广泛使用。Gatsby 是第一批，现在正在被 Next.js 超越。在快速变化的网络开发环境中，我对它的未来有很好的预感。

感谢您的阅读，祝您愉快。
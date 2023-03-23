# 为什么我在 Next.js 项目中去掉了 getInitialProps

> 原文：<https://betterprogramming.pub/why-i-got-rid-of-getinitialprops-in-my-next-js-project-fc926e98ed61>

## 以及如何在您的项目中使用现代数据获取 API 完成同样的工作

![](img/8484d1e8560e34a0fe2bd6ed0deae4a4.png)

照片由 [Jonatan Pie](https://unsplash.com/@r3dmax) 在 [Unsplash](https://unsplash.com/@akellbl4) 上拍摄。

先来小解释一下 Next.js 如何与`getInitialProps`协同工作。

在 Next.js 中，`getInitialProps`启用服务器端渲染，不能静态优化。每次我们打开一页它都会运行。如果页面是由 URL 直接请求的，它就在服务器上运行。另一方面，如果我们通过使用`next/link`或`next/router`的链接或 UI 元素打开页面，它会在客户端运行。

# getInitialProps 有那么差吗？

## 它在服务器端呈现一个页面

有时我们需要用来自服务器的数据来呈现静态页面。`getInitialProps`是最糟糕的方法。在这种情况下，您会失去静态优化。您已经有了静态 HTML 页面。现在您有了一个服务器端呈现的页面。当您需要为不同的用户动态更新页面时，这并不坏，但当它只是一个包含有时可以更新的数据的页面时，这就糟糕多了。您可以让您的服务器发送静态 HTML，无需处理和额外的时间。

## 当在 _app.js 中使用时，它选择退出自动静态生成

根据我的经验，我可以说使用`getInitialProps`的主要原因是获取共享数据并将其填充到应用程序中的所有页面。在这种情况下，它真的很有用，但是它破坏了静态优化的整个思想，因为所有的页面都变成了 SSR。当然，如果您计划编写一个仅支持 SSR 的应用程序，这是可以的，但是如果您不打算这样做，那就太可怕了。稍后，我们将了解如何使用`getStaticProps`实现这一点。

## 它在客户端引入了服务器逻辑

这个方法是混合的—它在服务器和客户机上都被调用。对于那些从一开始就使用它并习惯了它的人来说，这并不新鲜，但是这种方法导致了在没有任何正确的方法的情况下编写同构代码。

例如，在 React 中，您可以用`componentDidMount`或`useEffect`编写与浏览器相关的代码，而不用担心它会在服务器上被调用。使用`getInitialProps`，我们必须检查我们的代码在哪里工作。有时，客户端和服务器端的逻辑可能是重复的。这会导致代码中出现大量的条件，代码本身也会变得混乱。您的服务器端依赖项也可以打包在客户端包中。这仅仅是开始。

## 导航请求

现在我们来谈谈 HTTP 请求。我们可以在服务器上发送几个请求，在那里构建我们的页面，然后向客户端发送一个 HTML。但是当我们每次浏览我们的站点时，所有这些请求都会被调用。当然，您可以实现某种缓存或存储来管理数据，以便有效地组织请求。要做到这一点，你需要在项目中添加一些模块，而且复杂性会快速增长。

假设我们解决了请求缓存的所有问题，但是我们仍然有请求链。例如，请求一个用户，如果用户被授权，请求他们的权限。然后，检查权限，然后请求页面数据。如果我们在服务器端编写请求，它们可以更快地得到解决。为什么？因为我们是服务器对服务器做的，Next.js 应用和后端的数据通常比客户端和服务器的数据更接近。当在客户端做同样的事情时，我们只依赖用户的网速。

## 不推荐

不，[不弃用](https://nextjs.org/docs/api-reference/data-fetching/getInitialProps)。只是 Next.js 团队不推荐。他们不打算在最近的将来弃用它。由于 Next.js 团队发布了新的抓取方法`getServerSideProps`和`getStaticProps`，他们推荐使用它们。它们更加原子化，也更容易使用。

# 获取数据的新方法

自从[next . js 9.3](https://nextjs.org/blog/next-9-3)发布后，`getStaticProps`和`getServerSideProps`方法就成为了推荐的道具获取方式。它们是原子的，所以你只要看它们的名字就能明白它们是做什么的。

## 他们真的好吗？

在我看来，是的。在我看来，Next.js 的作者也有同样的看法。正如我以前说过的，它们是原子的，非常容易使用。如果你需要创建一个静态生成的页面，使用`getStaticProps`并创建 HTML。如果你想时不时更新你的页面，可以加`revalidate`。经过以秒为单位的参数值传递的时间后，您的页面将被标记为陈旧，当新的页面请求到来时，它将在后台重新生成。

`getServerSideProps`只在服务器上被调用，它解决了我上面描述的问题。此外，它还为重定向提供了一个 API。

## 如何让它们变得更好

新方法很好，但并不完美。让我们做一点编程。如果我们在许多页面上使用一些数据或动作，我们不想重复他们的代码。在这种情况下，我们可以创建一个包装。例如，使用 SSR 进行授权可以:

否则，我们有另外两个选择:编写我们的实现或者只使用`[next-connect](https://www.npmjs.com/package/next-connect)`。我不会展示如何使用`next-connect`，但我会尝试尝试一下，并提供我自己的实现。也许不会真的干净，但我会尽力。让我们看一个例子:

这是一个非常简单的例子，可以让我们的生活变得更容易。看看我们如何使用它:

# 过渡

在`_app.js`里既不能用`getServerSideProps`也不能用`getStaticProps`。相比`getInitialProps`是劣势。另一方面，这是 Next.js 团队的一个非常好的决定。如果您将`getInitialProps`添加到您的`_app.js`文件中，它将为每个页面启用服务器端呈现。如果你真的在乎速度，那不是你想做的。

更重要的是，您可能会丢失一些使用`getInitialProps`在页面上工作的模块，您将不得不寻找一个合适的替代品或者自己实现它。

## 重新寄送

让我们从一件简单的事情开始。在我看来,`getInitialProps`中的重定向很糟糕。您必须为服务器和客户端处理重定向。文档中没有任何关于`getInitialProps`中重定向的内容。正如示例中所示:

对于`getServerSideProps`来说，一切都简单多了。我们有文档中描述的官方方法。它看起来很干净，我们不需要考虑它将在哪里被调用:

## 静态页面

这里一切都很顺利。用`getStaticProps`代替`getInitialProps`就行了。我从[next . js docs](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation)中得到了一个例子:

并将其转换为`getStaticProps`:

此外，我们可以通过地图生成静态页面。比如文章或者新闻。让我们用 GitHub 用户的回复来创建页面。我们需要添加`getStaticPaths`:

我们在`getStaticPaths`中创建了一个参数图，它们看起来会像`[{ name: ‘akellbl4’, name: ‘akellbi4.github.io’ }]`。然后对于这个地图的每个元素，`getStaticProps`将被调用，这个名字将被传入`ctx.params`。我们可以摆弄后备参数。[查看文档](https://nextjs.org/docs/basic-features/data-fetching#the-fallback-key-required)了解更多信息。如果你需要按时间更新这些页面，你可以指定`revalidate`参数并从`getStaticProps`返回。详见[文档](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation)。

## 服务器端呈现的页面

这部分比上一部分容易多了。这里我们需要做的就是从`getInitialProps`中移除客户端代码，就这样。通过`next/link`或`next/router`在页面首次加载或站点导航时调用`getServerSideProps`。Next.js 调用导航上的`getServerSideProps`作为服务器上的 lambda，并将 JSON 中的执行结果返回到下一个页面。之后，页面将被渲染。

## 从`_app.js`上拆下`getInitialProps`

如果我们在这里共享数据，我们只是用新的方法为每个页面获取它。如果您的数据源有问题(例如，您的 CMS 或后端很慢),无法满足大量请求，我建议您阅读我的文章《在构建时有效地获取共享数据》。

首先，您应该检查每个页面是否真正需要逻辑。然后将该逻辑分成两个功能:第一个用于客户机，第二个用于服务器。我举一个有授权的例子:

让我们把它变得干净漂亮:

在这个例子中，我们共享一个 API 调用来按需使用它——不再在一个地方混合客户机/服务器逻辑。你可以按照同样的逻辑从`getInitialProps`中分离你的代码。

# 结论

目前，Next.js 是现代前端中最有趣、最先进、最有前途的框架之一。在过去的一年中，他们已经取得了重大进展，成熟成为一个受到良好支持的框架。当前的数据获取方法看起来是经过深思熟虑的，并提供了许多优势。您可以今天就开始实现它，然后一个接一个地迁移其他页面，而不用一次重写整个代码库。

这是开始这一旅程的好时机，我希望这篇文章能给你入门所需的工具！
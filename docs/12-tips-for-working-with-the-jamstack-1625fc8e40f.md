# 使用 Jamstack 的 12 个技巧

> 原文：<https://betterprogramming.pub/12-tips-for-working-with-the-jamstack-1625fc8e40f>

## CDN、原子部署、无服务器等等

![](img/81954a27e096ec76de3b6a61c5a7f080.png)

照片由[克莱门特·H](https://unsplash.com/@clemhlrdt?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这篇文章最初发表在智能编码器的[上。](https://thesmartcoder.dev/12-tips-for-working-with-the-jamstack/)

Jamstack 终于成为主流，也是 2020 年最热门的话题之一。这个概念获得了很大的吸引力，正在起飞。而且，作为一名 web 开发人员，无论你喜欢与否，你都应该参与进来。

即使你不喜欢，知道人们在谈论什么也是很好的。让你与时俱进是你在这个行业中的优势所在，所以对新事物要保持开放的心态！

这个 Jamstack 是什么？嗯，就是 JavaScript，API，和标记。但这还远远不止这些！当人们谈论 Jamstack 时，他们不会谈论诸如操作系统、后端编程、数据库或特定的 web 服务器之类的东西。

他们没有谈论具体的技术，而是谈论构建应用程序和网站的新方法，这些应用程序和网站具有高性能、更高的安全性、以更低的成本非常好地扩展，并提供更好的开发人员体验。

为了帮助您利用 Jamstack 的优势，并作为开发人员保持领先，我给了您一些使用 Jamstack 时应该知道的提示和技巧。

# 1.利用 CDN 进行托管

Jamstack 应用程序的最大好处之一是它们不依赖于后端实现(服务器端代码)。

相反，您可以直接从 CDN(内容交付网络)交付整个应用程序，如 Cloudflare 或类似的服务。

这在全球范围内带来了出色的性能和快速的响应时间，因为无论用户在哪里，大部分时间都有一个 CDN 节点靠近他们！

# 2.利用原子部署

Jamstack 项目通常包含数百或数千个文件。为必要的部署而一个接一个地上传所有这些文件可能会导致不一致的状态。肯定不是你想要的！

使用 atomic deployment，您可以上传所有文件，并等到一切就绪后再对生产进行任何更改。

# 3.使用现代构建工具

Jamstack 项目与最先进的技术配合得非常好，如[巴别塔](https://babeljs.io/)、[邮政编码](https://postcss.org/)、[包裹](https://parceljs.org/)等。不要等到浏览器采用新的特性，现在就用市场上最好的构建工具来使用它们！

# 4.自动化您的构建

Jamstack 项目利用预先构建的标记文件。要使更改生效，需要另一个构建过程。

现代主机提供商给你工具和服务来自动构建，以避免你的挫折。或者，您可以使用 webhooks 来实现这一点。

# 5.Git 中的整个项目

因为 Jamstack 项目由静态文件和资产组成，所以建议将整个项目放在 Git 中。

然后每个人都可以克隆项目，通过 npm 或 [Yarn](https://yarnpkg.com/) 安装软件包和依赖项，并在本地运行一切，而不必建立数据库或运行复杂的安装例程。

# 6.即时缓存验证非常重要

通过 CDN 运行 Jamstack 应用程序时要记住的一点是，许多服务会缓存您的文件和资产。

因此，为了在部署后看到变化，有必要定期使缓存失效，最好是通过自动化过程。

# 7.静态站点生成器是你的朋友

许多(如果不是大多数)Jamstack 应用程序是由当今最好的静态站点生成器驱动的，如 [Gatsby](https://www.gatsbyjs.org/) 、 [Next](https://nextjs.org/) 、 [Nuxt](https://nuxtjs.org/) 、 [Gridsome](https://gridsome.org/) 、 [Hugo](https://gohugo.io/) 等。

这些结合了 PWAs 和服务器渲染的强大功能，对 SEO 和页面速度有很大的影响。它们还附带了许多很酷的功能，例如像 webpack 这样的预配置捆绑器或 package，以加速应用程序开发。

# 8.前端框架需要一些工具经验

如果您想在 Jamstack 项目中使用 React 或 Vue 这样的前端库或框架，这是可能的。

然而，这些库通常不输出静态文件，所以需要一些工具来实现你的目标。你应该考虑使用 Next 或 Gatsby 而不是 plain React 或 Nuxt/Gridsome 而不是 Vue 来结合所有世界的优点！

# 9.选择一个 Jamstack 友好的托管服务

随着 Jamstack 的兴起，伟大的托管服务也应运而生，比如 [ZEIT](https://zeit.co/) 、 [Netlify](https://www.netlify.com/) 、 [GitHub Pages](https://pages.github.com/) 和 [Stackbit](https://www.stackbit.com/) 。他们为您的应用提供强大的支持和一键式部署服务。

# 10.无服务器功能运行良好

因为您通常不想在 Jamstack 应用程序中开发真正的后端，所以许多开发人员将无服务器概念用于数据和业务逻辑。

像 AWS Lambda 这样的无服务器解决方案是运行小部分逻辑而不需要维护服务器的绝佳选择。

# 11.通过无服务器数据库定制数据

为了在没有后端和数据库的情况下存储动态数据，开发人员可以利用无服务器数据库的服务，如 [FaunaDB](https://fauna.com/) 、[无服务器 GraphQL](https://serverless.com/blog/running-scalable-reliable-graphql-endpoint-with-serverless/) 或类似的服务。

# 12.无头 CMS + Jamstack =牛逼

为了支持像博客这样的 Jamstack 应用程序，无头内容管理系统是最好的选择。

像 Netlify CMS、 [Contentful](https://www.contentful.com/) 、 [Forestry](https://forestry.io/) 甚至 headless WordPress 这样的服务与 Jamstack 应用程序配合得非常好。分离的后端甚至有安全上的好处(想想 WordPress 的攻击媒介…)。

# 结论

所以，我希望你明白为什么 Jamstack 这么棒。如果你还没有被说服，这里有一个由 Sarah Drasner (Netlify)制作的关于 Jamstack 的视频。看看吧！

感谢阅读！
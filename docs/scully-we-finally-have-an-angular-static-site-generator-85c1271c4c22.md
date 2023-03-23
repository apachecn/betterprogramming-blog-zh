# 史高丽:我们终于有了一个有角度的静态站点生成器

> 原文：<https://betterprogramming.pub/scully-we-finally-have-an-angular-static-site-generator-85c1271c4c22>

## 正好赶上 2020 年

![](img/5c2395d40fb555235806a8f8acf697fd.png)

弗洛里安·奥利佛在 Unsplash[上的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

棱角分明的开发者们，庆幸吧！感谢 [HeroDevs](https://herodevs.com) 的团队，我们终于有了一个有角度的静态站点生成器！

虽然, [Scully](https://github.com/scullyio/scully) 几天前才发布，而且目前还在 Alpha 版本中，这对 Angular 社区来说是一个非常激动人心的时刻。

基于 React 的静态站点生成器如 [Gatsby](https://www.gatsbyjs.org/) 从 2017 开始就在[左右，差不多两年了，Angular 已经落后了。现在，正好赶上新的十年，Angular 开发人员有机会使用他们已经熟悉的相同方法和工具生成极快的静态站点。](https://www.gatsbyjs.org/blog/gatsby-first-beta-release/)

# 这是什么东西？

Scully 是一个全新的静态站点生成器，围绕 Angular 框架和生态系统构建。

对于那些还不熟悉的人，你可以阅读更多关于什么是静态站点生成器的内容，但是简短的版本是，Scully 将你的新的或现有的 Angular 应用程序预渲染为 HTML 和 CSS，不需要大量的 JavaScript 包。

它使用节点 CLI 来实现这一点，因此将其添加到 Angular 项目就像运行以下命令一样简单:

```
ng add @scullyio/init
```

然后，像往常一样构建您的站点，然后执行 Scully:

```
ng build
npm run scully
```

在这些命令之后，检查一下`dist/static`文件夹中新编译的 HTML 和 CSS！

如果你将 Scully 添加到一个已经存在的 Angular 项目中，你现在会惊讶于静态内容提供的大规模缩减的包大小。

# 我为什么要用史高丽？

正如使用 Gatsby、 [Hugo](https://gohugo.io/) 和其他静态站点生成器的开发人员已经知道的那样，与大规模的 Angular 或基于 React 的项目相比，静态站点有许多好处。

## 速度

> “你的应用程序会立即出现在任何设备上。”— [史高丽](https://github.com/scullyio/scully)

你应该使用 Scully 的第一个也是最重要的原因是它允许你创建超快的用户体验。

通过将代码预先呈现为静态格式，Scully 消除了用户等待下载大量 JavaScript 包时的长加载时间。

将 Scully 与 CDN 耦合，或者将你的网站托管在 [GitHub Pages](https://pages.github.com) 或 [Netlify](https://www.netlify.com) 上，将允许你的用户以最快的方式访问你的内容。

此外，由于无需在客户端运行脚本来呈现页面，用户的设备将能够更快地显示页面并进行交互，这再次让用户受益。

此外，如果没有脚本，您的页面可以在更旧的浏览器中查看，或者在不支持或禁用 JavaScript 的设备上查看。

## 创建一个只有棱角的博客

> "当你想把你的博客搬到 Angular 的时候，史高丽是你最好的朋友！"— [史考莉](https://github.com/scullyio/scully/blob/master/docs/blog.md)

以前，如果你想创建一个静态博客，你必须找到一些其他的技术来创建你的网站。

然而现在，Scully 允许你创建新的博客文章，就像你用 Angular 创建新的组件一样。首先，你需要通过运行以下命令在 Angular 应用程序中设置对 Scully 博客文章的支持:

```
ng g @scullyio/init:blog
```

在此之后，创建一个新的职位是非常简单的。同样，只需使用 CLI 并运行:

```
ng g @scullyio/init:post --name="Name your post here"
```

吧嗒吧，吧嗒吧，你有博客了。

## 角度发展体验

即使你从未尝试过棱角分明的发展，开始使用 Scully 也很容易。网上有很多介绍，包括 Angular 自己的[教程](https://angular.io/start)。

一些最大的好处，像路由，合并`npm`包，以及创建可重用的组件和服务在 Scully 中都是可用的，因为你真的只是在 Angular 中开发。

如果你已经是 Angular 开发者，那么 Scully 对你来说是一个极好的静态站点生成器，因为没有新的语法或概念需要学习。

它所需要的只是在构建时一些额外的命令，剩下的就交给史高丽了。

# 我应该用它吗？

如前所述，Scully 仍然处于开发的 Alpha 阶段，所以它不应该用于需要持续开发稳定性的项目。随着技术的采用和发展，可能会有许多变化。

另外，史高丽只和角 9 一起工作。所以如果你不能更新到最新版本，Scully 可能不适合你。

也就是说，制作静态站点有很多好处。Scully 当然是需要考虑的，尤其是如果你已经有了 Angular 开发者的背景。

在这个阶段，Scully 是爱好或小规模项目的完美候选人，它将允许您潜入有角度的静态站点的世界，而不必在您的开发过程中随着 Scully 的成熟而更新您的包和代码。

# 结论

我毫不怀疑在接下来的几个月里，随着越来越多的人了解并开始开发它，Scully 将会改变。在写这篇文章的时候，已经有很多强大的任务需要 Scully 来处理，并且还有更多的空间。

随着这个产品的成熟，我期待着能够以一种有角度的方式生成静态站点，创造两个世界的最佳效果:一个奇妙的开发人员体验，和一个终端用户的超快的体验。

在新的十年里，我肯定会用 Scully 创建一些项目，我也鼓励你这样做。

# 资源

*   [Scully GitHub 回购](https://github.com/scullyio/scully)
*   [史高丽文档](https://github.com/scullyio/scully/blob/master/docs/scully.md)
*   [构建应用的现场演示](https://www.youtube.com/watch?v=Sh37rIUL-d4)。
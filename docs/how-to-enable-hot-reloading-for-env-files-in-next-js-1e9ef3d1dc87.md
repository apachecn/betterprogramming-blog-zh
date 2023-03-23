# 如何为启用热重装？Next.js 中的 env 文件

> 原文：<https://betterprogramming.pub/how-to-enable-hot-reloading-for-env-files-in-next-js-1e9ef3d1dc87>

## 无需手动重新加载页面即可立即看到更改

![](img/a4faf740836b8dfca32aa4e186c22954.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Demi DeHerrera](https://unsplash.com/@demidearest?utm_source=medium&utm_medium=referral) 拍摄

开发过程中最烦人的事情之一就是当你改变了代码中的一些东西，却没有在你的应用程序中得到即时的反馈。这也是为什么 [Next.js 9.4 引入了什么叫做快速刷新](https://nextjs.org/blog/next-9-4#fast-refresh)的原因。

"*快速刷新是 Next.js 的一个特性，它为您提供对 React 组件所做编辑的即时反馈。在 9.4 或更高版本的所有 Next.js 应用程序中，默认情况下启用快速刷新。启用 Next.js 快速刷新后，大多数编辑应该在一秒钟内可见，而不会丢失组件状态。*——[基本特性:快速刷新](https://nextjs.org/docs/basic-features/fast-refresh)

但是，快速刷新功能可能不适用于所有文件。对于`.env`文件来说尤其如此。详细地说，当您编辑`.env`文件时，您通常必须重启开发服务器才能看到这些更改。

如果你对 Next.js 的行为不熟悉，这会导致沮丧，降低你的工作效率。幸运的是，Next.js 引入了一个解决方案。在您意识到需要重启开发服务器之前，不必再想为什么新的环境变量没有被 Next.js 应用程序加载。

让我们进一步了解如何对`.env`文件启用快速刷新功能。

# 在上启用 Next.js 快速刷新功能。环境文件

要启用`.env`文件的热重装，您只需将 Next.js 依赖项更新到 12.3+版本。为此，请确保启动以下命令:

```
npm install next@latest
```

这将安装最新的 Next.js 版本。在撰写本文时，它是 12.3.1 版本。任何大于等于 12.3 的版本都可以。请记住，从一个版本的 Next.js 迁移到另一个版本可能需要额外的工作。阅读官方文档中的[以了解更多信息。](https://nextjs.org/docs/upgrading)

如这里[所述](https://nextjs.org/blog/next-12-3#improved-fast-refresh)，Next.js 12.3 现在支持对配置文件进行修改时的热重装，包括:

*   `.env`和`env.*`文件
*   `jsconfig.json`或`tsconfig.json`文件

在 Next.js 12.3 中，当对这些配置文件进行更改时，您不再需要重新启动本地开发服务器。

您可以在下面的现场演示中体验这一新功能:

更改 Next.js 配置文件，点击 save，您的更改将在浏览器中得到反映，而无需重新加载页面。

瞧啊！您刚刚学习了如何在 Next.js 中为`.env`文件启用热重装

# 结论

在本文中，您了解了如何在 Next.js 上启用`.env`文件的热加载。最后，Next.js 将快速刷新特性扩展到了配置文件。

因此，通过采用高于或等于 12.3 的 Next.js 版本，当您更改配置文件时，您将能够在浏览器中获得即时反馈，而不必重启本地开发服务器或手动重新加载页面。

感谢阅读！我希望这篇文章对你有所帮助。请随意留下任何问题、评论或建议。
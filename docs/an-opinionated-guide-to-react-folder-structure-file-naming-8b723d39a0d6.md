# 一个固执己见的指南反应文件夹结构和文件命名

> 原文：<https://betterprogramming.pub/an-opinionated-guide-to-react-folder-structure-file-naming-8b723d39a0d6>

## 如何以简洁的方式组织你的项目

![](img/b1cac560d244385afdeec92a401602b0.png)

# 介绍

我已经和 React 合作了四年多。在这段时间里，我形成了一些我认为应用程序应该是什么样子的观点。这是一系列此类固执己见的文章的第一部分。

# 文件夹结构和文件命名

在我工作的应用程序中，我经历了文件夹结构的多次迭代。我发现最好的方法是使用扁平的文件夹和长文件名。由于我用的是 VSCode，搜索文件很容易，所以名字越长越好。下面是我如何布置我的大多数 React 应用程序。

# 文件夹结构

以下是这些文件夹中内容的快速概述。

*   `/assets` —图像、标志
*   `/components` —在多个页面之间共享的组件
*   `/contexts` —我将所有的上下文组件保存在一个单独的文件夹中，以避免与普通的 React 组件混淆。我喜欢实现的一个常见上下文是`UserAuthContext.tsx`。
*   `/lib`——当使用第三方库(例如 Firebase)时，我喜欢将所有的初始化放在一个名为`lib`的文件夹中。然后，我将导出该初始化库的实例。

*   `/pages` —页面也是 React 组件，但它们代表应用程序的页面或屏幕。这些地图与`AppRoutes.tsx`文件中的路线 1:1 对应。
*   `/services` —我所有的 API 方法都放在一个名为`services`的文件夹中。我喜欢这样做，这样我就不会把 API 调用的业务逻辑直接放到组件中，这样任何组件都可以轻松地引用它需要的服务。
*   `/styles` —我很少编写自定义 CSS，而是选择使用像 Tailwind CSS 这样的框架。这个`styles`文件夹是我生成的样式和任何自定义 CSS 所在的地方。
*   `AppRoutes` —此文件包含我的应用程序的所有路线。我使用 React Router 已经有一段时间了，我喜欢有一个包含我所有路由的文件，这样我就可以一目了然了。

*   `index.tsx` —这是典型的`index`文件，您可以在其中将 React 应用程序渲染到文档中。

# 文件命名

我的经验是文件名越长越有描述性越好。对于导出 React 组件的文件，我使用`PascalCase`。对于其他一切，我使用`dash-case`。

```
# PascalCase
/components/NavBar.tsx
/contexts/UserAuthContext.tsx
/pages/DashboardPage.tsx# dash-case
/services/auth-service.ts
/lib/apollo-client.ts
```

我总是更喜欢命名导出，而不是默认导出。这使得在我的编辑器中搜索我想要的内容更加容易。

# 包扎

这是我将要写的一系列文章的第一部分。如果你喜欢这个，请在下面评论。你还想让我报道什么？一如既往，我乐于接受建议。

感谢阅读。
# 使用 React 路由器实现模块化

> 原文：<https://betterprogramming.pub/using-react-router-for-modularization-872b32315a3b>

## 嵌套路由的简要指南

![](img/2706849d4551db3870cc91292e994f03.png)

作者图片

有时，在构建多页面应用程序时，不同的页面可能有非常不同的特性，或者您可能希望为应用程序的不同部分创建不同的布局。这可以通过模块化来创建应用程序的不同部分来实现。

# 关于反应路由器

React-Router 是 React 的一个轻量级客户端和服务器端路由库。它允许您在 web 应用程序中处理路由，根据 URL 显示不同的页面。React Router 将匹配 URL 并加载特定页面的组件。版本 6 将为给定的 URL 选择最具体的匹配，因此您不必担心排序您的路线。

## 目标

使用 React Router 将应用程序分成不同的部分。

## 先决条件

对于本教程，您的计算机上必须安装有 [Node](https://nodejs.org/en/) ≥ 14.0.0 和 [npm](https://www.npmjs.com/) ≥ 5.6。您还需要对构建和使用可重用组件有一个大致的了解，并且您需要一个代码编辑器。我们还将使用`[styled-components](https://styled-components.com/)`，这是一个允许你直接在 JS 文件中编写 CSS 的库。这消除了在不同文件之间来回移动以设计组件样式的需要。

让我们开始吧。

# 建立

我们将通过在您的终端或 cmd 上运行以下命令来创建一个新的 React 应用程序:

```
npx create-react-app my-router-app
```

导航到您的应用程序，然后安装`react-router-dom`包和`styled-components`包。命令末尾的`@6`意味着您正在添加 react-router-dom 的版本 6。

```
cd my-router-app
npm install react-router-dom@6 --save styled-components
```

您的`package.json`文件应该类似如下:

```
"dependencies": {
  "@testing-library/jest-dom": "^5.16.5",
  "@testing-library/react": "^13.3.0",
  "@testing-library/user-event": "^13.5.0",
  "react": "^18.2.0",
  "react-dom": "^18.2.0",
  "react-router-dom": "^6.3.0",
  "react-scripts": "5.0.1",
  "styled-components": "^5.3.5",
  "web-vitals": "^2.1.4"
},
```

设置完成后，您可以运行应用文件夹中的命令`npm start`来启动。现在，当您在`localhost`上打开端口 3000 时，您可以看到默认的 React 应用程序。

# 创建页面和组件

我们在这一部分创建的所有东西从技术上来说都是一个组件，但是一些组件将作为页面，而另一些将作为页面的一部分呈现。

我们首先在 src 文件夹中创建一个 pages 目录。这是我们所有页面的位置。此外，在我们的`src`文件夹中，我们创建一个组件目录，在那里我们将放置组成我们页面的其他组件。

我们将建造一艘`LoginPage`、一艘`HomePage`、一艘`CollectionsPage`和一艘`FavouritesPage`。我们还将使用`styled-components`为我们的组件添加非常简单的样式。

LoginPage.js 组件

HomePage.js 组件

CollectionsPage.js 组件

FavouritesPage.js 组件

接下来，我们创建`Footer`组件。

Footer.js 组件

我们将在下一步创建 Navbar 组件。

# 利用嵌套路线对您的应用进行细分

嵌套路由是 React 路由器提供的最强功能之一。根据 React 路由器[文档](https://reactrouter.com/en/main/getting-started/overview#nested-routes):

> 路由可以相互嵌套，它们的路径也会嵌套(子继承父)

[这里的](https://dev.to/tywenk/how-to-use-nested-routes-in-react-router-6-4jhd)是一篇很好的文章，深入探讨了如何利用嵌套路线来创建复杂的布局。

在我们的例子中，我们将使用嵌套路由的概念来有条件地呈现导航栏和页脚组件。

我们首先在我们的`components`文件夹中创建一个新组件，我们将其命名为`MainLaoyout.js`。在这个组件中，我们将导入具有相似布局的所有组件以及出现在多个组件上的组件。`Navbar`和`Footer`组件将在`HomePage`、`CollectionsPage`和`FavouritesPage`中渲染。

MainLayout.js 组件

从上面可以看出，要在多个页面上呈现一个组件，必须将该组件放在包含该组件的`Routes`标签之外。`Routes`标签是`Route`标签的父标签，其中每个`Route`标签将包含可以导航到的每个页面。这样，`Navbar`和`Footer`组件将出现在`Routes`标签中描述的所有页面上。

接下来，我们创建 Navbar 组件。该组件将包含我们的应用程序中所有其他页面的链接。

Navbar.js 组件

最后，在我们的`App.js`组件中，我们将不同的部分放在一起以完成我们的应用程序。

App.js 组件

在这最后一步结束时，您应该有一个带有导航栏和页脚的主页。当你浏览页面时，你应该看到导航和页脚是持久的，当你点击“注销”选项时，你会看到导航和页脚不再存在。

# 结论

在本文中，我们使用嵌套路由来创建模块化。我们的应用程序只有两个区域，但是您可以使用这个概念来创建更多的区域。

最后，[注意](https://stackoverflow.com/a/71446125)标签中的每个子组件必须是一个`Route`组件。这就是为什么在`MainLayout`组件中我们使用了`React.Fragment`，所以当我们从`App.js`组件中渲染它时，它被一个`Route`标签包围着。

我希望这篇文章对你有帮助！
# 网站开发初学者指南

> 原文：<https://betterprogramming.pub/beginners-guide-to-web-development-d46dadfdd049>

## 从哪里开始？从这里开始

![](img/c04608515c6894d6cf23c15d99c965b4.png)

Joshua J. Cotten 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

你不知道从哪里开始吗？不遭受信息过载，很难找到正确的建议。

在本文中，我列出了学习 web 开发所需的所有基础知识。有了这些，你就会对 web 开发的基础知识以及需要的技能有所了解。我将带你一步一步地了解基础知识。我建议先学习前端和后端的基础知识，然后再进行专门化。

# 你对这门学科有了解吗？

在开始之前，你需要了解一些基本概念，比如网站如何工作，前端和后端的区别，以及代码编辑器的使用。

如果你把一个网站剥离到它的核心，你会注意到它是由一堆文件组成的，这些文件存储在一台叫做服务器的有互联网连接的计算机上。然后，您可以通过浏览器加载该网站，在这种情况下，浏览器被称为客户端。因此，每次你在互联网上，你，客户端，从服务器加载数据，并提交数据回服务器。客户端和服务器之间的这种往复就是互联网的基础。

Web 开发人员角色通常分为前端、后端和全栈。这些术语描述了客户机-服务器关系的哪一部分是你的专长。前端意味着你主要和客户端打交道。后端是网站的一部分，处理网站/web 应用程序工作所需的大量逻辑和功能。前端和后端 web 开发都有独立但重要的功能。

当你建立一个网站时，你将使用的工具之一是你的代码编辑器或 IDE(集成开发环境)。这个工具允许你编写标记和代码来组成网站。有很多好的选择，但是目前，最流行的编辑器是 VS Code。

# 基本前端

HTML、CSS 和 JavaScript 文件构成了前端。浏览器在客户端加载这些文件。HTML(超文本标记语言)是所有网站的基础。当你浏览一个网站时，HTML 文件包含了页面上的所有内容，它使用标签来表示不同类型的内容。例如，您可以使用文本来创建标题、段落、项目符号列表、图像等。

HTML 标签本身确实有一些附加的样式，但都是非常基本的。CSS(层叠样式表)允许你操作 HTML 内容，让它看起来更吸引观众。您可以添加颜色、自定义字体，并根据自己的意愿放置网站的元素。人们有时倾向于忽略 CSS，这样他们就可以转移到 JavaScript 之类的东西上，但是值得注意的是，CSS 比看起来更有深度。

JavaScript 是一种设计用于在浏览器中运行的编程语言。使用 JavaScript，你可以使你的网站更具互动性。例如，您可以构建一个弹出窗口，当用户单击它时显示菜单链接。

# 基础知识之后需要熟悉的工具

既然您已经知道 HTML、CSS 和 JavaScript 是前端 web 开发的基本构件，那么在继续之前，您还需要学习一些其他工具。

软件包管理器是大部分开源软件的在线集合。每一个软件——通常称为软件包——都可以在您的项目中安装和使用。把它们想象成插件。您可以使用其他人已经编写的实用程序，而不是从头开始编写所有内容。最流行的包管理器叫做 NPM(节点包管理器)。也可以用另一个叫 yarn 的管理器。这两个都是不错的选择，但作为初学者，最好从 NPM 开始。

除了包管理器，您还需要熟悉模块捆绑器和构建工具，因为它们是运行任务和处理文件的前端工作流的另一个重要部分。您可以使用它们将您的 SASS 文件编译成 CSS，将您的 ES6 JavaScript 文件向下转换成 ES5 以获得更好的浏览器支持，运行本地 web 服务器，以及许多其他有用的任务。你会经常遇到的最流行的工具是 gulp、Webpack 和 package。你必须选择一个最适合你的项目。

在这个阶段，接下来要学习的是版本控制。版本控制跟踪您在项目文件中所做的每个代码更改。如果你犯了一个错误，你甚至可以恢复到以前的改变。最流行的版本控制系统是一个叫做 Git 的开源系统。使用 Git，您可以将所有文件及其更改历史存储在称为存储库的集合中。你可能也听说过 GitHub，这是微软旗下的一家在线托管公司，在这里你可以存储你所有的 Git 库。

# 再深入一点

此时，您可以选择钻研额外的前端技能或学习后端 web 开发。如果您选择更深入地学习前端，那么您将需要一些中级技能。我建议你看看 SASS、响应式设计和 JavaScript 框架。SASS 是 CSS 的扩展，使写作风格更加直观和模块化。使用 SASS，您可以将 CSS 分成多个文件以便更好地组织，创建变量来存储颜色和字体，并使用混合和占位符进行重用。即使你只是利用了一些基本的特性，比如嵌套，你也能更快地编写出你的风格，更少的麻烦。

响应式设计确保您的风格在所有设备上都很好看。响应式设计的核心实践包括使用灵活的元素尺寸，以及利用媒体查询来确定特定设备和宽度的目标样式。由于移动流量超过了桌面流量，用响应性 CSS 构建你的网站是必须的。

在掌握了普通 JavaScript 的基础知识之后，您可能想学习一种 JavaScript 框架——尤其是如果您想成为一名全栈 JavaScript 开发人员的话。框架带有预构建的结构和组件，允许您比从零开始更快地构建应用程序。目前，您有三个主要选择:反应，角度和 Vue。React 从技术上来说是一个库，是目前最流行的框架。事实是，没有什么是最好的框架。很少有一个选择在任何情况下对每个人都是 100%的最佳选择。你选择的框架将取决于你的工作或你的舒适程度。

不用太担心选择哪个框架。更重要的是你要学习和理解它们背后的概念。一旦你掌握了一个框架，之后学习其他的就容易多了。

# 后端

web 开发的后端或服务器端由三个主要组件组成。服务器、服务器端编程语言和数据库。正如一开始提到的，服务器是一台计算机，所有的网站文件、数据库和其他组件都存储在这里。传统的服务器运行在 Linux 或 Windows 等操作系统上，并且是集中式的，因为所有的东西都存储在服务器上。如今，出现了无服务器架构，这是一种更加分散的设置类型。这种类型的应用程序提供这些组件，并利用第三方供应商来处理每个组件。尽管名字如此，你仍然需要一个网络服务器来至少存储你的网站文件。AWS 和 Netlify 就是无服务器提供商的一些例子。

无服务器设置很受欢迎，因为它们快速、便宜，而且你不需要担心服务器维护。它们非常适合不需要传统服务器端语言的简单静态网站。然而，对于非常复杂的应用程序，在服务器上建立已久的服务器设置可能是更好的选择。

要为你的应用程序编写函数和逻辑，你需要一门编程语言。服务器将编译您的代码，并将结果反馈给客户端。流行的 web 编程语言包括 PHP、Python、Ruby、C#和 Java。还有一种形式的服务端 JavaScript，Node.js，这是一个可以在服务器上运行 JavaScript 代码的运行时环境。

最后，你需要了解数据库。顾名思义，数据库是你在服务器上为你的网站存储信息的地方。大多数数据库使用一种叫做 SQL 的语言，它代表结构化查询语言。数据驻留在数据库的表中，有点像复杂的 Excel 文档，用户可以在上面用 SQL 编写查询来创建、读取、更新和删除数据。数据库运行在使用服务器的服务器上，例如 Windows 服务器上的 Microsoft SQL Server 和 Linux 上的 MYSQL。

还有一些非 SQL 数据库将数据存储在 JSON 文件中，而不是传统的表中。一种非 SQL 数据库是 MongoDB，它经常与 React、Angular 和 Vue 应用程序一起使用。

# 结论

作为一个初学者，我建议你按照以下步骤开始你的 web 开发之旅。从这里开始，如果你想深入兔子洞，你会学到不同的筹码。编码快乐！
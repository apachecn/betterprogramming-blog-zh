# Node.js 如何改变后端工程的前景

> 原文：<https://betterprogramming.pub/how-node-js-transformed-the-landscape-of-backend-engineering-cd05731f592c>

## Node.js 简史

![](img/4dd9cbcbe60b264411386e8c2eb56ff7.png)

本杰明·沃罗斯在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# B.N .(节点前)

在软件工程的大部分历史中，JavaScript 一直是一种功能有限的语言，因为它只能在浏览器中运行。

每个浏览器都有一个支持 JavaScript 运行的引擎，比如 [Mozilla 基金会的 Rhino](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/Rhino) ，或者 Internet Explorer 的 Chakra。这些引擎，加上某些独特的功能，使得 JavaScript 成为应用程序前端的强大选择，但却无法在后端使用。

它在浏览器中的限制意味着它不能在本地运行，也不能用来构建服务器。

# Chrome 的 V8 JavaScript 引擎

![](img/3156fdc4771382392588e1bb0a01450f.png)

2008 年，世界各地的软件工程师和互联网用户都收到了来自谷歌的一份令人难以置信的礼物:Chrome 浏览器。那年 9 月，谷歌同时推出了 Chrome 浏览器和构建该浏览器的 V8 JavaScript 引擎(该引擎是用 C++编写的)。

事实证明，V8 引擎可以说是目前最快、最强大的 JavaScript 引擎，它的开源状态允许其他开发人员从开发角度访问它。

![](img/ffad599cb62d17acbe430b662a0f8f83.png)

V8 引擎为在浏览器之外使用 JavaScript 提供了基础。服务器端 JavaScript 的其他选择以前也出现过，但没有一个在尝试中取得如此成功。

对于我们这些只知道 JavaScript 有了 [Node.js](https://nodejs.org/) 的人来说，这是理所当然的。但在当时，这是件大事。

Node.js 的最初开发者 Ryan Dahl 看到了 V8 引擎提供的机会，并开始向开源代码添加额外的功能。

最重要的是，Node.js 将支持 web 服务器的创建，并引入了事件循环。

# World，Meet Node.js

Node.js 的第一个版本是在 2009 年推出的，比 Chrome 的发布晚了不到一年，它包含了用 JavaScript 创建 web 服务器的功能。

除了允许创建服务器，Node.js 还使得在本地运行 JavaScript 代码成为可能。如果您曾经在 Ruby 中使用过 [IRB，Node.js 允许在您的终端中运行与 IRB 相同的代码。](https://docs.ruby-lang.org/en/2.2.0/IRB.html)

Netscape 的 LiveWire 是第一个使用 JavaScript 进行服务器端编程的尝试，但从未真正成功，Node.js 没有经历这种挣扎。

Node.js 运行时环境建立在强大的 V8 引擎上，并且在 JavaScript 语言已经开始流行的时候发布，它爆炸了，并且从那以后一直没有减缓。

明确的说，是的，Node.js 是一个*运行时环境。*它不是框架，不是库，也不是引擎。它是一个运行时环境，因为正如这个描述所暗示的，它是一个允许 JavaScript 代码运行的环境。

如果 V8 引擎和 Chrome(或者 Firefox，Safari 等。)创建允许 JavaScript 在浏览器中运行供用户交互的环境，Node.js 在后端创建 JavaScript 运行的环境。

# (节点后)

Node.js 打开了一扇大门，在这个世界中，编写全栈 JavaScript 不仅是可能的，而且是一个奇妙的选择。在整个应用程序中使用一种语言只会让事情变得更简单。

后端和前端之间有更多的流动性。开发应用程序的整个团队都在用同一种语言编写。前端工程师可以在需要时更容易地进入后端工作，反之亦然。最重要的是，它非常快，性能也非常好。

Node.js 为流行的后端框架如 [Express.js](https://expressjs.com/) 、 [Next.js](https://nextjs.org/) 、 [Meteor.js](https://www.meteor.com/) 铺平了道路。在 Express.js 和 React.js 之间，您可以在您的终端中仅用两行代码运行一个全栈应用程序。这真是太神奇了。

使用流行的 NoSQL 数据库选项，如 [MongoDB](https://www.mongodb.com/) (MERN，意思是)，你甚至可以在数据库中以 JSON 格式*存储数据。*

从数据库到用户，你有一致性，并且知道期待什么，这在软件工程中是一个强大的东西。
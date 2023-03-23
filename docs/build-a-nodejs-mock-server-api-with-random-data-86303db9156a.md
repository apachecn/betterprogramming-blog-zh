# 用随机数据构建 Node.js 模拟服务器和 API 的 5 个步骤

> 原文：<https://betterprogramming.pub/build-a-nodejs-mock-server-api-with-random-data-86303db9156a>

## 创建一个模拟 API 进行开发

![](img/19b01faa6ad34fb25dd095ecbc9b9b72.png)

[泰勒·维克](https://unsplash.com/@tvick?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

作为前端开发人员，您经常需要来自 API 的数据。但是有时候你的团队还没有建立后端。为了让您继续模拟您的数据，最好不要将您的数据结构存储在内存中。

尽快开始与数据来源的外部 API 对话是一个好主意。在本教程中，我想通过一个最小的设置来模拟你的数据。之后，您应该能够根据需要用自己的数据扩展它。

# 1.属国

为了使用 Node，您需要在计算机上安装它。对于 Mac 用户来说，我强烈建议安装 nvm，因为它会让以后更新 Node.js 更容易。还有一个 Windows 版本。

为此项目创建一个新文件夹。运行`npm init -y`用 Node 初始化文件夹，它会自动为你创建一个`package.json`。

为了创建模拟服务器，我们需要两个 npm 依赖项:`[json-server](https://www.npmjs.com/package/json-server)`和`[casual](https://www.npmjs.com/package/casual)`。我们在项目中运行`npm install json-server casual — save-dev`。

# 2.模拟服务器的基础

创建一个`index.js`，并将这段代码粘贴到其中。

我们包括`json-server`是为了使用它。然后我们在`const server`中创建一个服务器实例。有了中间件，我们可以设置一些选项——比如静态文件的路径、CORS 等等。但是这里我们只是使用它，没有任何特定的选项。

港口非常重要。如果你想让它在一个服务器上运行，它会首先搜索是否有一个节点服务器的默认端口；否则，它会选择端口`3000`。

我们通过使用`server.user()`来包含`bodyParse`和中间件。之后，我们做一个控制台日志，这样您就知道模拟服务器正在运行。

# 3.为 100 个用户生成数据

创建一个名为`/users`的文件夹，并在其中创建一个`index.js`。

首先，包含 npm 包`casual`以便使用它。

对于特定语言，如果您不想使用英语默认值，请键入:

然后在它下面，我们需要导出一个模块，以便以后在根中的`index.js`中使用。

在这种情况下，我们使用`server.get()`作为`GET`请求。但是我们也可以选择`post`、`put`、`delete`等等。

在内部，我们检查请求是否是一个`GET`请求。如果是这样，我们需要用户的脚本并调用响应中的函数，这样您将看到随机生成的用户数组。

# 4.创建/用户端点

为了获得 100 个用户的数据，我们需要为模拟服务器创建一个端点。将下面的代码粘贴到`root index.js`中，就在`server.listen()`函数之前。

```
server.get(‘/users’, (request, response) => {
 if (request.method === ‘GET’) {
   const users = require(‘./users/index’)
   response.status(200).jsonp(users())
 }
})
```

在这种情况下，我们对一个`GET`请求使用`server.get()`。但是我们也可以选择`post`、`put`、`delete`等等。

在里面。我们检查请求是否是一个`GET`请求。如果是这样，我们需要用户的脚本并调用响应中的函数，这样您将看到随机生成的用户数组。

# 5.运行模拟服务器

现在，我们能够运行这个模拟服务器，并在我们的前端应用程序中获取数据。

在项目的根文件夹中运行`node index.js`。访问`[localhost:3000/users](http://localhost:3000/users)`，您将看到用户数组中有 100 个用户。

我希望本教程为您生成更多随机数据和扩展您的模拟服务器奠定了基础。

# 阅读更多

[](https://medium.com/dev-together/frontend-development-on-a-budget-raspberry-pi-4-4c917124d348) [## 预算上的前端开发:树莓 Pi 4

medium.com](https://medium.com/dev-together/frontend-development-on-a-budget-raspberry-pi-4-4c917124d348) [](https://medium.com/dev-together/5-tips-for-healthier-developers-ea21d1e826f) [## 给健康开发者的 5 个建议

### 你的身体和大脑需要关注

medium.com](https://medium.com/dev-together/5-tips-for-healthier-developers-ea21d1e826f) [](https://medium.com/dev-together/what-tech-i-use-in-2020-as-developer-f3269f857f0e) [## 作为开发者，我将在 2020 年使用什么技术

### 我在工作中使用的软件、硬件和其他东西

medium.com](https://medium.com/dev-together/what-tech-i-use-in-2020-as-developer-f3269f857f0e) [](https://medium.com/dev-together/javascript-concepts-you-need-before-starting-w-frameworks-libraries-25a325312b5c) [## 开始使用框架和库之前需要的 JavaScript 概念

### 在你熟悉它们之前不要开始

medium.com](https://medium.com/dev-together/javascript-concepts-you-need-before-starting-w-frameworks-libraries-25a325312b5c)
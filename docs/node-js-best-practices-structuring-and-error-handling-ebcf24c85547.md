# Node.js 中的代码结构和错误处理

> 原文：<https://betterprogramming.pub/node-js-best-practices-structuring-and-error-handling-ebcf24c85547>

## 节点最佳实践

![](img/d7418a89b47cf42f2f5a7d7349238ecd.png)

Clem Onojeghuo 在 [Unsplash](https://unsplash.com/s/photos/structure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Node.js 是编写应用程序的流行运行时。这些应用程序通常是许多人使用的生产质量的应用程序。为了使维护它们变得更容易，我们必须为人们设置一些遵循的准则。

在本文中，我们将了解编写 Node.js 应用程序时的一些基本最佳实践。

# 按组件构建解决方案

一个节点应用程序的代码应该像任何其他类型的应用程序一样被分成组件。大代码文件很难阅读和搜索。做任何事情也需要大量的滚动。

为了避免这种情况，我们可以将代码分成模块。Node.js 支持常见的 js 和 ES 模块，因此我们可以选择如何将文件组织到模块中。

# 将组件分层并在其边界内保持 Express

每个组件都应该包含层。它应该有单独的文件专门用于端点、幕后逻辑和数据访问代码。

关注点的清晰分离很容易模拟和测试。我们不应该将请求和响应对象传递到逻辑和数据访问层，因为这使得我们的代码在任何地方都依赖于它们。

# 将常用实用程序包装为 npm 包

Npm 包是有用的发布库，可以像 loggers、encryption 等一样在任何地方重用。如果我们写了自己的解决方案。这样，我们可以在任何地方分享它们，并在任何需要的应用程序中使用它们。

他们不必公开，所以我们不必担心暴露任何我们不想暴露的东西。

# 将 Express“应用程序”和“服务器”分开

我们应该将`app`对象与`server`入口点分开。这样，我们可以通过直接调用它们来测试控制器代码，而不是被迫通过调用 HTTP 调用来测试。

通过 HTTP 调用从测试中获取覆盖率报告要困难得多，而且速度也较慢。拥有大的代码文件也不好，因为它们很难阅读。

例如，我们可以将`app`与入口点分开，如下所示:

`index.js`:

```
const app = require('./app');
const http = require('http');
app.set('port', 3000);
const server = http.createServer(app);
server.listen();
```

`app.js`:

在上面的代码中，我们在`app.js`而不是`index.js`中创建了 Express `app`对象。我们导出最后一行中的`app`对象。

然后我们在`index.js`中导入它，用`app`调用`http.createServer`来创建 HTTP 服务器。最后，我们在它上面调用`listen`来监听请求。

# 使用环境感知和安全配置

我们从环境变量中读取应用程序的配置。例如，`dotenv` npm 包是从`.env`文件和操作系统的环境变量中读取环境变量的好方法。

例如，我们可以如下使用`dotenv`从`.env`文件中获取配置，然后如下检索它:

上面的代码将把配置从`env`文件加载到`process.env`，因为我们已经:

```
require('dotenv').config()
```

然后我们可以在任何地方随心所欲地使用这些值。鉴于`.env`已经:

```
DB_HOST=localhost
DB_USER=root
DB_PASS=s1mpl3
```

我们会得到:

```
{ 
  "NODE_VERSION":"10.17.0",
  "HOSTNAME":"813e55f05734",
  "YARN_VERSION":"1.19.1",
  "HOME":"/home/runner",
  "PATH":"/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
  "NODE_PATH":"/usr/local/lib/node_modules:/home/runner/node_modules",
  "PWD":"/home/runner",
  "TERM":"xterm-256color",
  "DB_HOST":"localhost",
  "DB_USER":"root",
  "DB_PASS":"s1mpl3"
}
```

作为`process.env`的值。

它还支持从其他文件和操作系统的环境变量列表中加载配置。

![](img/1bead401544095b50268030bc6070e51.png)

桑迪·米勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 使用 async-await 或 Promises 进行异步错误处理

回调地狱绝对是我们想要避免的。因此，如果我们必须将多段异步代码链接在一起，我们应该使用 promises 或者它的简写，`async`和`await`。

例如，我们可以通过写下以下内容来使用承诺来捕捉错误:

或者我们可以编写以下代码来做同样的事情:

两个例子都通过顺序运行异步代码来链接它们。它们每一个都依赖于前一个的值，所以我们别无选择，只能把它们链起来。

然而，使用承诺或`async`和`await`使这变得容易。在这两个例子中，我们都有`catch`来捕捉错误。他们只是位于不同的地方。

# 结论

有一些简单的方法可以清理 Node.js 代码。我们应该将大块代码分成小模块。此外，我们应该通过使用承诺或`async`和`await`来消除回调嵌套。

我们还可以将环境变量存储在一个安全的位置，并通过库获取它们。
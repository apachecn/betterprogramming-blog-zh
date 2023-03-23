# 使用 Express 和 Webpack 构建具有实时重装功能的 API

> 原文：<https://betterprogramming.pub/build-an-app-with-live-reloading-using-express-and-webpack-787ea00c8eb4>

## 通过实时重载更快地构建 API

![](img/7fec45b81cc75c28da52cde7c52d880e.png)

杰西·拉米雷斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

以前，我使用 [nodemon](https://nodemon.io/) 来自动重启我的 Express 服务器，每次我修改开发中的一些文件。它工作了，但我想开始将我的 Express 应用程序与 webpack 捆绑在一起，以受益于缩小化，并使用 webpack 的许多可用插件中的一些。

我花了一段时间让它按照我想要的方式工作，并支持实时重装。这是我找不到的教程。

# 入门指南

## 设置 Express

注意:如果您已经设置了 Express(像我一样)，您可以跳过这一部分。

安装 npm 软件包:

```
$ npm install --save express
```

现在让我们创建一个简单的服务器向世界问好！

太好了！让我们在本地运行它。

```
$ node src/index.js
```

现在，让我们设置 webpack。

## 设置 webpack

如果您遵循官方的[入门](https://webpack.js.org/guides/getting-started/)指南，很容易迷路，因为它假设您正在捆绑一个 web 应用程序，但我们正在捆绑一个节点应用程序。所以让我们从一些基本的 webpack 配置开始。不改几个就不行了，还是过一遍吧。

首先，安装所需的软件包:

```
$ npm install --save-dev webpack webpack-cli
```

接下来，让我们创建一个基本的 webpack 配置文件:

现在让我们捆绑它！剧透:会有误差。

```
$ npx webpack
```

## 有一个错误

默认情况下，webpack 将为一个 *web* 环境构建，所以当您开始依赖本机节点模块时，您将会遇到问题，因为这些模块不能被捆绑。

所以我们需要确保`node`就是`target`:

再次运行`npx webpack`,应该会很好——尽管有一个警告:“依赖项的请求是一个表达式”这个警告甚至可能不会出现在你面前…我还没有弄清楚。

现在，您可以运行捆绑的 Express 应用程序:

```
$ node dist/main.js
```

太好了！现在，您已经有了一个与 webpack 捆绑在一起的运行中的 Express 服务器。

然而，webpack 的一个很酷的功能是手表模式:

```
$ npx webpack -w
```

这将在每次检测到文件更改时重新捆绑应用程序，让您以更快的速度进行开发。但是，watch 开箱不做的，其实是替你运行服务器(它只捆绑它)，所以每次还是需要手动重启……除非我们引入 nodemon。

正如一开始提到的，nodemon 会观察文件的变化，并为您重新启动应用程序。

所以，让我们用 webpack 开始吧！

## 使用 nodemon 实时重载

您可以单独安装和运行 nodemon，但幸运的是有一个 webpack 插件可以轻松地将两者结合起来。所以让我们安装`nodemon-webpack-plugin`:

```
$ npm install --save-dev nodemon-webpack-plugin
```

现在我们只需要再次修改我们的 webpack 配置来启用它:

运行`npx webpack -w`，你会看到`webpack watch`不仅会捆绑应用程序，还会为我们运行服务器！

继续修改您的源代码，当您点击*保存*时，您将看到 webpack 重新捆绑应用程序并自动重启服务器。

# 资源

这些是官方的入门指南:

*   [Express.js](https://expressjs.com/en/starter/installing.html)
*   [网络包](https://webpack.js.org/guides/getting-started/)
*   [nodemon-webpack-plugin](https://www.npmjs.com/package/nodemon-webpack-plugin)

# 结论

我希望这个教程对你有用。如果你有任何不确定的地方，请随时问我，或者提供任何建议！
# 如何锁定您的 Api_key 以及为什么它很重要

> 原文：<https://betterprogramming.pub/locking-down-your-api-key-and-why-is-important-ada0ee366654>

## 在代码中不指定 API 键的情况下运行应用程序

![](img/f9bc6db3403e01f45bb5fe2488dc6edf.png)

乔恩·摩尔在 Unsplash[上的照片](https://unsplash.com/search/photos/locked?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

你会把钥匙留在大停车场的车里吗？没有吗？那你为什么在你的 GitHub 项目中公开你的 api_key 呢？

使用云服务部署应用程序是常态，但这也带来了安全复杂性。云服务需要凭证，通常是 API 令牌的形式。

狡猾的黑客搜索这些令牌，以用作挖掘加密货币的计算资源或访问敏感数据。

一种常见的做法是扫描 web 和公共工具，如 GitHub，以搜索在不知不觉中被公开访问的 API 密钥。这给用户和云服务提供商都带来了巨大的风险。

# API 键的问题是

主要问题是，从历史上看，开发人员将 api_key 存储在代码中。

下面的代码并不难找到。

```
# src/keys/api_key.js
SOME_SERVICE_API_KEY = '2es8-473t-43ef-a34d' # Don't tell anyone!
```

许多开发人员不断地在代码中暴露他们的 API 密钥，然后他们推进他们的 repo，瞧！

显然，这是最不安全的方法，因为你正在披露一个重要的秘密，这大大增加了泄露你的秘密的风险。

我必须开发一个项目作为一项任务，其中一项要求是消费 [*电影数据库 API*](https://developers.themoviedb.org/3) 服务。

方法是在每次调用服务时提供 api_key。在我的情况下，当应用程序运行时，密钥不会完全隐藏。

无论如何，这对我来说是一个学习如何避免在代码中存储密钥的好机会。我借此机会在这里分享一下我的经历。你可以在我的 [GitHub 项目](https://github.com/lilicaway/movierama)中获得所有细节。

我使用 ES6、 [TypeScript](https://www.typescriptlang.org/) 和 [webpack](https://webpack.js.org/) 实现了这个任务。请注意，这个解决方案可以在任何其他使用 webpack 的环境中工作。

# 使用 Webpack 添加 API 密钥

在我的例子中，我想要一个非常简单的解决方案，只需为每个部署脚本设置 api_key。

我使用了 webpack 的`[DefinePlugin](https://webpack.js.org/plugins/define-plugin/)`，它允许我将 api_key 创建为一个全局常量，可以在编译时进行配置。

```
new webpack.DefinePlugin({
  // Definitions...
});
```

我的 [webpack.config](https://github.com/lilicaway/movierama/blob/master/webpack.config.js) 文件中的定义如下:

注意用函数设置`module.exports`，而不是直接分配对象，这很重要。否则，您将无法访问`env`变量。

# 深入到配置文件中

配置应该能够在开发和生产模式下为应用程序提供服务。

当然，如果你使用的是 webpack，它还包括 HMR(热模式重载)。

这是如何在开发模式下为应用程序提供服务:

```
TMDB_API_KEY=<yourTmdbApiKey>
npm run serve-dev  -- --env.apiKey=${TMDB_API_KEY}
```

并且，在生产模式下提供服务:

```
TMDB_API_KEY=<yourTmdbApiKey>
npm run build:webpack -- --env.apiKey=${TMDB_API_KEY} && \
 npm run serve-prod
```

请注意，在这两种情况下，我都需要明确地告诉 webpack 我正在传递密钥。

请注意，`--`本身作为一个参数在所有 Unix 命令中都是标准化的。这意味着进一步的参数应被视为位置参数，而不是选项。

在这个上下文中，这意味着`--`之后的参数应该发送到命令`serve-dev`或`build:webpack`，这取决于您正在运行的脚本。

我在我的 webpack 文件的开头添加了以下代码，以确保运行应用程序的任何人都知道运行脚本和加载应用程序需要 api_key。

此时，我已经在配置文件中做好了一切准备。我正在导入这个模块，并返回运行命令后得到的 api_key。

我在`api_key.js`文件中有下面的代码，它位于我消费服务的地方。

我的`package.json`中的脚本看起来像:

# 测试脚本

但是，测试脚本会发生什么呢？

通常我们不需要 API 键来测试，我们应该模拟调用。我知道，但是在我的例子中，我在测试中有一些代码间接调用返回 api_key 的函数。

如果你也是类似的情况，你也可以和我一样。为了避免使用正确的 api_key，我将这段代码添加到了`[babel.js](https://babeljs.io/)`:

现在，只需运行 npm 测试脚本，无需传递 apy_key:

```
npm run test
```

这就是你所需要的，现在你应该准备好运行你的应用程序，而不需要在代码中指定 API 键。

感谢阅读！如果你有任何建议或想法，请告诉我。
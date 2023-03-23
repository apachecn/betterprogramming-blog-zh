# 以下是如何设置 Webpack 4

> 原文：<https://betterprogramming.pub/how-to-set-up-webpack-4-49012dedbfd6>

## 我做了调查所以你不用做了

![](img/bad17d3184e800cead7d07ce5e9def24.png)

[法比安·格罗斯](https://unsplash.com/@grohsfabian?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

每个人都有配置 Webpack 4 的问题。Webpack 一开始对我来说很复杂，很难理解。我开始寻找如何做到这一点，我在网上找到了一百万个教程，所以你可能已经看到了一千种不同的方法来配置一个 Webpack 文件。

但是我想在没有`.babelrc`文件的情况下在 webpack 4 上配置 web app，并且我想谈论更多细节上的散列。在本指南中，我们将逐步了解 ES6 的 Webpack 4 配置。

在本教程中，您将学习:

*   如何安装和配置 Webpack 4
*   没有`.babelrc`如何安装配置 Babel
*   如何安装 React
*   如何使用`html-webpack-plugin`
*   哈希(`hash`、`chunkhash`、`contenthash`)
*   如何安装和配置 Webpack 开发服务器

如果你完全不熟悉 Webpack，你可以阅读文档。Webpack 有很好的文档，其中有很多解释。

# 设置项目

创建一个新目录并移动到`webpack-tutorial`:

```
mkdir webpack-tutorial && cd ./webpack-tutorial
```

初始化项目:

```
npm init
```

我们需要从我们的终端下载`webpack 4` 和`webpack-cli` 来运行它:

```
npm install webpack webpack-cli --save-dev
```

最后但同样重要的是，在`package.json`中添加 webpack 命令:

现在，我们没有 webpack 的配置文件。

在`src`文件夹中创建一个`index.js`文件，并写下以下几行:

```
console.log("Hello World");
```

现在，使用以下脚本编辑您的`package.json`:

在 Webpack 4 中，你有这些模式:*生产*和*开发*。

# **设置反应**

在使用 React 之前，您应该安装`react`和`react-dom`。

```
npm install react react-dom --save
```

接下来，你需要转换你的 JS 代码，因为现代的 JS 代码大部分是用 ES6 写的，并不是所有的浏览器都支持 ES6。所以你需要转换它，把你的 ES6 代码转换成 ES5。你可以用**巴别塔**来说明。Babel 是一个工具链，主要用于将 ES5+代码转换成当前和旧版本浏览器或环境中的向后兼容版本的 JavaScript。设置`@babel/core`、`@babel/preset-env`、`@babel/preset-react`和`**babel-loader**`、**。**

```
npm install @babel/core @babel/preset-env @babel/preset-react babel-loader --save-dev
```

*   `@babel/core`:将 ES6 代码转换成 ES5 JavaScript 代码。
*   `@babel/preset-env`:允许你使用最新的现代 JavaScript。
*   `@babel/preset-react`:巴别预设将 JSX 电码转化为函数。
*   `babel-loader`:这个包允许使用 [Babel](https://github.com/babel/babel) 和 W [ebpack](https://github.com/webpack/webpack) 传输 JavaScript 文件。

让我们来创造你的`webpack.config.js`。将这些行放入您的配置文件 **:**

接下来，像这样创建你的`index.js` 文件:

# **设置 Webpack 配置**

让我们在`webpack.config.js`中创建应用程序目录的入口和输出点:

上面，我们用`filename: "[name].js"`定义了`filename`。该名称将是`app.js`,因为 Webpack 将它取自条目密钥名称。

接下来，我们应该创建一个 HTML 文件来服务我们的 Webpack 包。因此，我使用`html-webpack-plugin`在 dist 文件夹中生成一个 HTML 文件。

```
npm install html-webpack-plugin html-webpack-template --save-dev
```

您应该在`webpack.config.js`中插入以下代码行:

现在我们知道如何将 JS 代码转换成 ES5。CSS 文件呢？为此，我们应该在我们的`package.json`中使用`style-loader`****`css-loader`**；`css-loader`加载所有 CSS 文件并合并成一个文件，`style-loader`在文档标签的样式中添加所有样式。******

```
****npm install style-loader css-loader --save-dev****
```

******现在，要将所有转换后的 CSS 提取到一个单独的“bundle”文件中，我们必须使用`mini-css-extract-plugin`。******

```
****npm install mini-css-extract-plugin --save-dev****
```

******当您处理一个项目时，您希望使用最新版本的代码。这是发展中最常见的问题之一。它是用哈希法修复的。Webpack 提供三种类型的散列:******

1.  ******[哈希]******
2.  ******[chunkhash]******
3.  ******[内容哈希]******

******`Hash` 是计算出来的一个构建。******

****`Chunkhash`是为 chunk(条目文件)计算的。****

****`Contenthash`是在`ExtractTextPlugin`中生成的特殊哈希，由提取的内容计算得出。`MiniCssExtractPlugin`也支持`contenthash`。请随意使用。****

# ******设置 webpack-dev-server******

****`webpack-dev-server`为 Webpack app 提供实时重装开发服务器。你可以查看关于它的官方文件。让我们安装`webpack-dev-server`:****

```
**npm install webpack-dev-server --save-dev**
```

****接下来，在您的`package.json`文件中更改脚本`start`:****

****您应该在`webpack.config.js`中插入以下代码行:****

*   ****`contentBase`:告诉服务器从哪里提供内容；****
*   ****`compress`:启用压缩一切；****
*   ****`port`:监听端口号；****
*   ****`open`:指定默认打开的浏览器；****
*   ****`watchContentBase`:告诉`dev-server`观看文件。****

****最后，我们的`webpack.config.js`:****

****我想在下一篇文章[讲讲如何优化 webpack 捆绑包缩小 Webpack 4 捆绑包大小的五个步骤](https://medium.com/@gapur.kassym/five-steps-to-reduce-webpack-bundle-size-8aebcd8e1562)。****

# ****资源****

*   ****链接到 GitHub:[web pack-教程](https://github.com/Gapur/webpack-tutorial)****
*   ****什么是 Webpack？****
*   ****[哈希 vs 块哈希 vs 内容哈希](https://medium.com/@sahilkkrazy/hash-vs-chunkhash-vs-contenthash-e94d38a32208)****
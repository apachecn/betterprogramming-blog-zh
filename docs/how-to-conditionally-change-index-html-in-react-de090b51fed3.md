# 如何有条件地改变 index.html 的反应

> 原文：<https://betterprogramming.pub/how-to-conditionally-change-index-html-in-react-de090b51fed3>

## 使您的 index.html 内容依赖于环境变量

![](img/6ee77b8420385216a7d1306b71531fd4.png)

[Jackson So](https://unsplash.com/@jacksonsophat?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

当开发一个 [React](https://reactjs.org/) 应用程序时，您可能需要根据您为构建的[环境来包含或更改脚本或 HTML 标签。在处理](/how-to-correctly-build-a-multi-environment-react-app-6ba4878e98ba) [SEO](https://en.wikipedia.org/wiki/Search_engine_optimization) 或跟踪工具时尤其如此，比如[谷歌分析](https://analytics.google.com/analytics/web/)或 [HotJar](https://www.hotjar.com/) 。

[](/how-to-correctly-build-a-multi-environment-react-app-6ba4878e98ba) [## 如何正确构建多环境 React App

### 为每个环境保留一个专用的构建文件夹

better 编程. pub](/how-to-correctly-build-a-multi-environment-react-app-6ba4878e98ba) 

你可能不知道的是，用 [Create React App](https://github.com/facebook/create-react-app) 初始化的 React 应用程序为你提供了这样做的可能性。虽然很难在网上找到它的文档，但它实际上是一个很酷的功能。事实上，只需几行代码，您就可以让您的`index.html`文件内容依赖于环境变量。

让我们来了解一下这个鲜为人知的功能，看看如何使用它。

# 带有 HtmlWebpackPlugin 的条件代码

`[HtmlWebpackPlugin](https://webpack.js.org/plugins/html-webpack-plugin/)`自带 Create React App，用于捆绑。具体来说，它使用`public/index.html`作为模板，用`[webpack](https://webpack.js.org/)`提供的路径注入一个`<script>`，生成最终的 HTML 页面。

另外，`HtmlWebpackPlugin`为开发人员提供了使用以下语法处理条件代码的能力:

```
<% if (condition) { %>
    // code inserted in the final HTML file only if
    // condition is true
<% } %>
```

您也可以如下使用`else`和/或`if else`分支:

```
<% if (condition1) { %>
    // code inserted in the final HTML file only if
    // condition1 is true
<% } else if (contition2) { %>
    // code inserted in the final HTML file only if
    // condition1 is true
<% } else { %>
    // code inserted in the final HTML file only if
    // both condition1 and contition2 are false
<% } %>
```

另外，在调试时，您可能还需要打印变量或条件的值。您可以使用以下语法来实现:

```
<%= variable %>
```

在一个`[npm build](https://docs.npmjs.com/cli/v6/commands/npm-build)`或`[npm start](https://docs.npmjs.com/cli/v7/commands/npm-start)`进程结束时，您会发现它的值打印在生成的 HTML 文件中。

# 条件代码在起作用

比方说，您希望只有您的 production React 应用程序被 Google 抓取，并且 Google Analytics 只出现在产品中。您可能会考虑编写两个定制的构建脚本，但这不是必需的。

事实上，你可以轻松实现这两个结果。您只需要根据当前环境有条件地更改您的`public/index.html`文件的代码，如下所示:

发生的情况是，只有当`process.env.NODE_ENV`具有所需的值时，用上述特殊语法编写的两个部分才会包含在最终的 HTML 文件中。这个环境变量是 Create React App 的一部分，如官方文档中所述。

> “[……]一个名为`NODE_ENV`的内置环境变量。可以从`process.env.NODE_ENV`开始读。当你运行`npm start`时，它总是等于`'development'`，当你运行`npm test`时，它总是等于`'test'`，当你运行`npm run build`来制作一个生产包时，它总是等于`'production'`。您不能手动覆盖`NODE_ENV`。— [创建 React 应用程序](https://create-react-app.dev/docs/adding-custom-environment-variables/)

瞧啊！您的`index.html`内容现在取决于环境变量。

# 结论

今天，我们看了如何根据环境变量改变`index.html`文件。实现这个目标很容易，只需要几行代码。特别是，由于默认情况下 Create React App 自带的`HtmlWebPackPlugin`，这成为可能。这个有用但并不广为人知的 React 特性可以让您避免为面向环境的构建编写定制脚本，从而节省您的时间。

感谢阅读！我希望这篇文章对你有所帮助。请随意留下任何问题、评论或建议。
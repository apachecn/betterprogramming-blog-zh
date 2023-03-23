# JavaScript 工具拯救世界

> 原文：<https://betterprogramming.pub/javascript-tooling-to-the-rescue-2279742a8d1e>

## Webpack、Babel、ESLint 和 Prettier 的优势

![](img/20ef45c34dedff399c0012d48b5222a1.png)

[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/tools?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

如今，JavaScript 开发人员可以使用许多开发工具来简化我们的工作。这些工具让我们不再担心与应用程序开发相关的琐碎任务，而是专注于真正重要的部分，即编码。

在这篇文章中，我将展示几个在开发者中最流行的 JavaScript 工具: [Webpack](https://webpack.js.org/concepts/) 、 [Babel](https://babeljs.io/docs/en/) 、 [ESLint](https://eslint.org/) 和[prettle](https://prettier.io/docs/en/index.html)。如果你还没有在你的项目中使用过它们中的任何一个，相信我，你就错过了。一定要在你的下一个项目中给他们一个尝试。

# 网络包

Webpack 是 JavaScript 开发人员中流行的模块捆绑器。它将应用程序中大量的 JavaScript 模块作为输入，并将它们打包成一个或几个 JS 文件，您可以轻松地将它们包含在 HTML 文档中。

现代的 web 应用程序包含大量的 JS 文件，因为我们倾向于在应用程序中使用越来越多的动态特性。虽然添加的功能对用户来说很棒，但是在没有额外支持的情况下管理这些模块以及应用程序中使用的其他第三方依赖项对开发人员来说可能会变得相当头疼。

例如，您必须手动控制每个脚本在浏览器上的执行顺序。加载许多文件也会影响应用程序的性能。

有了 Webpack 的使用，你就不用再担心这些问题了。Webpack 轻松高效地为您处理这项业务。它从一个入口点开始分析应用程序的 JS 模块，并构建一个依赖图。在加载器和插件的帮助下，它可以处理诸如处理依赖性、决定加载优先级和解析路径等任务。

不仅仅是针对 JavaScript。您也可以使用 Webpack 的 css-loader 来管理 css 文件。

Webpack 允许您在代码中定义分割点，以便将输出捆绑到几个 JS 文件中，而不是一个长文件。它给了你只在需要的时候加载部分代码的自由。

这种随需应变的延迟加载可以有效地提高应用程序的性能。只有当用户做了一些需要它们的功能的事情时，才加载新的代码块。

特别是如果你正在使用 React 这样的框架，Webpack 提供的功能——拆分和捆绑代码，处理依赖模块——将省去你自己管理它们的许多麻烦。

# 巴比伦式的城市

Babel 是最流行的 JavaScript trans piler。它将 ES6 JavaScript 代码转换成 ES5 JavaScript，这样代码甚至可以在旧版本的浏览器中运行。

Transpilers 是以一种语言编写的代码作为输入并将其转换为另一种语言的工具。在 Babel 的例子中，它主要取 ES6 代码，输出 ES5 代码。

以下面使用 ES6 中新引入的类编写的代码片段为例。

巴别塔会把它转换成这样的 ES5。

如果这就是 Babel 所做的一切，为什么要在项目中使用 Babel 呢？尽管开发人员接受了 JavaScript 中引入的新 ES6 特性，但一些浏览器还没有跟上 ES6 支持的步伐。

如果您的 web 应用程序的用户正在使用这些浏览器中的一种，或者只是使用旧版本的浏览器，他们将无法利用您添加到应用程序中的所有优秀特性。为了确保每个人都可以访问你的应用程序，Babel 介入并将你的代码传输到 ES5，这是所有浏览器都支持的。

在所有浏览器都开始支持 ES6 之后，你应该停止使用 Babel 吗？答案是否定的。尽管目前的焦点是 ES6，但 ECMA 每年都会发布新的 JavaScript 更新。浏览器总是需要一些时间来赶上最新的标准。

但是有了 Babel 的支持，它更快地采用了最新的标准，在开发应用程序时，您不必担心转换到使用新的 JavaScript 特性。

如果你是一个 React 开发者，想要在你的代码中使用 ES6 的特性或者 JSX，让 Babel 帮助你把代码移植到 ES5 是明智的选择。还有，可以用 Babel 配合 Angular 来写 ES6 代码。

除了 ES6，Babel 也可以将 TypeScript 转换成 ES5。

# 埃斯林特

ESLint 是大多数 JavaScript 开发人员的默认 linter。它帮助您编写更清晰的代码，并遵循您所遵循的编码惯例。

linter 的任务是分析你的代码，标记其中的编程和风格错误。使用 ESLint，您可以完全控制应该标记哪些错误以及它们的错误级别。您可以将规则的错误级别定义为*错误*、*警告*或*禁用*。

ESLint 对于发现错误非常有用，比如未声明的变量赋值和与变量作用域相关的问题。

您还可以使用规则在代码中强制执行某些编码约定。例如，您可以定义规则来禁止使用 await 内部循环或禁止使用 console。

如果您参与的项目遵循特定的样式指南和编码约定，您可以定义 ESLint 规则来帮助您与它们保持一致。如果您遵循的样式指南对连续空行有限制，您可以在规则中指定这一点。然后，每当您错误地留下太多空行时，ESLint 就会在您的代码中显示一个错误。

# 较美丽

漂亮是一个代码格式化程序。您可以使用它来自动格式化代码，而不必手动检查样式错误。

尽管它附带了一组适用于其首选样式的规则，但您可以设置自己的规则来反映您遵循的样式指南。然后你所要做的就是保存源文件，让 Prettier 格式化你的代码来适应定义的规则。

类似于 ESLint，appellist 帮助你写出符合你所选择的风格指南的更干净的代码。ESLint 和 pretty 在加强样式方面的区别在于，ESLint 只显示你做错的地方，而 pretty 则自己进行格式化。

但重要的是要明白它们是不可互换的。ESLint 提供了比检查样式错误更多的用例。

# 结论

JavaScript 生态系统依赖于帮助开发人员解决问题和构建惊人应用的工具。这些工具为干净的代码、兼容性问题和性能提供支持。

正是因为有了这些工具，像 React 这样的框架才成为可能，JavaScript 也不仅仅出现在 web 上。

同样真实的是，这些工具中的一些只是为了修复 JavaScript 遗留给我们的“烂摊子”。

感谢阅读！
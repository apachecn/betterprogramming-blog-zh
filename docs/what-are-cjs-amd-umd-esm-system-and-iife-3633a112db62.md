# 什么是 CJS，AMD，UMD，ESM，System，IIFE？

> 原文：<https://betterprogramming.pub/what-are-cjs-amd-umd-esm-system-and-iife-3633a112db62>

## 用汇总示例说明模块格式

![](img/885b8bf4b2276fc77b570caa73e908a9.png)

由[法尔扎德·纳兹菲](https://unsplash.com/@euwars?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

现代 JavaScript 项目需要一个捆绑器来将小段代码编译成更大更复杂的东西，比如一个库或应用程序。常见的捆扎机有 [webpack](https://medium.com/better-programming/micro-frontends-using-webpack-5-module-federation-3b97ffb22a0d) 、 [Rollup](https://rollupjs.org/guide/en/) 、[package](https://parceljs.org/)、[requires](https://requirejs.org/)和 [Browserify](http://browserify.org/) 。它们将 JavaScript 代码转换成可以作为一个包加载的模块。

束可以以不同的格式排列。在本文中，我们将展示 CJS、AMD、UMD、ESM、System 和 IIFE 格式的真实示例。

# 捆扎机和格式

这是一个标准的 HTML 文件，第 5 行包含一个样式表，第 6 行包含一个 JavaScript 文件:

将所有 JavaScript 代码放在一个文件中对于一个简单的例子来说是可行的。随着项目规模的扩大，我们需要将代码模块化为具有独立名称空间的独立模块。除了更好的组织，模块化还带来了封装、依赖性管理和可重用性的能力。

这就是捆绑者进入画面的方式。需要将小块 JavaScript 代码连同样式表和图像一起编译成更大更复杂的东西，比如库或应用程序。

捆绑器应该如何将捆绑的代码格式化为输出？有许多选择，[roll up](https://rollupjs.org/guide/en/#outputformat)定义了以下格式:

*   `cjs` (CommonJS) —适用于 Node 和其他 bundlers(别名:`commonjs`)。
*   `amd`(异步模块定义)—用于像 RequireJS 这样的模块加载器。
*   `umd`(通用模块定义)—集`amd`、`cjs`和`iife`于一身。
*   `es`–将包保存为 ES 模块文件。适用于其他捆绑器，并作为`<script type=module>`标签包含在现代浏览器中(别名:`esm`、`module`)。
*   `system`–system js 加载程序的原生格式(别名:`systemjs`)。
*   `iife`–适合作为`<script>`标签包含的自执行功能。如果您想为您的应用程序创建一个包，您可能想使用这个。

在本文中，我们将通过例子来解释这些格式。

# 这个例子

下面是一个要捆绑四个文件的示例:

*   `index.js`
*   `increase.js`
*   `decrease.js`
*   `others.js`

主入口文件是`index.js`:

在第 4-6 行，`index.js`明确列出了从`increase.js`、`decrease.js`和`others.js`导入的内容。

这是进口的`increase.js`:

这是进口的`decrease.js`:

这是进口的`others.js`:

本例中没有使用`others.js`中的`const d`和`index.js`中的`function divide()`。`index.js`中的`function power()`也不用，但是导出。

ES2015/ES6 引入了静态`[import](https://medium.com/better-programming/a-handy-guide-to-export-and-import-modules-for-javascript-and-typescript-6cff8e47d554)` [和](https://medium.com/better-programming/a-handy-guide-to-export-and-import-modules-for-javascript-and-typescript-6cff8e47d554) `[export](https://medium.com/better-programming/a-handy-guide-to-export-and-import-modules-for-javascript-and-typescript-6cff8e47d554)`，允许静态分析器在不运行代码的情况下构建完整的依赖关系树。此外，这为树抖动优化奠定了基础。根据[维基百科](https://en.wikipedia.org/wiki/Tree_shaking):

> “树摇动通过从入口点开始并且仅包括可能被执行的函数，从束中消除了未使用的函数。”

由于这个例子是用 ES2015 风格写的，带有特定的导入(不是`import *`)，所以 rollup 树抖动过程会移除 ***中的`const d`和`function divide()`所有*** 生成的格式。`function power()`被保留，因为导出的功能可能会被使用。

# CommonJS (CJS)

[CJS](https://en.wikipedia.org/wiki/CommonJS) 适用于[节点](https://medium.com/better-programming/whats-new-in-node-js-15-fc24e87e2590)以及网页浏览器之外的其他生态系统。在[服务器端](https://medium.com/javascript-in-plain-english/a-hands-on-guide-for-a-server-side-rendering-react-app-dd1efa3ec0d8)广泛使用。使用`require()`功能和`module.exports`可以识别 CJS。`require()`是一个函数，可用于将符号从另一个模块导入到当前范围。`module.exports`是当前模块在另一个模块需要时返回的对象。

CJS 模块的设计考虑到了服务器开发。自然，API 是同步的。换句话说，模块是在源文件中按照需要的顺序加载的。

由于 CJS 是同步的，并且不能被浏览器本地识别，所以 CJS 模块不能在浏览器端使用，除非它与 transpiler 打包在一起。transpiler，如 [Babel](https://babeljs.io/) 或 [Traceur](https://github.com/google/traceur-compiler) ，是一种帮助在较新版本的 JavaScript 中编写代码的工具。如果环境本身不支持较新的版本，transpiler 会将它们编译成支持的版本。

以下是 CJS 格式的汇总生成文件:

我们在浏览器端执行这个文件，它出错并显示消息`exports is not defined`(第 3 行)。

可以通过在`index.html`中包含以下代码来修复错误:

```
<script>
  const exports = {};
</script>
```

# 异步模块定义(AMD)

[AMD](https://en.wikipedia.org/wiki/Asynchronous_module_definition) 诞生于 CJS，支持异步模块加载。AMD 和 CJS 的主要区别在于对异步模块加载的支持。AMD 是 RequireJS 用的，在浏览器端工作。

根据[维基百科](https://en.wikipedia.org/wiki/Asynchronous_module_definition):

> “AMD 提供了一些 CJS 互操作性。它允许在代码中使用类似的`exports`和`require()`接口，尽管它自己的`define()`接口更加基础和首选。”

以下是 AMD 格式的汇总生成文件:

我们在浏览器端执行这个文件，它出错并显示消息`define is not a function`(第 1 行)。

可以通过在`index.html`中包含`require.js`来修复错误:

```
<script src=”https://requirejs.org/docs/release/2.3.6/minified/require.js"></script>
```

# 通用模块定义(UMD)

UMD 被设计成可以在任何地方工作——在服务器端和浏览器端。它试图提供与当今最流行的脚本加载器的兼容性，比如 RequireJS。在许多情况下，它使用 AMD 作为基础，并添加了特殊的外壳来处理 CJS 兼容性。然而，兼容性增加了一些复杂性，使得读写变得复杂。

以下是 UMD 格式的汇总生成文件:

这段代码可以在浏览器中运行。

# ES2015 模块(ESM)

ESM 自 2015 年起成为 JavaScript 使用的官方标准。它在 JavaScript 客户端开发中被广泛使用，并且也被 TypeScript 所采用，TypeScript 是具有附加类型的超集。

从`^12.20.0 || ^14.13.1 || >=16.0.0`版本开始，[节点开始支持 ESM](/what-might-be-coming-in-npm-9-6985cf2678a6) 。ESM 越来越受欢迎，可用于客户端和服务器。

静态的`import`指令可以用来将模块引入当前范围。[动态](https://medium.com/better-programming/dynamic-import-code-splitting-lazy-loading-and-error-boundaries-fff57e63f6c4) `[import()](https://medium.com/better-programming/dynamic-import-code-splitting-lazy-loading-and-error-boundaries-fff57e63f6c4)`从 ES2020 开始提供。

另一方面,`export`指令可以用来显式地公开项目。

以下是 ESM 格式的汇总生成文件:

我们在浏览器端执行这个文件，它出错并显示消息`Uncaught SyntaxError: Unexpected token 'export'`(第 45 行)。

可以通过将`script`标签的`type`设置为`index.html`中的`module`来修复该错误:

```
<script type=”module” src=”dist/bundle.js”></script>
```

# 系统模件

[SystemJS](https://github.com/systemjs/systemjs) 是一款通用模块加载器，支持 CJS、AMD 和 ESM 模块。Rollup 可以将代码捆绑成 SystemJS 的原生格式。

以下是以系统格式汇总生成的文件:

我们在浏览器端执行这个文件，它出错并显示消息`System is not defined`(第 1 行)。

安装`system.js`:

```
npm install --save-dev systemjs
```

可以通过将`system.js`包含在`index.html`中来修复该错误:

```
<script src="node_modules/systemjs/dist/s.min.js"></script>
```

# 立即调用函数表达式(IIFE)模块

正如模块名所示，IIFE 是一个自执行函数，适合作为`<script>`标签包含。我们可以使用这种格式为应用程序创建一个包。它帮助我们将东西放入名称空间，以避免变量冲突并保持代码私有。

以下是以 IIFE 格式汇总生成的文件:

这段代码可以在浏览器中运行。

# 生成多种格式

`rollup.config.js`是一个汇总配置文件。它是可选的，但是功能强大、方便，因此值得推荐。

以下是我们用来一次生成多种输出格式的输出配置:

# 结论

我们探讨了 JavaScript 模块格式——CJS、AMD、UMD、ESM、System 和 IIFE。在这些格式中，ESM 很受欢迎，可用于客户端和服务器。

感谢阅读。我希望这有所帮助。你可以在这里看到我的其他媒体出版物。

*注:感谢 Daria Mehra 审阅本文。*
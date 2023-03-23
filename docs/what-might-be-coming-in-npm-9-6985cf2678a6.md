# 快速浏览 npm 8 特性和对 npm 9 的预测

> 原文：<https://betterprogramming.pub/what-might-be-coming-in-npm-9-6985cf2678a6>

## 近距离观察 ES 模块(ESM)

![](img/ffc50bcceb673c10149e6cd4b78acc7e.png)

由[真实机构](https://unsplash.com/@trueagency?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

npm 是 node.js JavaScript 平台的包管理器。它将模块放在适当的位置，以便节点可以找到它们，并智能地管理依赖冲突。

npm 是可配置的，以支持发布、发现、安装和开发节点程序的各种用例。它有一个强大的命令列表。

[npm 8](https://newreleases.io/project/npm/npm/release/8.0.0) 发布于 2021 年 10 月 7 日。在本文中，我们看一下 npm 8，并预测 npm 9 可能是什么。

# npm 8 有什么新功能？

npm 8 没有什么新东西。该版本的目的是放弃对旧节点版本的支持，并移除对`require('npm')`的支持。没有其他突破性的变化。更具体地说，这些变化是:

*   删除对节点 10 和 11 的支持。
*   将节点 12 和 14 的支撑上限提高到 LTS ( `^12.13.0` / `^14.15.0`)。
*   将支架降至`require('npm')`。
*   由于删除了 node10 和 node 11 支持，更新了一些依赖关系。

简单明了。

如果你想升级到 npm 8，确保你的 node.js 已经升级到版本`>=12.0.0`。`[nvm](/how-to-use-nvm-to-manage-node-js-17-and-npm-8-2da8bf3ca5e9)` [是管理节点和 npm 版本的简单方法](/how-to-use-nvm-to-manage-node-js-17-and-npm-8-2da8bf3ca5e9)。

# npm 9 中可能有什么新内容？

征求意见稿(RFC)是由互联网工程任务组(IETF)起草的一份正式文件，描述了特定技术的规范。npm 8 是一个被认可的 RFC，目前是一个正式的标准。

npm 8 的 RRFC 提到了一个将支持版本提升到`^12.20.0 || ^14.13.1 || >=16.0.0`的建议，这将是向支持 ESM 风格模块的 node.js 版本的转移。因为它没有成为 npm 8，所以它可能是 npm 9 的一个特性。

## CJS 对欧洲稳定机制

我们已经讨论了 JavaScript 模块格式，例如 [CJS、AMD、UMD、ESM、System 和 life](/what-are-cjs-amd-umd-esm-system-and-iife-3633a112db62)。

CommonJS (CJS)是 node.js 用来在模块中封装 JavaScript 的标准。CJS 使用`require()`功能和`module.exports`。

*   `require()`是一个可以用来从另一个模块导入符号到当前作用域的函数。`require`语句可以在代码的任何地方使用，引用的模块同步加载和处理。
*   `module.exports`是当前模块在另一个模块需要时返回的对象。

ES 模块(ESM)从 ES2015 开始成为 JavaScript 使用的官方标准。它广泛应用于 JavaScript 客户端开发。它还被作为附加类型的超集的 TypeScript 所采用。 [ESM 使用](/a-handy-guide-to-export-and-import-modules-for-javascript-and-typescript-6cff8e47d554) `[import](/a-handy-guide-to-export-and-import-modules-for-javascript-and-typescript-6cff8e47d554)` [和](/a-handy-guide-to-export-and-import-modules-for-javascript-and-typescript-6cff8e47d554) `[export](/a-handy-guide-to-export-and-import-modules-for-javascript-and-typescript-6cff8e47d554)` [语句来处理模块](/a-handy-guide-to-export-and-import-modules-for-javascript-and-typescript-6cff8e47d554)。

*   静态的`import`指令可以用来将模块引入当前范围。[动态](https://medium.com/better-programming/dynamic-import-code-splitting-lazy-loading-and-error-boundaries-fff57e63f6c4) `[import()](https://medium.com/better-programming/dynamic-import-code-splitting-lazy-loading-and-error-boundaries-fff57e63f6c4)`从 ES2020 开始提供。`import`语句可以在代码中的任何地方使用。由于`imports`是异步加载的，所以建议将它们放在文件的顶部。
*   另一方面，`export`指令可以用来显式地将项目公开。

## CJS 是节点的默认值

正如我们提到的，CJS 是节点的默认设置。按照[生产就绪型 React 应用](https://javascript.plainenglish.io/a-hands-on-guide-for-creating-a-production-ready-react-app-864ad98e7497)中描述的步骤，我们使用 [Create React 应用](/10-fun-facts-about-create-react-app-eb7124aa3785)作为示例来探索节点服务器是如何工作的。

```
npx create-react-app react-esm
cd react-esm
```

执行命令`npm run build`，生成的`build`目录中包含了需要部署的代码。

[Express](https://expressjs.com/) 是一个面向 web 和移动应用的极简灵活的 Node.js web app 框架。Express server 是部署生产版本的常见选择。

由于 Express 是 Create React 应用程序的一部分，因此无需再次安装。

在`server/index.js`设置 Express 服务器的配置文件:

上面的代码显然是 CJS 格式的，带有`require`语句(第 1 行和第 4 行)。

第 2 行创建了一个 Express 服务器。

第 5–8 行用于制作网页。

第 10–12 行在港口`8080`启动 Express 服务器。

运行`node server`，在`http://localhost:8080`可以进入用户界面。

## 设置运行 ESM 的节点

现在我们修改代码，用`import`代替`require()`。

运行`node server`，我们看到以下错误:

第 10 行的警告提供了两种解决方案:

*   将`"type": "module"`设置在`package.json`中。
*   将`server/index.js`改为`server/index.mjs`，运行`node server/index.mjs`。

两种解决方案都可行。下面是修改后的`package.json`(第 5 行):

对于没有扩展名的文件，如果没有`"type"`，或者在父`package.json`中将类型设置为`"commonjs"`，则被视为 CJS 模块。如果`"type"`设置为`"module"`，它们将被视为 ES 模块。

另外，以`.cjs`结尾的文件被视为 CJS 模块，以`.mjs`结尾的文件被视为 es 模块。

我们将`"type"`设置为`"module"`，现在节点将文件视为 es 模块。又跑`node server`了:

它不再抱怨`import`了。但是，什么是`__dirname`问题呢？

`__dirname`是 CJS 变量，ES 模块中没有。可以通过`import.meta.url`复制。

`import.meta`对象向 JavaScript 模块公开特定于上下文的元数据。它包含关于模块的信息，比如模块的 URL。

修改`server/index.js`如下:

运行`node server`，ES 模块完美工作。

或者，我们可以使用`node:`前缀，在这种情况下，它会绕过 require 缓存。例如，`"node:path"`(第 4 行)和`"node:url"`(第 5 行)将总是返回内置的`"path"`和`"url"`模块。

我们已经看到了 CJS 和 ESM 之间的一些差异。以下是如何将 CJS 文件转换为 es 模块的列表:

*   没有`require`、`exports`或`module.exports`—使用`import`或`export`代替。
*   没有`__filename`或`__dirname`——使用`import.meta.url`代替。

*   没有 JSON 模块加载——使用`import.meta.url`和`fs`代替。

*   无本地模块加载—使用`module.createRequire()`或`process.dlopen`代替。

*   否`require.resolve`—使用`new URL('./local', import.meta.url)`代替。
*   不要使用符号链接。
*   否`require.extensions` —请勿使用。
*   否`require.cache` —请勿使用。

# 结论

npm 8 不再支持节点 10 和 11。

你对 npm 9 有什么看法？它应该放弃对不支持 ES 模块的节点版本的支持吗？

在为 JavaScript 客户机和服务器的不同语法而斗争之后，看到 ESM 获得了可用于客户机和服务器的流，难道不令人兴奋吗？

在节点应用程序中使用 ESM 式模块的时机已经到来。

感谢阅读。我希望这有所帮助。如果你有兴趣，可以看看[我的其他媒体文章](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af)。
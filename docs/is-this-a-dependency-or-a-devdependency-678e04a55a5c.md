# 这是依赖还是 DevDependency？

> 原文：<https://betterprogramming.pub/is-this-a-dependency-or-a-devdependency-678e04a55a5c>

## 在你的 package.json 中把 npm 模块放在哪里，并没有看起来那么简单。

![](img/feea853cf81d4ff90951cd05f3f76aa5.png)

[Bonnie Kittle](https://unsplash.com/@bonniekdesign?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

几天前，有人打开了一个对 [svelte-spa-router](https://github.com/ItalyPaleAle/svelte-spa-router) 的拉取请求——这是一个开源项目，我为用 svelte 框架构建的单页应用程序维护了一个客户端路由器——询问他们是否应该在`package.json`文件中将该模块作为`dependency`或`devDependency`安装。

除了这个案例的特殊性，我认为总的来说这是一个很好的问题——值得讨论。什么时候我们应该把`package.json`中的一个模块依赖作为`devDependency`？

# 医生怎么说

实际上有关于把包放在哪里的官方指导，这清楚地写在 [npm 文档](https://docs.npmjs.com/specifying-dependencies-and-devdependencies-in-a-package-json-file)中。准确地说:

> `dependencies`:您的应用在生产中需要的包。
> 
> `devDependencies`:只需要本地开发和测试的包。

简而言之，当一个模块只用于开发和测试时，你应该把它保存为一个`devDependency`；其他都应该是个`dependency`。你可能认为这是简单明了的指导，但是就像我的模块的用户一样，在现实生活中事情会变得模糊不清。

真正的答案是*看情况。*选择将每个模块放在哪里不仅取决于模块本身，还取决于您的应用程序——甚至取决于它的开发和部署方式。

# 简单的时候…

有一些明显的例子——或者几乎是。像 [ESLint](https://www.npmjs.com/package/eslint) 这样的包*总是*一个`devDependency`…当然，除非你正在构建一个 CLI，它的工作就是运行 ESLint，在这种情况下，你会把它添加为`dependency`！

其他(几乎)总是会成为`devDependencies`的包包括:

*   测试框架:[摩卡](https://www.npmjs.com/package/mocha)、[超级测试](https://www.npmjs.com/package/supertest)、[夜巡](https://www.npmjs.com/package/nightwatch)等。，以及它们的依赖关系如 [ChromeDriver](https://www.npmjs.com/package/chromedriver)
*   代码覆盖工具，例如 [nyc](https://www.npmjs.com/package/nyc) 和[套装](https://www.npmjs.com/package/coveralls)
*   Linters 和代码格式化程序，例如已经提到的 [ESLint](https://www.npmjs.com/package/eslint) 、 [TSLint](https://www.npmjs.com/package/tslint) ，以及它们的插件/依赖项
*   用于本地开发的服务器，如 [serve](https://www.npmjs.com/package/serve) 、 [http-server](https://www.npmjs.com/package/http-server) 、 [sirv](https://www.npmjs.com/package/sirv) 等等
*   文档工具，如 [JSDoc](https://www.npmjs.com/package/jsdoc) 和 [TypeDoc](https://www.npmjs.com/package/typedoc)

对于运行在服务器端的 Node.js 应用程序，通常也是*清楚什么包应该是`dependencies`。经验法则是，如果应用程序导入了一个模块(例如，用一个`require('foo')`)，那么它应该是一个`dependency`。其他都是`devDependency`。*

# …如果不是这样

当我们处理客户端(在浏览器中运行)、预处理(例如，使用 TypeScript)或两者兼有的应用程序时，答案就不那么明确了。

对于这些情况，我的建议是考虑模块在应用程序中的角色，以及应用程序是如何构建和部署的。

# 客户端应用程序

让我们从基于浏览器的应用程序开始。现代前端开发包括捆绑器、预处理器，有时还包括直接编译器。

该工具链通常至少包括一个捆绑器( [webpack](https://www.npmjs.com/package/webpack) 、 [Rollup](https://www.npmjs.com/package/rollup) 、[package](https://www.npmjs.com/package/parcel)等等)或一个任务运行器(比如 [Grunt](https://www.npmjs.com/package/grunt) )，外加一个前端框架，比如 [React](https://www.npmjs.com/package/react) 、 [Angular](https://www.npmjs.com/package/angular) 、[svelet](https://www.npmjs.com/package/svelte)或 [Vue](https://www.npmjs.com/package/vue) 。此外，通常还有 transpilers(如 [TypeScript](https://www.npmjs.com/package/typescript) 和 [babel](https://www.npmjs.com/package/babel) )或其他预处理程序(如 CSS 和 JavaScript 文件)。

当您的前端应用程序被捆绑时，就像 svelte-spa-router 的用户一样，您可能会想知道将应用程序的每个依赖项放在哪里。既然只在捆绑阶段使用，那么应该都是`devDependencies`？

如前所述，这种情况引入了很多主观性，我能给你的最好建议是看看你的申请，然后根据你的喜好得出你的结论。以下是一些想法和看法。

首先，看看**应用程序是如何部署的**。理想情况下，在生产服务器中，您希望只安装生产依赖项。如果您的前端应用程序与后端应用程序一起部署，并且在应用程序启动时捆绑在一起，那么捆绑器和工具链的其余部分应该作为`dependencies`包含在内:事实上，它们是启动应用程序所需要的。

如果前端应用程序是预先**捆绑的**，例如在持续集成(CI)服务器中或者甚至在开发人员的机器上，那么您可以考虑依赖关系。

严格遵循官方文档，因为这些包只在构建时使用，它们应该被认为是`devDependencies`。然而，在这样做的时候，你会以所有的包都是`devDependencies`而结束，对我来说这感觉就像是打败了要点。

相反，在这种情况下，我的建议是将直接进入捆绑代码的包放到`dependencies`块中。

比如你前端应用导入的模块和框架本身(比如 React，Angular，Vue，Svelte 等。)，都将作为依赖关系放在`package.json`文件中。捆扎机、预处理器、传输机等。反而会以`devDependencies`的身份出现。

这样做的原因是，它清楚地表明了您实际上向您的用户发布了什么样的第三方代码，出于安全原因，这一点非常重要。事实上，它使得审计应用程序的软件供应链和评估依赖项的漏洞对项目的影响变得更加容易。

# 传输服务器端应用

另一种情况是使用 transpiled 的 Node.js 服务器端应用程序，比如当您使用 TypeScript 或 babel 时。

与上面的情况类似，我的建议是从了解应用程序是如何部署的开始。你会在部署之前还是之后运行 transpilation？

例如，如果您正在使用 TypeScript，您会在部署应用程序之前或每次在服务器中启动应用程序时运行`tsc`(TypeScript trans piler)作为构建步骤吗？

*   如果您在运行时运行 transpiler 来启动应用程序，那么 transpiler 本身和工具链的其余部分应该是`dependencies`
*   如果您在部署应用程序之前预先构建了它，那么您可以将 transpilers 和工具链的其余部分作为`devDependencies`。
    例如，当您使用构建服务器运行`tsc`，在将传输的文件复制到服务器之前，您正在开发人员的机器上运行它，或者当您使用多阶段 Docker-image 构建，其中第一阶段运行`tsc`时，就是这种情况。
    与上面前端应用的情况不同，transpilers 一般不会捆绑代码(虽然可以)，所以代码中直接导入的所有模块都需要`dependencies`。

# 运送 npm 包

上面的两个部分着眼于“完整的应用程序”，然后部署这些应用程序供最终用户访问。但是，如果我们正在处理一个要运送到 npm 的包呢？

在这种情况下，我们需要特别小心，让`dependencies`的列表尽可能小。原因是当有人从 npm 安装你的包时，他们会安装所有的`dependencies`而不是`devDependencies`。

根据您使用的技术，npm 上的包可以原样发布，不修改源代码(除了纯 JavaScript 项目之外，这包括前端组件，如 React、Svelte 等。)或者可以预先进行预处理。

例如，通常不在 npm 上发布 TypeScript 文件，而是发布 transpiled JavaScript 代码和类型定义文件。

无论哪种情况，我的建议都是一样的:不管你用什么工具链(如果有的话)来构建/传输/打包你的代码，这些包都是以`devDependencies`的形式存在的。

事实上，如果你发布预处理代码，没有必要让你的用户安装你的预处理程序(transpilers，bundlers，等等)。).另一方面，如果您发布原始源代码，用户将需要使用他们自己的预处理程序，您仍然不应该让他们安装您用于模块开发和测试的东西。

# 总的来说，生活更加复杂

正如您所看到的，决定什么去哪里并不像看起来那么简单。npm 文档在两行中忽略了什么，我写了一整篇文章，并且我可能忽略了其他一些情况。

然而，在一天结束的时候，上面所有的讨论可能都不重要。当你和许多其他人一起在一个大项目上工作时，包会不断地从`package.json`文件中添加和删除，并且所有依赖项的适当卫生通常不是团队的首要任务。

在这些情况下，你不能确定你所有的`dependencies`和`devDependencies`在任何时候都被放置在正确的位置。

团队经常因过于谨慎而结束。在一个生产服务器上安装所有的包(包括`devDependencies`)是一个简单的方法，可以消除很多*我的机器上的*类似的问题，节省调试时间。由于 npm 工具的进步，包括命令，安装依赖项不再像以前那样慢了。

此外，除非您确信每个依赖项都在正确的位置，否则最好以相同的方式对待依赖项中漏洞的所有安全警报，不管它们是针对`dependency`还是`devDependency`。
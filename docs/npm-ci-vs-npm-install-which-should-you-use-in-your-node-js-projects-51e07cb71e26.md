# npm ci 与 npm install —您应该在 Node.js 项目中使用哪一个？

> 原文：<https://betterprogramming.pub/npm-ci-vs-npm-install-which-should-you-use-in-your-node-js-projects-51e07cb71e26>

## 到底有什么不同？

![](img/6492923f211abba77e4cc3c8de0bbf8c.png)

npm 是 [Node.js](https://nodejs.org/) 项目的默认包管理器。

使用 npm 可以轻松安装和更新依赖项。在 [npmjs](https://www.npmjs.com/) 中列出的一个依赖项，例如 [Vue.js](https://www.npmjs.com/package/vue) 框架，甚至显示了可以复制并粘贴到终端中的安装命令。

如果您使用 npm 已经有一段时间了，您会使用`npm install`(或者更短的`npm i`)来安装或更新依赖项。

虽然这仍然有效，但在 npm v6 中引入了一个新命令- `npm ci`。

在本文中，我想强调两者之间的区别，以及使用这些命令的不同用例。

# npm 安装(简称:npm i)

`[npm install](https://docs.npmjs.com/cli/install)`或`[npm i](https://docs.npmjs.com/cli/install)`，用于安装依赖项:

*   它将安装所有的依赖项。
*   如果在指定依赖项的版本时使用`***^***`或`***~***`，npm 可能不会安装您指定的确切版本。
*   `npm install`可以在有变化时更新您的`[package-lock.json](https://docs.npmjs.com/files/package-lock.json)`，例如当您安装一个新的依赖项时。

# npm ci

`[npm ci](https://docs.npmjs.com/cli/ci.html)`会做以下事情:

1.  它将删除您的`node_modules`文件夹，以确保一个干净的状态。
2.  它将在您的`package-lock.json` 中查找安装所有依赖项的确切版本。
3.  不像`npm install`，`npm ci`永远不会修改你的`package-lock.json`。然而，它确实希望您的项目中有一个`package-lock.json`文件——如果您没有这个文件，`npm ci`将无法工作，您必须使用`npm install`来代替。

如果你使用`npm ci`，你会得到可靠的构建。当你在一个持续集成工具中运行时，如 [Jenkins](https://jenkins.io/) 或 [GitLab CI](https://about.gitlab.com/product/continuous-integration/) ，这是很有用的。

# npm ci 与 npm 安装—使用哪个？

**如果您使用的是 npm v6 或更高版本:**

*   使用`npm install`安装新的依赖项，或者更新现有的依赖项(例如，从版本 1 升级到版本 2)。
*   当在持续集成中运行时，或者如果您想在不修改`package-lock.json`的情况下安装依赖项，请使用`npm ci`。

**如果你是 NPM v5 或更低版本:**

*   您只能使用`npm install`来安装或更新依赖项。
*   尝试升级到最新的 npm 版本。除了`npm ci`之外，它还具有`npm audit`命令，该命令可以更容易地识别和修复依赖关系的安全漏洞。此外，使用 npm v6 安装依赖项应该会更快。

# 结论

感谢阅读这篇文章。如您所见，这两个命令都有其有效的用例。如果可能的话，我推荐使用`npm ci`，因为它可以可靠地完成工作，而使用`npm install`来安装新的依赖项或更新现有的依赖项。
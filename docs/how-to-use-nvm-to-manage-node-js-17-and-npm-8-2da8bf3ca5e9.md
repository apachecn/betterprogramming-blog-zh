# 如何使用 NVM 管理 Node.js 17 和 NPM 8

> 原文：<https://betterprogramming.pub/how-to-use-nvm-to-manage-node-js-17-and-npm-8-2da8bf3ca5e9>

## NVM 实用指南

![](img/436c2334c883d2867cf334bf9520adb5.png)

照片由 [Aaron Burden](https://unsplash.com/@aaronburden?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

> *本文发布为 nvm* `*0.39.0*` *，*[*node . js 17*](/3-major-features-of-node-js-17-4bee7135df02)*，*[*NPM 8*](/what-might-be-coming-in-npm-9-6985cf2678a6)*。本文中的所有概念仍然有效。但是，您可以查看以下文章中的更新示例:*
> 
> [*如何使用 NVM 管理 Node.js 19 和 NPM 9*](/how-to-use-nvm-to-manage-node-js-19-and-npm-9-bc5656d52d5f)

[Node.js 17](https://nodejs.org/en/blog/release/v17.0.0/) 发布于 2021 年 10 月 19 日。它带有 npm 8 和许多新功能。你准备好尝试了吗？

正如我们在[上一篇文章](/use-nvm-to-manage-node-js-and-npm-versions-2bd0d0875f9f)中所说，最好的尝试方式是使用 [NVM(节点版本管理器)](https://github.com/nvm-sh/nvm)来升级 node.js 和 npm 版本。它可以帮助我们降低风险。

这是一篇类似的文章，所有的概念仍然有效。但是，所有示例都更新了新版本的`nvm`、node.js 和 npm。

# 安装 NVM

`nvm`管理 node.js 和 npm 版本。它被设计为按用户安装，按 shell 调用。`nvm`可以在任何 POSIX 兼容的 shell (sh、dash、ksh、zsh、bash)上工作，特别是在这些平台上:unix、macOS 和 windows WSL。

`nvm`的最新版本是`0.39.0`，可以通过 curl 或者`wget`命令安装:

脚本`install.sh`将 nvm 存储库克隆到`~/.nvm`，并尝试将下面代码片段中的源代码行添加到正确的概要文件(`~/.bash_profile`、`~/.zshrc`、`~/.profile`或`~/.bashrc`)。

在 profile 文件中，如`~/.zshrc`，我们看到上面的行被添加。

# 使用 NVM

安装 nvm 后，我们可以使用下面的命令来安装 node.js 的最新版本:

上面的输出(第 7 行)表明 npm `8.1.2`与 node.js `17.1.0`一起使用。这一点可以得到证实:

我们还可以指定要安装的确切版本。语义版本格式由[sever](https://semver.org/)定义:

如果已经安装了特定版本，则不会重新安装:

我们可以列出所有已安装的版本:

上面输出中的箭头显示 node.js 的当前版本是`12.22.7`(第 3 行)。它还显示了`default` ( `14.17.6`，第 6 行)、`node` ( `17.1.0`，第 9 行)和`stable` ( `17.1.0`，第 10 行)的值。

`nvm use`修改当前版本:

您可能想知道`v10.14.0`如何使用比`v14.17.6`(第 6 行)更高版本的 npm(第 2 行)。这可以通过以下命令实现:

正如我们所知，npm 8 放弃了对 Node.js 10 的支持。版本，`10.14.0`，无法安装 npm `8.0.0`。

以下命令将获取当前节点版本上支持的最新 npm 版本:

`nvm use`为当前外壳设置特定版本。如果启动新的 shell，新设置的 node.js 版本将会丢失。

如何才能让一个特定的节点版本持久化？

默认版本适用于所有 shells。`nvm alias`可以设置默认版本。

为了方便，可以创建一个`.nvmrc`文件，它采用 SemVer 格式，或者`node`，或者`default`。之后，`nvm use`、`nvm install`、`nvm exec`、`nvm run`和`nvm which`将使用`.nvmrc`文件中指定的版本，如果命令行上没有提供版本的话。

我们可以使用以下命令检查当前版本:

```
% nvm current
v17.1.0
```

列出了所有可用的版本，但是要准备好一个很长的列表。

```
% nvm ls-remote
```

更具体地说，提供部分版本可以缩小可用列表的范围。

`nvm which`显示可执行文件的安装路径。我们已经安装了`10.14.0`、`12.22.7`、`14.17.6`、`17.1.0`的 node.js。下面是`nvm which` 的结果:

特定的节点版本可以直接用于运行应用程序:

```
% nvm run 14.17.6 app.js
```

或者，下面的命令运行`node app.js`，路径指向节点`14.17.6`。

```
% nvm exec 14.17.6 node app.js
```

如果您想找到更多 nvm 命令，请运行帮助命令:

```
% nvm --help
```

# 升级 NVM

我们可以用`nvm`来升级 node.js 和 npm。如何才能升级`nvm`本身？

让我们试试。

在升级之前，我们有`nvm` `0.37.2`。

```
% nvm --version
0.37.2
```

我们将其升级到版本`0.39.0`。

正如输出(第 9 行)所述，我们需要关闭并重新打开终端以使用新版本:

```
% nvm --version
0.39.0
```

# 结论

`nvm`方便管理 node.js 和 npm 版本。按照本文中的说明，您可以升级到 node.js 17 和 npm 8，或者任何其他版本。

*   [node . js 18 的 5 大特色](/5-major-features-of-node-js-18-5f4a164cc9fc)
*   [node . js 17 的 3 大特色](/3-major-features-of-node-js-17-4bee7135df02)
*   [快速浏览 Node.js 16 特性](/a-quick-look-at-the-node-js-16-features-d616e8b2f29)
*   Node.js 15 的新功能
*   [探索 npm 9 中的新功能](https://medium.com/p/bea4631a96d6)
*   [快速浏览 npm 8 特性和对 npm 9 的预测](/what-might-be-coming-in-npm-9-6985cf2678a6)
*   [理解和采用 npm 7 的逐步指南](https://medium.com/better-programming/the-step-by-step-guide-to-understanding-and-adopting-npm-7-914504f7090f)

感谢阅读。

```
**Want to Connect?**If you are interested, check out [my directory of web development articles](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af).
```
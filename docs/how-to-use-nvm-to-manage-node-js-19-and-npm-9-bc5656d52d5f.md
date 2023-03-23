# 如何使用 NVM 管理 Node.js 19 和 NPM 9

> 原文：<https://betterprogramming.pub/how-to-use-nvm-to-manage-node-js-19-and-npm-9-bc5656d52d5f>

## NVM 实用指南

![](img/c5f79d49bb4eaf1b2eb2cb7f1a65a528.png)

布莱克·康纳利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

[Node.js 19](https://nodejs.org/en/blog/release/v19.0.0/) 发布于 2022 年 10 月 18 日。它配有 [npm 9](https://medium.com/p/bea4631a96d6) 和许多新功能。你准备好尝试了吗？

正如我们在[上一篇文章](/how-to-use-nvm-to-manage-node-js-17-and-npm-8-2da8bf3ca5e9)中所说， [NVM(节点版本管理器)](https://github.com/nvm-sh/nvm)是升级 node.js 和 npm 版本的最佳方式。它可以帮助我们降低风险。

这是用新版本的`nvm`、node.js 和 npm 重写的。

# 安装 NVM

`nvm`管理 node.js 和 npm 版本。它被设计为每个用户安装，每个 shell 调用。`nvm`可以在任何 POSIX 兼容的 shell (sh、dash、ksh、zsh、bash)上工作，特别是在 Unix、macOS 和 windows WSL 上。

`nvm`的最新版本是`0.39.2`，可以通过 curl 或者`wget`命令安装:

脚本`install.sh`将 nvm 存储库克隆到`~/.nvm`，并尝试将下面代码片段中的源代码行添加到正确的概要文件(`~/.bash_profile`、`~/.zshrc`、`~/.profile`或`~/.bashrc`)。

在概要文件中，如`~/.zshrc`，我们看到上面的行被添加。

# 使用 NVM

安装 nvm 后，我们可以使用下面的命令来安装 node.js 的最新版本:

上面的输出(第 7 行)表明 npm `8.19.2`与 node.js `19.0.0`一起使用。这可以通过以下内容来验证:

我们还可以指定要安装的确切版本。语义版本格式由[sever](https://semver.org/)定义:

如果已经安装了特定版本，则不会重新安装:

npm 升级到版本 9 需要什么？

我们可以列出所有已安装的版本:

上面输出中的箭头显示 node.js 的当前版本是`19.0.0`(第 11 行)。它还显示了`default` ( `14.17.6`第 13 行)、`node` ( `19.0.0`第 16 行)和`stable` ( `19.0.0`第 17 行)的值。

`nvm use`修改当前版本:

你可能想知道`v10.14.0`如何使用比`v14.20.1`(第 6 行)更高版本的 npm(第 2 行)。以下命令可以实现这一点:

正如我们所知，npm 8 放弃了对 node.js 10 的支持。版本`10.14.0`无法安装 npm `8.0.0`。

以下命令将获取当前节点版本上支持的最新 npm 版本:

对于 node.js `19.0.0`，npm 的最新工作版本是`8.19.2`。

`nvm use`为当前外壳设置特定版本。如果启动新的 shell，新设置的 node.js 版本将会丢失。

如何才能让一个特定的节点版本持久化？

默认版本适用于所有 shells。`nvm alias`可以设置默认版本。

为了方便，可以创建一个`.nvmrc`文件，它采用 SemVer 格式，或者`node`，或者`default`。之后，`nvm use`、`nvm install`、`nvm exec`、`nvm run`和`nvm which`将使用`.nvmrc`文件中指定的版本，如果命令行上没有提供版本。

我们可以使用以下命令检查当前版本:

列出了所有可用的版本，但是要准备好一个很长的列表。

```
% nvm ls-remote
```

更具体地说，提供部分版本可以缩小可用列表的范围。

`nvm which`显示可执行文件的安装路径。我们已经安装了`10.14.0``12.22.7``14.17.6``17.1.0``18.0.0``19.0.0`的 node.js。以下是`nvm which` 的结果:

特定的节点版本可以直接用于运行应用程序:

```
% nvm run 14.17.6 app.js
```

或者，下面的命令运行`node app.js`，路径指向节点`14.17.6`。

```
% nvm exec 14.17.6 node app.js
```

如果你想找到更多的`nvm`命令，运行帮助命令:

# 升级 NVM

我们可以用`nvm`升级 node.js 和 npm。我们如何升级`nvm`本身？

让我们试试。

升级之前，我们有`nvm` `0.39.0`。

```
% nvm --version
0.39.0
```

我们将其升级到版本`0.39.2`。

正如输出(第 10 行)所述，我们需要关闭并重新打开终端以使用新版本:

```
% nvm --version
0.39.2
```

# 结论

`nvm`方便管理 node.js 和 npm 版本。我们可以升级到 [node.js 19](/6-major-features-of-node-js-19-b98e28b9670c) 和 npm 9，以及之前的任何版本，如下所列:

*   [node . js 18 的 5 大特色](/5-major-features-of-node-js-18-5f4a164cc9fc)
*   [node . js 17 的 3 大特色](/3-major-features-of-node-js-17-4bee7135df02)
*   [快速浏览 Node.js 16 特性](/a-quick-look-at-the-node-js-16-features-d616e8b2f29)
*   [node . js 15 的新功能](https://medium.com/better-programming/whats-new-in-node-js-15-fc24e87e2590)
*   [探索 npm 9 中的新功能](https://medium.com/p/bea4631a96d6)
*   [快速浏览 npm 8 特性和对 npm 9 的预测](/what-might-be-coming-in-npm-9-6985cf2678a6)
*   [理解和采用 npm 7 的分步指南](https://medium.com/better-programming/the-step-by-step-guide-to-understanding-and-adopting-npm-7-914504f7090f)

感谢阅读。

```
**Want to Connect?**If you are interested, check out [my directory of web development articles](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af).
```
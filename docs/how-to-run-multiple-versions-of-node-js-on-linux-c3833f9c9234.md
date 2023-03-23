# 如何在 Linux 上运行 Node.js 的多个版本

> 原文：<https://betterprogramming.pub/how-to-run-multiple-versions-of-node-js-on-linux-c3833f9c9234>

## nvm 是管理多个 Node.js 版本的最简单的程序之一

![](img/88b4369f100341b5bbcf9f5e92477849.png)

[泰勒·弗兰塔](https://unsplash.com/@tfrants?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果您管理多个 Node.js 项目，它们可能运行不同版本的 Node.js。

Node.js 不支持同时运行不同版本的运行库。这意味着你必须使用第三方软件来完成。

要在 Linux 上做到这一点，你所要做的就是安装一些额外的包，然后你就可以在你的电脑上安装不同版本的 Node.js 了。在您的计算机上获得不同版本的 Node.js 的最简单方法之一是使用`[nvm](https://github.com/nvm-sh/nvm)`。

`nvm`可供任何人免费下载。要下载它，用`curl`下载并安装:

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```

或者，你可以用`wget`做同样的事情:

```
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```

该脚本所做的是将 nvm 库克隆到`~/.nvm`，并将源代码行添加到您的概要文件(`~/.bash_profile`、`~/.zshrc`、`~/.profile`或`~/.bashrc`)。

要加载`nvm`，将以下内容放入您的配置文件(`~/.bash_profile`、`~/.zshrc`、`~/.profile`或`~/.bashrc`):

```
export NVM_DIR="${XDG_CONFIG_HOME/:-$HOME/.}nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

如果在`nvm.sh`后添加`--no-use`，则`--no-use`选项可用，这样它就不会选择 Node.js 的默认版本。

您可以通过运行`nvm -v`来检查`nvm`版本。

或者，您可以手动克隆`nvm`的 Git 存储库并安装它。您可以通过运行以下命令来实现:

```
$ cd ~/
$ git clone https://github.com/nvm-sh/nvm.git .nvm
```

然后，将以下内容添加到您的`~/.bashrc`、`~/.profile`或`~/.zshrc`文件中，让它在登录时运行:

```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

安装至少一个版本的 Node.js 后，就可以使用了。

要安装最新版本，运行`nvm install node`，或者要安装早期版本，运行 `nvm install 6.14.4`。第一个安装的 Node.js 版本将是默认版本。

要列出已安装的 Node.js 版本，请运行:

```
nvm ls-remote
```

当你打开一个新的外壳时，你必须再次运行`nvm`。

你也可以用`nvm run`运行`node`命令。

比如`nvm run node -v`。要选择您想要运行的版本，请运行`nvm exec 4.2 node — v`。

要安装 Node.js 运行时的一个版本的路径，请运行:

```
nvm which 10.16
```

您可以安装最新的长期支持(LTS)版本，而无需指定版本。为此，运行`nvm install --lts`。

要安装 Node.js 的其他 LTS 版本，可以通过它们的代码名来指定，例如`nvm install — lts=carbon`。

其他示例命令包括:

*   `nvm install lts/carbon`:安装 Node.js 的最新 8.x 版本。
*   `nvm uninstall --lts`:删除最新的 LTS 版本。
*   `nvm uninstall --lts=carbon`:删除 Node.js 最新的 8.x 版本。
*   `nvm uninstall 'lts/*'`:卸载 Node.js 的所有 LTS 版本
*   `nvm uninstall lts/carbon`:卸载 Node.js 最新的 8.x 版本。
*   `nvm use --lts`:使用 Node.js 的最新 LTS 版本
*   `nvm use --lts=carbon`:使用 Node.js 最新的 8.x 版本。
*   `nvm use 'lts/*'`:使用最新安装的 Node.js 版本。
*   `nvm use lts/carbon`:使用 Node.js 的 8.x 版本。
*   `nvm exec --lts`:使用 Node.js 的最新 LTS 版本运行 Node.js 脚本
*   `nvm exec --lts=carbon`:使用 Node.js 最新的 8.x 版本运行 Node.js 脚本。
*   `nvm exec 'lts/*'`:使用 Node.js 的最新 LTS 版本运行 Node.js 脚本
*   `nvm exec lts/carbon`:用 Node.js 最新的 8.x 版本运行 Node.js 脚本
*   `nvm run --lts`:使用 Node.js 的最新 LTS 版本运行 Node.js 脚本
*   `nvm run --lts=carbon`:使用 Node.js 最新的 8.x 版本运行 Node.js 脚本。
*   `nvm run 'lts/*'`:使用 Node.js 的最新 LTS 版本运行 Node.js 脚本
*   `nvm run lts/carbon`:使用 Node.js 最新的 8.x 版本运行 Node.js 脚本。
*   `nvm ls-remote --lts`:列出 Node.js 的所有 LTS 版本
*   `nvm ls-remote --lts=carbon`:列出 Node.js 的所有 8.x 版本。
*   `nvm ls-remote 'lts/*'`:列出 Node.js 的所有 LTS 版本
*   `nvm ls-remote lts/carbon`:列出 Node.js 的所有 8.x 版本。
*   `nvm version-remote --lts`:列出 Node.js 的最新 LTS 版本
*   `nvm version-remote --lts=carbon`:列出 Node.js 最新的 8.x 版本。
*   `nvm version-remote 'lts/*'`:列出 Node.js 的最新 LTS 版本
*   `nvm version-remote lts/carbon`:列出 Node.js 最新的 8.x 版本。

要在安装`nvm`时迁移全局包，请运行:

```
nvm install node --reinstall-packages-from=node
```

它将通过在迁移前运行`nvm version node`来识别您正在迁移的节点的版本。然后，它将运行`nvm reinstall-packages`将包重新安装到您的新环境中。

您还可以指定 Node.js 迁移到和迁移自的版本:

```
nvm install 8--reinstall-packages-from=5
nvm install 8--reinstall-packages-from=argon
```

要使用 Node.js 的系统版本，请运行:

```
nvm use system
```

要列出已安装的 Node.js 版本，请运行:

```
nvm ls
```

要查看您可以安装的可用版本，请运行:

```
nvm ls-remote
```

默认情况下，`nvm ls`、`nvm ls-remote`和`nvm alias`以彩色文本输出。您可以通过运行以下命令来禁用彩色文本输出:

```
nvm ls --no-colors
```

运行`nvm deactivate`以停用`nvm`并恢复您原来的`PATH`环境变量。

最后，要为新的 shell 设置默认的 Node.js 版本，运行:

```
nvm alias default node
```

`nvm`是在 Linux 计算机上管理多个 Node.js 版本的最简单的程序之一。它还可以与 Linux 的 Windows 子系统一起工作。
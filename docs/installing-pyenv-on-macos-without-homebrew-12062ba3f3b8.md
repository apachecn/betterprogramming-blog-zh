# 如何在没有自制的 MacOS 上安装 pyenv

> 原文：<https://betterprogramming.pub/installing-pyenv-on-macos-without-homebrew-12062ba3f3b8>

## 当然，自制软件很棒，但是即使没有它，你也可以在 Mac 上拥有一个健壮的 Python 环境

![](img/41f6f7393d13f6e1c03aa58c66cefa9d.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[鲁本·门纳格斯](https://unsplash.com/@rubmenarguez?utm_source=medium&utm_medium=referral)的照片

我最近发现自己正在为 Python 开发设置一个新的 MacBook Pro。互联网上有大量关于在 Mac 上安装 pyenv 的文档，但它们似乎都集中在通过自制软件安装上。嗯，那对我来说不是一个选项，所以下面我记录了我在没有自制软件的情况下安装 [pyenv](https://github.com/pyenv/pyenv) 的步骤。

# TL；速度三角形定位法(dead reckoning)

运行以下命令安装 pyenv。

```
% curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
```

如果 pyenv 仍然不在您的路径中，并且您使用的是最新版本的 MacOS，请在终端中运行以下命令。

```
% PYENV_ROOT="${HOME}/.pyenv"% echo "export PATH=\"${PYENV_ROOT}/bin:\$PATH\"" >> ~/.zshrc
% echo "eval \"\$(pyenv init -)\"" >> ~/.zshrc
% echo "eval \"\$(pyenv virtualenv-init -)\"" >> ~/.zshrc
```

# 我的眼镜

我用这种方法在运行 macOS Catalina 的 2020 MacBook Pro 上安装 pyenv。

# 使用 pyenv-installer

如果您不希望在 pyenv 安装中使用 Homebrew，该项目的开发者还提供了 [pyenv-installer](https://github.com/pyenv/pyenv-installer) 项目。这提供了一个带有简单 bash 命令的安装脚本，可以为您处理安装。

这个项目的文档说运行`curl [https://pyenv.run](https://pyenv.run) | bash`，但是这给了我一个证书错误。

有两种方法可以解决这个问题。阻力最小的方法是在`curl`命令中使用`-k`标志(不检查证书)。更安全的选择是只调用`pyenv.run`脚本重定向到的命令——该命令指向 GitHub 并有一个安全证书。为此，请运行:

```
% curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
```

这个命令下载安装程序文件，并将安装程序文件传输到 bash。换句话说，它在 bash shell 中运行文件。

# 将 pyenv 添加到路径

不幸的是，我们使用的 installer 命令没有成功地将 pyenv 添加到您的路径中。这是由于 bash shell 的命令管道。在 macOS Catalina 中生效，苹果已经开始使用 Zsh(读作 Z Shell)代替 bash 作为默认终端([来源](https://support.apple.com/en-us/HT208050))。因此，当我们将命令传送给 bash 时，它不知道更新 Zsh 的配置。

补救这种情况的一个简单方法就是不要将命令传送到 bash，而是在 Zsh 中运行它。如果该脚本能够正确地检测到它正在运行，它就有内置的逻辑来更新 Zsh 配置文件。但是，如果您像我一样，已经运行了安装程序，下面的步骤将纠正这种情况，并将 pyenv 添加到路径中。

要添加到 Zsh 路径，我们需要运行以下命令将 pyenv 添加到 shell 的配置文件中。

```
% PYENV_ROOT="${HOME}/.pyenv"% echo "export PATH=\"${PYENV_ROOT}/bin:\$PATH\"" >> ~/.zshrc
% echo "eval \"\$(pyenv init -)\"" >> ~/.zshrc
% echo "eval \"\$(pyenv virtualenv-init -)\"" >> ~/.zshrc
```

这将把程序添加到您的 path 变量中。从这里，您可以重新启动您的终端并开始使用 pyenv。

# 开始使用 pyenv

安装了 pyenv 之后，我用它安装了最新版本的 Python。在撰写本文时，这是 Python 3.8.3。为了安装，我运行:

```
% pyenv install -v 3.8.3
```

此时，如果您检查您的 Python 版本，您仍然会看到系统默认的版本。在我的系统上，这看起来像:

```
% python -V
Python 2.7.16
```

我们必须告诉系统我们想要使用哪个版本的 Python。首先，我们可以通过 pyenv 列出可用的 Python 版本，然后我们可以告诉 pyenv 将全局版本设置为首选版本。在这种情况下，这是 3.8.3。

```
% pyenv versions
* system (set by /Users/snolesmuth/.pyenv/version)
3.8.3% pyenv global 3.8.3% python -V
Python 3.8.3
```

一旦我们告诉 Python 使用新版本，我们就可以正常运行 Python 命令，但是 pyenv 会重定向到我们指定的版本。

# 结论

Pyenv 是一个非常强大的工具，允许您根据需要为不同的用户、项目或文件夹使用单独的 Python 安装，而不依赖于 macOS 非常过时的默认版本。
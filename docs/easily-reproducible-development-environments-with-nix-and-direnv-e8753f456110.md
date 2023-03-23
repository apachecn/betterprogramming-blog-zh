# 使用 Nix 和 direnv 的简单可重复开发环境

> 原文：<https://betterprogramming.pub/easily-reproducible-development-environments-with-nix-and-direnv-e8753f456110>

## 自动(重新)加载开发环境，不会污染操作系统，也不需要虚拟机或容器

![](img/ec18ae6a682f32872049feab906ece0b.png)

由[尼基塔·万托林](https://unsplash.com/@vantorin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/development?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

获得一个有效的开发环境通常不是一件容易的事情。我记得在一些项目中，新开发人员(包括我)要花两三天时间才能在他们的开发计算机上把所有东西都处理好。

使用 [Docker](https://www.docker.com/) 和 [Docker Compose](https://docs.docker.com/compose/) 在那个领域非常有用，但是它们只能帮你到这里。它们适用于运行依赖项，而不必担心构建依赖项。

例如，您可以在一个容器中运行您的应用程序运行的数据库，然后使用它。有些人通过将代码源作为一个卷安装在容器中来运行应用程序本身。

它解决了必须安装库和工具的问题，但这并不像听起来那么简单。比如你的文本编辑器呢？你能让林挺在里面无痛工作吗？

除了手动和容易出错之外，在本地安装所有东西的问题是，你最终会在你的计算机上有一大堆程序和库。

如果你在几个项目上工作，当其中一些项目使用同一事物的不同版本或者冲突的系统时，情况会变得更糟。

如果能够在本地安装一个开发环境(没有容器或虚拟机)而不影响操作系统，那不是很好吗？

[Nix](https://nixos.org/nix/about.html) 解决了这个问题。所有派生都安装在 Nix store 中，这个目录包含构建派生的结果。你可以把一个派生看作是如何构建一个程序或者一个库的方法。也就是说，没有什么会污染你的`/usr/local/bin`。

# Nix 的(非常)快速概述

很难对尼克斯进行分类。在网站上，它被描述为“纯功能的包管理器”。然而，它也可以被认为是一种编程语言、一种配置工具和一种构建工具。

简而言之，Nix 表达式是用纯函数式编程语言编写的程序，可以返回一个或一组导数。

派生是描述如何获得所有依赖关系和构建工件(如可执行文件或库)的数据结构。Nix 然后可以构建这些派生并将结果存储在 Nix 存储中，实际上是在`/nix/store`中。

Nix 的一个关键方面是构建是*可复制的*。这意味着，给定一些推导，不管你在什么计算机上或者什么时候尝试构建它，它都会产生相同的结果(除了一些出于实际原因的例外)。

为了避免构建已经构建好的东西，Nix 存储中的路径以结构`hash-name-version`命名，其中`hash`是派生的散列。这样，如果再次需要一个派生，Nix 将会看到它已经在 Nix 存储中，而不会重新构建它。

类似地，Nix 缓存也有助于防止重复工作。如果已经在一台远程机器上构建了一个派生，该机器与您的计算机共享它的 Nix 存储作为缓存，Nix 将简单地下载它，而不是在本地构建派生。

Nix 可以用来构建任何东西:你的项目的依赖项，你的项目本身，还有 Docker 容器和带有 NixOS 的整个 Linux 系统。

# Nixpkgs

Nixpkgs 是一个巨大的 Nix 表达式资源，有很多很多通用程序和库的派生。根据它的自述文件，在撰写本文时，它已经有超过 40，000 个包。

它还有一堆帮助函数和方法来为不同的编程语言和框架建立依赖关系。值得一提的有 [Go](https://golang.org/) ，Node，Java，Python， [Rust](https://www.rust-lang.org/) ，以及 [Haskell](https://www.haskell.org/) 。

# nix-shell

`nix-shell`是一个命令行工具，用于在基于某些派生的环境中启动交互式 shell。

默认情况下，它会在当前目录中寻找一个应该返回一个派生的`shell.nix`或`default.nix`。然后，它将再现派生的构建将在其中运行的环境，即所有依赖项都可用的环境。

有时使用`nix-shell`快速进入一些程序的外壳也很方便。这可以通过`-p`选项实现。例如:

# 没有 direnv 的开发环境

我们需要一个名为`shell.nix`的文件放在项目的根目录下，这样我们就可以调用`nix-shell`并拥有所有可用的依赖项和开发工具。然而，为了保持有序，所有的 Nix 代码都将放在一个目录`nix/`中。

不用太担心没有完全理解 Nix 代码。这里的目标是演示如何使用 Nix 创建一个开发环境，而不是教您 Nix 本身。

如果你想学习更多关于语言本身的知识，这本手册的[部分](https://nixos.org/nix/manual/#chap-writing-nix-expressions)就是你开始学习的全部。

我们还想修复 Nixpkgs 的版本，以便开发环境完全可再现。`nix/sources.nix`将包含这样的外部引用。

这里，`e4d0e33f36491a0e08a3b1a15db13366d7d0785f`是我们想要从中获取派生的提交的散列。

```
{
  nixpkgs = builtins.fetchGit {
    url = "https://github.com/NixOS/nixpkgs.git";
    rev = "e4d0e33f36491a0e08a3b1a15db13366d7d0785f";
  };
}
```

假设我们的项目是一个用 Haskell 编写的小可执行文件，通过调用`cowsay`打印环境变量`PORT`的值和一个随机数。

不太花哨，但它仍然需要一个开发环境，在那里我们可以编译 Haskell 程序，所以我们需要一个编译器，库`random`和`process`，以及程序`cowsay`。

在`nix/shell.nix`中，我们现在可以在函数`pkgs.mkShell`的帮助下为环境创建派生。

可以看到，`mkShell`采用了一个属性集(相当于 JavaScript 中的一个对象或者 Python 中的一个字典)。`buildInputs`是依赖关系列表。例如，你可以在那里添加`pkgs.docker-compose`或`pkgs.nodejs-12_x`。

未使用的键如`PORT`按原样添加到环境变量中。不要太担心`ghcWithPackages`,因为这是 Haskell 特有的将库和编译器结合在一起的方式。

从根目录调用没有任何参数的命令`nix-shell`很方便，所以我们需要一个文件`shell.nix`放在根目录下。简直就是进口`nix/shell.nix`。

```
import nix/shell.nix
```

最后，这里是我们的小程序的代码，用于演示目的。

调用`nix-shell`后，我们可以编译并运行如下程序。

# 使用 direnv 自动(重新)加载

直接使用`nix-shell`的问题在于，每次您更改环境的规范时，您都需要退出并重新加载 shell。此外，它会覆盖提示。

也许更重要的是，如果 shell 当前不是活动的，那么就没有垃圾收集根，即`/nix/var/nix/gcroots`中的一个符号链接。这就是 Nix 如何跟踪 Nix 存储中哪些路径被使用，哪些路径可以被删除。

这意味着运行`nix-collect-garbage`会删除开发环境所需的所有路径，因此需要您在下次运行`nix-shell`时重新构建或重新下载依赖项，因为 Nix 不知道您还需要它。

[Direnv](https://direnv.net/) 可以用来解决这些问题。它寻找一个文件`.envrc`，并在进入目录时加载环境。退出目录会卸载环境。它不是一个 Nix 工具，但是它有一个名为`use_nix`的函数，可以用`nix-shell`加载/卸载 Nix 环境。

让我们给我们的虚拟项目添加一个文件`.envrc`。这里没有列出`direnv/use_nix.sh`的内容，但是你可以在[演示库](https://github.com/thoferon/nix-direnv/blob/master/direnv/use_nix.sh)中查看。`use_nix`的代码来自 [direnv 关于 Nix](https://github.com/direnv/direnv/wiki/Nix) 的 Wiki 页面。

```
#!/usr/bin/env bash. direnv/use_nix.shuse_nix -s shell.nix \
  -w nix/sources.nix \
  -w nix/shell.nix
```

选项`-w` 告诉 direnv 当其中一个文件改变时重新加载环境。第一次你必须调用`direnv allow`来告诉 direnv 可以运行这个特定的`.envrc`。

就是这样！这个项目现在唯一需要的先决条件是 Nix 和 direnv，而 direnv 甚至不是绝对必要的。

这不会污染开发人员的计算机，因为 Nix 没有在通常的目录中安装任何东西，例如`/usr/local/bin`，并且每个人都在运行完全相同的环境，因为 Nixpkgs 的版本是固定在`nix/sources.nix`中的(不要忘记不时地更新它)。

# 支持 VS 代码

VS 代码对 Nix 和 direnv 都有扩展，尽管在这种情况下你只需要对 direnv 的扩展[。在打开的对话框(`ctrl+p`)中输入`ext install direnv`即可快速安装。](https://github.com/rubymaniac/vscode-direnv)

如果一些扩展需要 Nix 环境中的一些工具，可能需要重新加载编辑器，但除此之外，我对它的体验相当顺利。

# 结论

同时使用这两个工具让我管理依赖关系变得简单多了。再也不需要花两天时间进行长时间的准备工作，甚至可以开始一个项目。

Nix 有一个陡峭的学习曲线，但是在不太了解语言和工具内部的情况下，用它可以完成很多事情。

它不需要很长时间就能工作，所以试试吧！

如果你喜欢这样的故事，可以考虑成为[的会员，或者](https://tomferon.medium.com/membership)或者[订阅](https://tomferon.medium.com/subscribe)。

# 进一步阅读

*   包含本文代码的[库](https://github.com/thoferon/nix-direnv)。
*   [对接器](https://www.docker.com/)和[对接器组成](https://docs.docker.com/compose/)。
*   [Nix 手册](https://nixos.org/nix/manual/)及其关于[编写 Nix 表达式的章节](https://nixos.org/nix/manual/#chap-writing-nix-expressions)。
*   [NixOS 手册](https://nixos.org/nixos/manual/)。
*   Nixpkgs 手册。
*   [direnv](https://direnv.net/)
*   direnv 关于 Nix 的 Wiki 页面。
*   [vscode-direnv](https://github.com/rubymaniac/vscode-direnv) ，direnv 的 VS 代码扩展。
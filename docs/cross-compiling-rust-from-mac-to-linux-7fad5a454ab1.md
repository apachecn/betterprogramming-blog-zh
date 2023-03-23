# 从 Mac 到 Linux 的交叉编译 Rust

> 原文：<https://betterprogramming.pub/cross-compiling-rust-from-mac-to-linux-7fad5a454ab1>

## 我遇到的问题之旅

![](img/0ff9ca97feddd180fa8656843f511f37.png)

在尝试将 rust 从 Mac 交叉编译到 Linux 时，我遇到了一些问题，所以我想我应该为自己和其他感兴趣的人写一个简单的指南。这主要是我自己的发现，可能并不适用于每个人。

# 一般步骤

要从一个平台交叉编译到另一个平台，仅仅使用`[rustup](https://rustup.rs/)`为目标安装标准库通常是不够的。你还必须为平台安装一个链接器，并告诉`rustc`在哪里可以找到它。

对于某些平台来说，其他依赖项可能也是必要的，但是在本文中，我将集中讨论从 Mac 到 Linux 的编译。

通常，从一个平台编译到另一个平台需要以下步骤:

*   使用`rustup target add <target>`为目标安装标准库
*   为平台安装一个链接器
*   [更新货物配置](https://doc.rust-lang.org/cargo/reference/config.html#target)以便`rustc`可以找到链接器
*   在编译期间，将`TARGET_CC`环境变量设置为链接器可执行文件
*   将目标标志添加到您的构建命令:`cargo build --release --target <target>`

# 选择构建目标

当选择正确的构建目标时，我需要在`x86_64-unknown-linux-gnu`和`x86_64-unknown-linux-musl`之间做出选择。

`musl`目标使用 musl C 标准库生成一个静态链接的二进制文件，其目标是非常轻量级和简单。静态链接 C 标准库的好处是，它基本上可以在任何现代 Linux 操作系统上运行，没有依赖性。缺点是`musl`通常比`glibc`慢很多，因此对于许多项目来说不是最佳选择。

另一方面，`gnu`目标产生一个动态链接的二进制文件，它依赖于安装在主机系统上的`glibc`。

因为`glibc`通常比`musl`快一点，所以我决定选择`gnu`目标。但我也会解释如何为`musl`目标进行构建。

## 角马

第一步总是使用`rustup`安装标准库。这可以使用`rustup target add x86_64-unknown-linux-gnu`简单地完成。

但是为了实际编译到给定的目标，我们还需要一个链接器。为此，我们将使用社区提供的自制公式:

```
brew install SergioBenitez/osxct/x86_64-unknown-linux-gnu
```

现在连接器已经安装好了，我们需要告诉`rustc`在哪里可以找到它。这可以通过更新[来完成。cargo/config.toml](https://doc.rust-lang.org/cargo/reference/config.html#hierarchical-structure) 并为目标添加链接器:

```
[target.x86_64-unknown-linux-gnu]
linker = "x86_64-unknown-linux-gnu-gcc"
```

现在您可能已经能够使用`cargo build --target x86_64-unknown-linux-gnu`编译到目标，但是对于许多项目来说，还需要将`TARGET_CC`环境变量设置为链接器可执行文件。因此，完整的构建命令可能如下所示:

```
TARGET_CC=x86_64-unknown-linux-gnu cargo build --release --target x86_64-unknown-linux-gnu
```

就是这个！现在可以从 Mac 交叉编译到 Linux 了！
请记住，生成的二进制文件是动态链接的，因此您可能需要在主机系统上安装其他依赖项来使可执行文件工作。

## MUSL

首先，我们需要使用`rustup`安装标准库。这可以通过运行`rustup target add x86_64-unknown-linux-musl`简单地完成。

为了实际编译到给定的目标，我们还需要一个链接器。为此，我们将使用社区提供的自制配方:

```
brew install FiloSottile/musl-cross/musl-cross
```

既然已经安装了链接器，我们需要告诉 rustc 在哪里可以找到它。这可以通过更新[来完成。cargo/config.toml](https://doc.rust-lang.org/cargo/reference/config.html#hierarchical-structure) 并为目标添加链接器:

```
[target.x86_64-unknown-linux-musl]
linker = "x86_64-linux-musl-gcc"
```

现在您可能已经能够使用`cargo build --target x86_64-unknown-linux-musl`编译到目标，但是对于许多项目来说，还需要将`TARGET_CC`环境变量设置为链接器可执行文件。因此，完整的构建命令可能如下所示:

```
TARGET_CC=x86_64-linux-musl-gcc cargo build --release --target x86_64-unknown-linux-musl
```

就是这样！现在可以从 Mac 交叉编译到 Linux。

生成的二进制文件是静态链接的，应该可以在大多数现代 Linux 操作系统上运行。
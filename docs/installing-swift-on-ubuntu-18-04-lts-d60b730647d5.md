# 如何在 LTS Ubuntu 18.04 上安装 Swift

> 原文：<https://betterprogramming.pub/installing-swift-on-ubuntu-18-04-lts-d60b730647d5>

## Linux 上的 Swift

![](img/fab6c288433168d5941e2c3a32411092.png)

[Guillaume Jaillet](https://unsplash.com/@i_am_g?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/fast?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在本指南中，我将编写在 Ubuntu 18.04 LTS 上安装 Swift 所需的命令和步骤。我的服务器将由 Linode 托管，但它应该可以在任何运行 Ubuntu 18.04 LTS 的电脑上运行。因为我是在 Linode 上做这个的，所以所有的说明都是关于如何使用命令行安装 Swift 的。

让我们从访问你的 Ubuntu 18.04 LTS 实例开始。对我来说，这意味着使用 SSH 来访问我的服务器。

**注意**:安装时，我将把`sudo`放在我的命令之前，因为我们需要 root 权限来安装到一些目录中。如果有 root 权限，应该不需要`sudo`。

# 1.安装`clang`和`libicu-dev`

需要安装两个包，因为它们是依赖关系。在实际安装之前，我想解释一下它们是什么。

## `clang`

`clang`为 C 编程语言中的编程语言提供了工具基础设施。它就像一个编译器。

## `libicu-dev`

`libicu-dev`提供全功能的 Unicode 和区域设置支持。这允许在我们的代码中使用 Unicode。

## 如何安装

```
sudo apt-get install clang libicu-dev
```

`apt-get`是一个命令行工具，处理 APT 库中的包和其他工具。

# 2.下载 Swift 文件

苹果在[Swift.org/downloads](https://swift.org/download/)托管 Swift 文件供下载。您可以去那里获取下载最新版 Swift 或您需要的版本的 URL。

注:在撰写本指南时，最新的版本是 5.1.3，这是我将在命令中使用的版本。

导航到您想要下载要安装的 Swift 版本的二进制文件的位置。我把我的放在`src`文件夹中，所以下面的命令会把我带到那里。

```
cd src
```

现在，让我们下载二进制文件。

```
wget [swift.org/builds/sw...](https://swift.org/builds/swift-5.1.3-release/ubuntu1804/swift-5.1.3-RELEASE/swift-5.1.3-RELEASE-ubuntu18.04.tar.gz)
```

让该命令结束，然后继续执行步骤 3。

# 3.提取文件

```
tar -xvzf swift-5.1.3-RELEASE*
```

`swift-5.1.3-RELEASE`后面的`*`充当通配符，这样`tar -xvzf`就可以找到以`swift-5.1.3-RELEASE`开头的文件。

这将提取文件并创建一个名为`swift-X.X.X-RELEASE-ubuntu18.04`的 Swift 二进制文件文件夹。`X.X.X`将是您安装的版本号。

# 4.将此添加到路径中

我们需要将二进制文件的绝对路径——二进制文件被提取的位置——添加到`PATH`变量。这样，我们实际上可以在命令行中使用`swift`命令。

导航到步骤 2 和 3 中使用的位置，使用命令`pwd`查看二进制文件的完整路径。现在，要将该路径添加到`PATH`，请编写:

```
export PATH=path_shown_using_pwd
```

我的命令看起来像这样:

```
export PATH=/home/mw/src/swift-5.1.3-RELEASE-ubuntu18.04
```

# 5.验证安装

Swift 现已安装。让我们通过使用以下内容来验证它的工作情况:

```
swift -version
```

终端应该显示你正在使用的版本。我的展示了`Swift version 5.1.3`和更多关于版本的东西。就是这样！
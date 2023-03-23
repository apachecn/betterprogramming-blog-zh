# 如何在 Xcode 版本之间轻松切换

> 原文：<https://betterprogramming.pub/easily-swith-between-xcode-versions-b36f9f685979>

## 轻松地在 Xcode 版本之间切换，彻底测试您的代码

![](img/98fe59179c76bef2db6daa454c703f79.png)

由[法比安·伊尔萨拉](https://unsplash.com/@firsara?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片。

iOS 14 来了。如果您需要安装新版本的 Xcode 来测试您的应用程序，并且出于某种原因您还需要保留旧版本的 Xcode，那么这篇文章就是为您准备的。

每次新版本的 iOS 出来，我们都必须更新我们的编译器，以便能够在新的 iPhone 上测试我们应用程序的新操作系统。

我用过几个应用程序，前景总是一样的:新版本的操作系统出来了，我们必须更新我们的 Xcode，但我们的 CI 是用以前的版本配置的，或者我们有一个由多个开发人员维护的库，他们使用相同版本的 Xcode，以便在不同版本之间保持一致。

不管是哪种情况，解决方案总是[手动下载](https://developer.apple.com/download/more/)我们需要的所有版本，然后向上帝祈祷不要破坏命令行构建兼容性。

有更简单的方法吗？

# 最简单快捷的方法

有一个叫做 [xcode-install](https://github.com/xcpretty/xcode-install) 的工具，可以让你在电脑上轻松安装和管理多个版本的 xcode。你肯定听说过 [NVM](https://github.com/nvm-sh/nvm) (节点版本管理器)或者 [RVM](https://rvm.io/) (Ruby 版本管理器)。这是类似的东西，但是对于 Xcode。

## 装置

只需在您的终端中运行以下命令:

```
$ gem install xcode-install
```

## 使用

现在是时候玩一玩，开始管理我们的 Xcode 版本了。

列出所有可用和已安装的 Xcode 版本:

```
xcversion list
```

安装特定的 Xcode 版本:

```
xcversion install 12
```

选择要使用的特定 Xcode 版本:

```
xcversion select 12
```

如果您想在`/Applications/Xcode`更改符号链接，运行:

```
xcversion select 12 --symlink
```

要查看当前选择的版本:

```
$ xcversion selected
```

xcode-install 需要环境变量和您的凭证来访问 Apple 开发者中心。它们使用[快速通道](http://fastlane.tools/)的[凭证 _ 管理器](https://github.com/fastlane/fastlane/tree/master/credentials_manager#using-environment-variables)存储:

```
XCODE_INSTALL_USER
XCODE_INSTALL_PASSWORD
```

就是这样，现在我们可以用一个命令安装或删除任何版本的 xcode

# 但是还有别的东西…

xcode-install 也可以通过调用以下命令来安装 xcode 的命令行工具:

```
xcversion install-cli-tools
```

## 有人说“模拟器”吗？

xcode-install 还可以使用`simulators`命令管理您的本地模拟器:

```
xcversion simulators
```

希望这个工具能对你有用。我称之为我的小 XVM (Xcode 版本管理器)。
# 设置 React Native 的完整指南

> 原文：<https://betterprogramming.pub/the-complete-guide-to-setting-up-react-native-afae593e6cfc>

## 开始在 Mac 上开发 React 原生应用程序之前需要做的一切

![](img/733bd48251e4ffcce772a124b8659c1a.png)

照片由[莱纳斯·米米耶茨](https://unsplash.com/@linusmimietz?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄。

如果你在这里，你会有一个有趣的旅程。我们将探索所有必须遵循的步骤，以便为 [React Native](https://reactnative.dev/) 开发正确设置您的 Mac。我还将向您展示我个人遇到的问题以及如何解决这些问题。

如果你在 Windows 上，没问题！步骤基本相同——除了下载 Xcode 需要三个小时。

# 为什么您应该使用 Mac

在 Windows 上开发 RN 应用的缺点是你不能在 iOS 上测试你的应用。如果你只针对 Android，那就没什么好大惊小怪的。但是在这种情况下，您应该使用本地方法。看看科特林。你会喜欢的！

然而，如果你计划同时针对 Android 和 iOS，你将无法从 Windows 在 iOS 上运行你的应用程序。这是不可取的。有时，一个库可能会在一个目标平台上破坏你的应用程序，或者你可能会遇到不可预见的不一致。

也有一些奇怪的情况，一些东西在一个平台上工作，而在另一个平台上不工作。我想到的一个简单的例子是从`react-native-gesture-handler`导入`TouchableWithoutFeedback`。在安卓上就不行了！你必须从`react-native`进口它，它才能工作。你明白了:为了保证质量和避免崩溃，你必须在两个平台上运行你的应用程序。

# 概述

本指南没有按照您必须完成的设置类型进行分组。相反，我会以方便的顺序向您展示您需要做的事情，以便该过程尽可能顺利。

你需要三个主要的发展项目:

*   [VS Code](https://code.visualstudio.com/) ，您的主 IDE —在这里，您将编写干净的类型脚本代码。
*   [Xcode](https://developer.apple.com/xcode/) ，苹果的 IDE——一些 iOS 特有的部分需要它。
*   [Android Studio](https://developer.android.com/studio) —用于 Android 开发的官方 IDE。React 原生开发周期中的一些特定于 Android 的部分也需要它。

除了 ide，我们还将安装一些开发 RN 应用程序所需的其他工具。

# 先决条件

## 设置 Git

在开始使用 ide 之前，让我们确保已经设置了 Git CLI。您可以通过打开您的终端(CMD+空格键，并搜索终端)并键入一个基本命令(如`git status`)来简化这个过程。如果没有设置 Git CLI，那么您将被引导完成整个过程。还会提示您安装在 macOS 上开发所需的工具。

如果你不习惯版本控制系统，那么你可能想看看 [Git 的文档](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F)，以便更好地理解 Git 是什么。

## 安装 Brew

Brew 是一个在 macOS 和 Linux 上使用的流行的包管理器。我们将在后面的步骤中需要它，所以让我们现在安装它。打开终端并运行以下命令:

```
/bin/bash -c "$(curl -fsSL [https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh](https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh))"
```

# 安装 ide

## 安装 VS 代码

转到 [VS 代码下载页面](https://code.visualstudio.com/download)，选择适合您平台的版本，然后完成安装过程。在下一篇文章中，我还会给出一个很好的清单，列出 RN 开发所必需的扩展(以及其他很酷的扩展)。

现在我们需要使用 React Native 为您的机器准备 Android 和 iOS 开发。

## 安装 XCode

Xcode 安装可能需要三个小时，所以我们应该提前安装。在 Mac 上打开 App Store(CMD+空格键，搜索“App Store”)。搜索 Xcode，获取它，开始下载。这需要一段时间。与此同时，我们会完成其他一切。

## 安装 Android Studio

进入 [AS 下载页面](https://developer.android.com/studio/index.html)下载最新稳定版本。完成下载后，安装过程相当顺利。默认设置是完美的，在这个过程中，您还将安装 Android SDK——它捆绑在 IDE 中。

# 设置 React 本地环境

React 原生应用可以在 iOS 和 Android 上运行。正如我之前提到的，我们必须确保您可以在两个平台上运行。

下面介绍的步骤可以在[官方文档](https://reactnative.dev/docs/environment-setup)中找到，但是我想简化它们，直奔主题。

## 设置 Android

为了能够在 Android 上运行你的注册护士应用程序，你需要完成一些设置。你已经安装了 Android Studio，但我们还需要一些工具。

1.  获得 JDK (Java 开发工具包):您将获得使用 Brew 的 JDK。打开终端并运行以下命令:

```
brew install --cask adoptopenjdk/openjdk/adoptopenjdk8
```

不要从其他来源手动安装 JDK。你可能会犯下严重的错误。

2.安装节点和 Watchman: [Node.js](https://nodejs.org/en/) 是一个 JavaScript 运行时，对于 React 本地开发是必需的(因为您使用的是 js)。 [Watchman](https://facebook.github.io/watchman/) 是脸书开发的一个工具，它可以观察文件系统的变化并提高性能。在终端中，运行以下命令:

```
brew install node
brew install watchman
```

你也可以从[他们的网站](https://nodejs.org/en/download/)单独安装 Node。但是，建议使用 Brew 安装它。

3.配置环境变量:打开`$HOME/.bash_profile`文件并添加以下几行:

```
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/tools/bin
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

您可以进入您的终端，键入`open $HOME/.bash_profile`，并在那里添加行。

4.接受 Android Studio 许可:这一步可能不是每个人都需要的，但我每次都必须这样做。文档中没有提到，但是除非您接受许可，否则 Android 版本可能无法工作。

运行以下命令应该可以解决您的问题:

```
yes | sudo ~/Library/Android/sdk/tools/bin/sdkmanager --licenses
```

## 设置 iOS

让我们看看您还应该做些什么来完成 iOS 设置。完成 Xcode 安装后，请按照以下步骤操作:

1.  安装 Xcode 命令行工具，方法是转到“偏好设置”>“位置”,并在命令行工具下拉菜单(或微调器，对于 Android devs)中选择最新版本。
2.  安装[cocoa pods](https://cocoapods.org/)—Swift/Objective C 项目的包管理器。RN 项目的 iOS 端需要它来进行(你猜对了)包管理。

进入命令行并运行以下命令:

```
brew install cocoapods
```

Cocoapods 文档为此提供了另一个命令，但是 Brew 更可靠。这是另一个命令:

```
sudo gem install cocoapods
```

# 收尾工作

你应该已经准备好了，能够开发出色的跨平台移动应用。您只需再遵循两个步骤，就可以开始编码了。

## 安装纱线

我们将使用 [Yarn](https://yarnpkg.com/) 在 React 本地项目中进行包管理。在互联网上，你会看到人们使用纱线或 NPM。我们更喜欢纱线。我觉得有点好笑的是，我们将使用 NPM 来安装纱线:

```
npm install --global yarn
```

我第一次在全球范围内使用 NPM 时，遇到了一个“缺少权限”的错误。别担心。这个问题有一个简单的解决方法。

这些是您将对 Yarn 使用的最常用的命令:

*   `yarn add <package-name>`用于将包安装到项目中。
*   `yarn install`用于获取您的同事添加到项目中的依赖项。
*   `yarn android`用于在 Android 设备(或模拟器，如果没有设备连接)上启动应用程序。
*   `yarn ios`用于在 iOS 模拟器上运行您的应用。如果您想要在 iPhone 上运行应用程序，您必须从 Xcode 运行应用程序。

重要提示:在 iOS 上运行应用程序之前，您还必须运行`pod install`，以便 Cocoapods 可以获得 iOS 项目上的项目依赖关系。

## 在 Mac 上设置 SSH 密钥

您将使用 Git 进行版本控制。在我看来，使用 VCS 的更好方法是使用 SSH 密钥，而不是传统的登录凭证。这样更方便。如果你对这个话题很好奇，看看这篇文章。

在本文中，您将找到设置 SSH 密钥的必要步骤。在 Mac 上创建密钥后，您还必须将其添加到 Bitbucket(或您用作 VCS 的任何东西)。当您克隆一个 repo 时，您将选择 SSH 选项。

## 安装 React 本机调试器(可选)

你可以在谷歌 Chrome 等浏览器中调试你的代码，但有些人更喜欢使用[独立应用](https://github.com/jhen0409/react-native-debugger)。

# 结论

现在，您应该已经为开发 React Native 做好了一切准备。如果有什么不对劲，或者你遇到了问题，不要犹豫，留下你的评论。我会尽快帮你的。

感谢阅读。希望这个指南对你有帮助！接下来你想读什么？
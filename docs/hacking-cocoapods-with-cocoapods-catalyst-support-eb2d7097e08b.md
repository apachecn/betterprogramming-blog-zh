# 用 cocoapods-catalyst-support 攻击 CocoaPods

> 原文：<https://betterprogramming.pub/hacking-cocoapods-with-cocoapods-catalyst-support-eb2d7097e08b>

## 快速配置您的 pod，使其仅在 Mac Catalyst 或 iOS 上运行，并且仍然使用一个主要目标

![](img/e5b5523a8b10e17faafdb20ab77b5a2b.png)

照片来自 [CSO](https://www.csoonline.com/article/3040408/hacking-back-will-only-get-you-in-more-trouble.html) 。

[Mac Catalyst](https://developer.apple.com/documentation/uikit/mac_catalyst) 在 WWDC19 上发布，这是一场激动人心的盛会，苹果也在会上展示了 SwiftUI 和 Combine 的早期版本。一个事件的大量公告。

Mac Catalyst 的发布为 iOS 开发者提供了一种轻松进入 macOS 世界并将我们的应用移植到 Mac 设备家族的方式。这看起来很容易，也很简单。然而，没有人说大量的核心 Cocoa 框架不支持这种架构。因此，许多知名且广泛使用的第三方库也没有依赖于它们(如`Firebase/Analytics`)。

虽然有几种方法可以解决这一问题，但大多数方法都依赖于创建新的目标或使用 Swift Package Manager。这种方法有一些缺点:

*   新目标:目标很难维护，你需要处理新的签名问题，新的测试模式，你很可能会复制构建设置和项目配置，等等。重复从来都不是一个好兆头，而且会在未来困扰你。
*   迁移到 Swift 包管理器:SPM 允许您选择依赖项可用于哪个 SDK。然而，迁移是有风险的，并且伴随着许多未知因素。这个依赖关系管理器还很年轻，缺少其他依赖关系管理器(如 [CocoaPods](https://cocoapods.org/) )提供的大量功能，原因很简单，它们已经运行了很多年。另外，您可能使用的一些依赖项很可能不支持 SPM。

那你能做什么？我在这里提出另一个解决方案，可能会非常方便，易于使用，并会顺利地让你摆脱困境。你仍然可以使用 CocoaPods *，*避免考虑痛苦的迁移，并使用你的应用的目标来支持这两个 SDK。

# 椰子-催化剂-载体

`cocoapods-catalyst-support`是我不久前创建的开源 CocoaPods 插件:

[](https://github.com/fermoya/cocoapods-catalyst-support/) [## fermoya/椰子-催化剂-载体

### gem 安装 cocoapods-catalyst-support 注意:如果您使用 fastlane 的 cocoapods 操作，请确保包含 gem…

github.com](https://github.com/fermoya/cocoapods-catalyst-support/) 

这个红宝石宝石附带了一些`cocoapods`宝石的扩展。通过一个类似于您已经在 Podfile 中使用的简单语法，您将能够将某些依赖项或 pods 定义为“仅 catalyst”或“仅 iOS”，以便它们仅可用于该 SDK。你可以快速轻松地一劳永逸地解决这个问题。

## 初始化

开始使用`cocoapods-catalyst-support`极其简单。作为公共 gem，您可以从您的终端将其安装到您的机器中:

```
gem install cocoapods-catalyst-support
```

一旦安装完毕，你只需要在你的项目文件夹中运行`pod catalyst init`来初始化你的 Catalyst 配置。该命令会自动将下列行添加到您的 Podfile *中:*

这是什么意思？

1.  “导入”宝石以在您的 Podfile 中使用它。
2.  限制您的 pod 只能在一个 SDK 中使用。例如，`macos 'AppCenter/Analytics'`将使`AppCenter/Analytics`只适用于你的 Mac Catalyst 应用。如果你正在寻找相反的东西，用`ios`代替`macos`(例如，一个只适用于 iOS 的 pod)。请注意，不在此块内的任何 pod 都将被视为多平台(iOS 和 Mac Catalyst)。
3.  配置您的应用程序依赖项。

## 使用

一旦您的依赖项按照您的意愿进行了配置，您就可以通过快速运行`pod catalyst validate`来检查您的配置。这将让您知道您的配置中是否有任何意外。

这个命令将捕获的一个常见错误是安装一个与您正在配置的依赖项不同的依赖项。例如，安装`pod 'Firebase/Analytics'`然后配置`iOS 'FirebaseAnalytics`。注意丢失的`/`。这可能看起来无关紧要，但是它显著地改变了 CocoaPods 如何配置您的 pod 的项目设置。

如果验证成功，您只需运行`pod catalyst run`或简单的`pod catalyst`。这是一个键盘命令，它将在您的设置中执行魔法，并按照您的意愿配置您的项目。

## 之后会发生什么？

到目前为止，您的依赖项已经过配置，它们是:

*   仅为选定的 SDK (Mac Catalyst 或 iOS)内置编译时。
*   仅针对选定的 SDK (Mac Catalyst 或 iOS)在运行时链接。
*   从你的最终档案中剥离，连同他们的资源。这适用于未选择的 SDK。

现在你只需要分叉你的代码来为 Mac Catalyst 和 iOS 使用不同的解决方案。例如，在处理分析时，您可能会对 iOS 使用`Firebase/Analytics`，对 Mac Catalyst 使用`AppCenter/Analytics`。在这种情况下，您需要执行类似于以下代码片段的操作:

在这种情况下，我可以给你的一个建议是将这个功能提取到一个组件中，并定义两个不同的实现:

您可以在下面找到更多信息:

[](/why-dont-my-pods-compile-with-mac-catalyst-and-how-can-i-solve-it-ffc3fbec824e) [## 为什么我的 Pods 不能用 Mac Catalyst 编译，如何解决？

### 如何在您的 iOS 中使用 Mac Catalyst 解决“for architecture x86 _ 64c lang:error:linker command failed with exit”

better 编程. pub](/why-dont-my-pods-compile-with-mac-catalyst-and-how-can-i-solve-it-ffc3fbec824e) 

# 最后的想法

`cocoapods-catalyst-support`是一种变通方法，在您的项目中继续使用 CocoaPods，同时能够在您的主要目标中支持 Mac Catalyst。它易于安装和配置。一旦设置好，`cocoapods-catalyst-support`将在您每次运行`pod install`或`pod update`时自动调用。

如前所述，这是一种变通办法。Swift Package Manager 是未来，没有人会告诉你不是这样。尽管如此，在有些情况下，您不会对 SPM 现在所提供的感到满意。您不希望冒迁移的风险，因为未知或某些依赖项不支持此依赖项管理器。在这些场景中，CocoaPods 很可能是您的首选，在这种情况下，如果您决定支持 Mac Catalyst，这个插件可能会派上用场。
# 为什么 CocoaPods 还是 2022 年的东西？

> 原文：<https://betterprogramming.pub/why-cocoapods-is-still-a-thing-in-2022-b1478f03852>

## 使用 CocoaPods 而不是 Swift Package Manager 的几个原因

![](img/653890f047903b35c76ed557cc6ad547.png)

由[安东尼·特里维特](https://unsplash.com/@antonytrivet?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Swift 包管理器(或 SPM/SwiftPM)出现在 Swift 3.0 中，作为流行的包管理器 CocoaPods 和 Carthage 的替代。它是苹果官方的软件包管理器，这给了它巨大的优势。当它刚出现的时候，我听很多专家说，它会杀死所有其他的包管理器。到底发生了吗？让我们找出它。

# 为什么您应该转向 SPM

与它的替代品相比，SPM 有几个优点。主要是……是苹果。苹果是一个品牌。它保证质量和支持。人们通常信任他们的产品。这意味着在一段时间内，越来越多的产品将支持 SPM，有时仅支持 SPM。

SPM 是 Apple 开发生态系统的一部分。它是 Xcode 的一部分。不像 CocoaPods 和 Carthage，你不需要安装外部工具来使用它们。您不需要在终端中运行任何东西，您可以直接在 Xcode 中添加包。

SPM 可以在 Linux 中使用。没错，Swift 是跨平台语言。尽管大多数开发人员将 Swift 用于 macOS、iOS 和其他苹果操作系统——这意味着在 macOS 环境中编写代码——但仍有少数开发人员将 Swift 用于其他目的(例如后端)。

SPM 不需要工作空间。CocoaPods 创建了一个工作区，这可能会令人困惑。您可以打开项目而不是工作区。默认情况下，该工作空间与项目同名，它位于 Xcode 欢迎窗口或 XCOrganizer 等帮助应用程序中。

切换到 SPM 的最后一个原因是它不需要任何配置。理论上，它可以像任何苹果应用产品一样开箱即用。

# 为什么你应该和椰子呆在一起

如果你在 SPM 发布之前为苹果平台开发应用，你可能会使用 CocoaPods 或 Carthage。我将比较 SPM 和 CocoaPods，将 Carthage 放在后面，因为它更受欢迎，而且我在这方面更有经验。

CocoaPods 与 Objective-C 一起工作。如果您的应用程序需要在低内存设备上工作，或者需要更高的性能，您可以考虑使用 Objective-c。cocoa pods 比 Swift 更早，甚至在 Swift 发布之前就可以工作。

CocoaPods 更灵活。它有许多内置参数，如`use_frameworks!`、`inhibit_all_warnings!`等。但是，即使这些参数不能满足您的需要，您也可以手动配置不同的目标:

```
post_install do |installer|
  installer.pods_project.targets.each do |target|
    ...
  end
end
```

通过这种方式，您可以更改构建标志。例如，您可以为旧应用程序设置旧的 Swift 版本。

CocoaPods 可以使用任何自定义源，包括本地文件夹。就像这样，你可以修改一个库，并在你的应用中使用修改后的版本:

```
pod 'MyLib', :path => '~/MyLib'
pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git'
```

除了选择路径或存储库 URL 之外，您还可以选择分支、标记甚至特定的提交:

```
pod 'Alamofire', :git =>  'https://github.com/Alamofire/Alamofire.git', :branch => 'dev'
pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git', :tag => '3.1.1'
pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git', :commit => '0f506b1c45'
```

# 在这种情况下，椰子是唯一的选择

在一些情况下，您根本无法使用 SPM，尽管官方的 SPM 应该可以工作。

第一种情况是不稳定的互联网连接。如果你在家里有不稳定的互联网连接，或者如果你喜欢在咖啡馆、酒店甚至海滩工作，SPM 可能不会拉你的依赖。当 SPM 无法下载依赖项时，它会在 Xcode 中显示一个错误，甚至不允许重试。若要再试一次，您需要重新启动 Xcode，或者移除并添加依赖关系。

我遇到的第二个无法使用 SPM 的情况是一个带有定制模式和框架的项目。项目通常有`debug`和`release`模式。如果您停留在这个范式中，您可以使用 SPM。但是如果你有不同的构建模式，你的项目就不会构建。例如，对于 QA 或营销团队，当您同时使用框架来分离代码的某些部分时，这些框架使用来自 SPM 的框架。很可能在某个时候，苹果会修复这个问题，但在写这篇文章的那一刻，他们似乎并不着急。

# 摘要

CocoaPods 和 Swift 包管理器都是很好的成熟包管理器，用于数千种产品。你可以使用它们中的任何一个，因为对于大多数项目来说，它们都可以完成预期的任务。

如果你不能决定什么对你和你的项目更好，为什么不两者都用呢？他们甚至在同一个项目上工作得很好。

编码快乐，下次见！
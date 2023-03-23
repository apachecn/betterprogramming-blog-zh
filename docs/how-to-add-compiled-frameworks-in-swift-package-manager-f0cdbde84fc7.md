# 如何在 Swift 包管理器中添加编译的框架

> 原文：<https://betterprogramming.pub/how-to-add-compiled-frameworks-in-swift-package-manager-f0cdbde84fc7>

## Swift 5.3 为 SPM 带来了二进制依赖或编译框架

![](img/6e6f8b89dc051d400bbd81f30a7ea00f.png)

在 [Unsplash](https://unsplash.com/s/photos/box?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Erda Estremera](https://unsplash.com/@erdaest?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

Swift 终于推出了期待已久的功能。Swift 5.3 将使您能够在 [Swift 包管理器](https://swift.org/package-manager/)中添加二进制依赖项。为什么这听起来很棒？因为在 Swift 5.3 之前，SPM (Swift Package Manager)仅将源代码库作为依赖项启用，这意味着许多企业软件(通常以闭源形式出现)或闭源库不能作为依赖项添加到 SPM 中，如 Firebase 或 Google Analytics。

[SE-0272](https://github.com/apple/swift-evolution/blob/master/proposals/0272-swiftpm-binary-dependencies.md)by[Braden scot hern](https://github.com/bscothern)、 [Daniel Dunbar](https://github.com/ddunbar) 和 [Franz Busch](https://github.com/FranzBusch) 为 Swift 5.3 带来了二进制依赖能力，这意味着您可以将编译后的框架添加到您的 SPM 中。但是请记住，目前只支持`xcframework`格式。(撰写本文时，其他类型的框架仍在讨论中。更多细节见[此处](https://github.com/apple/swift-evolution/blob/master/proposals/0272-swiftpm-binary-dependencies.md#support-for-non-apple-platforms)。

# xcframework 是什么？

`xcframework`是苹果在 2019 年 WWDC 会议上推出的一种格式，在 Swift 的“[二进制框架”中讨论了这种格式](https://developer.apple.com/videos/play/wwdc2019/416/)

使用`xcframework`有几个好处，像多架构多平台(包括模拟器类型)，上传到 AppStore 之前不用切片 x86_64。

你应该知道怎么用`xcframework`因为 SPM 只支持这个。除非你已经编译过了，否则跳过这一节。我建议你观看上面的 WWDC 2019 会议。

注意:因为 Swift 5.3 只允许`xcframework`，所以该功能只在苹果 OS (iOS、macOS、tvOS 等)上有效。我希望未来的 Swift 版本能在 Linux 和 Windows 上增加这一功能。

# 如何使用

首先在 SPM 目标中添加一个新的`.binaryTarget`。

```
let package = Package(
   name: "AwesomeLibrary",
   ...
   targets: [
      .target(
         name: "AwesomeLibrary",
         dependencies: ["Logging"]
      ),
      .binaryTarget(name: "Logging", path: "artifacts/Logging.xcframework"
      ),
      ...
   ]
```

SPM 支持二进制依赖的`zip`和原始`xcframework`文件。如果你使用了`zip`，确保`xcframework`位于档案的根目录。

有两种方法可以添加二进制目标。

# 带 URL

您可以从 URL 添加二进制目标，如 GitHub Release、GitHub Packages、GitLab、Bitbucket、Artifactory、Nexus 等。

```
.binaryTarget(
   name: "Logging",
   url: "[https://github.com/wendyliga/compiled-framework-spm/releases/download/0.0.1/Logging.xcframework.zip](https://github.com/wendyliga/compiled-framework-spm/releases/download/0.0.1/Logging.xcframework.zip)",
   checksum: "c246c715ac7f6fae9ef0a89e758a8514644071a164985b1e95d344a684d84621"
)
```

注意:使用 Swift 5.3，可以通过执行`swift package compute-checksum <path-to-zip>`来生成校验和。您需要确保首先下载二进制文件，以计算校验和。

# 原始文件

另一方面，您可以通过将二进制文件放在源代码中来添加它:

`.binaryTarget(name: "Crypto", path: "artifacts/Crypto.xcframework")`

注意:到`xcframework`的路径从项目的根开始(与`Package.swift)`相同)。

# 考虑

Swift 不验证工件的格式(二进制依赖)，这意味着您应该确保提供正确有效的工件。

从安全的角度来看，比较基于源代码的依赖和二进制依赖之间的信任级别，自然地，基于源代码的依赖会更高，因为您可以检查代码。然而，基于源代码的依赖也有其自身的安全问题。

二进制依赖也有它自己的问题，我们应该减轻，例如，托管在网络上的二进制依赖。总会有`tempering`的风险。Swift 通过将您设置的二进制校验和与网络中的校验和进行比较来减少这种情况。哈希也存储在包中，以避免供应商在没有人注意到的情况下在以后的版本中更改二进制文件。

# 示例项目

本文中的所有示例都可以在这个示例项目中找到:[Wendy liga/compiled-framework-SPM](https://github.com/wendyliga/compiled-framework-spm)

# 结束了

Swift 5.3 支持编译框架作为 SPM 中的依赖项。这为更广泛的用例开辟了道路。

Swift 5.3 还提供了向 SPM 添加资源文件或非编译文件的功能，例如 JSON、mp3 或 HTML。我在之前的文章“如何在 Swift Package Manager 中添加资源[中讨论过这个问题](https://medium.com/better-programming/how-to-add-resources-in-swift-package-manager-c437d44ec593)

我要说的就是感谢您花时间阅读这篇文章。我希望您可以在您的 SPM 项目中使用这个特性。
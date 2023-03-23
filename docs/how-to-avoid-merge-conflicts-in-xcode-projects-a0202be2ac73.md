# 如何避免 Xcode 项目中的合并冲突

> 原文：<https://betterprogramming.pub/how-to-avoid-merge-conflicts-in-xcode-projects-a0202be2ac73>

## Xcode 对于 git 项目格式来说可能不太舒服。xcodegen 工具将其替换为简单的 YAML 文件。

![](img/6cb9e9f73bec2fcadcd9ccce6c89e0cc.png)

由[车头时距](https://unsplash.com/@headwayio?utm_source=medium&utm_medium=referral)在[挡泥板](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍摄

Xcode 是一个非常强大的 IDE，历史悠久，功能丰富。但即使在多年来开发者的抱怨和 App Store 的低评级之后，苹果仍未解决一些核心问题。其中之一是项目文件结构。我已经写了一篇关于 Xcode 问题的文章，你可以在这里查看。

即使您使用 AppCode 或从另一个第三方工具导出您的项目，git(或其他存储库)也必须处理一般的`xcodeproj`结构，特别是`pbxproj`文件。

Xcode 没有使用 JSON 风格的树结构，而是使用自己的专有格式和自动生成的对象 id，这造成了很多合并冲突。

有两种方法可以解决这个问题:

1.  您可以创建一个工作区，并将您的项目拆分成许多小项目。
2.  您可以使用`xcodegen`生成一个项目，并将其从您的存储库中排除。

在本文中，我将描述第二种方式。

# 什么是`xcodegen?`

这是一个从`yaml`描述生成 Xcode 项目的免费开源工具。

![](img/0bf88940d0d0c28c6f9eea0c0c84a633.png)

来自[https://github.com/yonaskolb/XcodeGen](https://github.com/yonaskolb/XcodeGen)的 Xcodegen 标志

项目描述文件不必包含整个结构。`xcodegen`按照 YAML 文件中定义的规则，自动从选定的文件夹中查找文件。

支持不同的目标、构建设置、定制模式、单元测试和许多其他特性。让我们回顾一下它是如何工作的。

## 它是如何工作的

首先，你需要安装`xcodegen`:

```
brew install xcodegen
```

其次，创建一个`project.yaml`文件。您可以编写自己的配置，或者使用来自官方 GitHub 库的示例:

```
[https://github.com/yonaskolb/XcodeGen](https://github.com/yonaskolb/XcodeGen)
```

最后，运行:

```
xcodegen generate
```

如果成功，你会得到一个`xcodeproj`文件夹，即使你以前没有它。现在可以用 Xcode 或者 AppCode 打开了。

## 配置

`xcodegen`有非常丰富的配置文件。它有一个`yaml`格式，默认名称是`project.yaml`。第一行通常是项目的名称:

```
name: myproject
```

它定义了项目名和项目文件名。`options`参数设置项目范围的选项:

```
options:
  minimumXcodeGenVersion: 2.23
  deploymentTarget:
    iOS: "13.0"
```

上面的例子设置了一个最小的`xcodegen`版本。您需要使用支持您使用的所有功能的最低版本。新版本可能会添加新选项。最低版本应该支持您使用的所有选项。`deploymentTarget`设置最低平台版本。每个平台的版本号都不同。

`packages`选项添加了对 Swift Package Manager 包的引用:

```
packages:
  Yams:
    url: https://github.com/jpsim/Yams
    from: 2.0.0
```

您可以在这里列出所有 SPM 依赖项，这样以后就可以只通过名称来引用它们。

配置文件可以包括其他文件:

```
include:
  - config.yml
```

如果您有多个项目的一些基本配置，或者想要缩短您的主配置文件，可以使用这种方法。

最重要的配置项是`targets`。它用特定于目标的选项设置了一个目标列表(应用程序、库、框架、单元测试等)。

```
targets:
  myapp:
    type: application
    platform: iOS
    sources:
      - path: project_root
        excludes: 
          - "*.md"
      - path: project_additional_files
    dependencies:
      - target: mylib
      - framework: SomeFramework.framework
      - sdk: libc++.tbd
      - package: Yams
    settings:
      PRODUCT_NAME: My Project
      SWIFT_VERSION: 5.7
      ENABLE_BITCODE: YES
  mylib:
    type: framework
    platform: iOS
    sources:
      - path: library_path
```

这种配置非常简单。每个目标都有一个`type`，表示它是一个库、框架、应用程序还是其他目标。`platform`定义目标平台，例如`iOS`。

`sources`有一个源文件的路径列表。它们相对于`project.yaml`文件。您可以使用`excludes`列表排除一些文件。

`dependencies`设置项目依赖列表。它们可以是同一个项目的目标、作为文件夹分发的框架、静态库、SPM 包等等。

`settings`包含应用程序构建设置列表。例如，标题搜索路径、ld 标志、位代码、swift 版本等等。您可以在原始 Xcode 项目中看到密钥列表。

你可以在`xcodegen`的官方 GitHub 库上找到完整的文档:

```
[https://github.com/yonaskolb/XcodeGen](https://github.com/yonaskolb/XcodeGen)
```

## `.gitignore`

因为我们的主要目标是消除合并冲突，所以您需要从存储库中排除项目。如果您使用 git——它是 Xcode 项目最流行的存储库——您需要将这一行添加到`.gitignore`文件中:

```
my-project.xcodeproj/
```

如果你也使用`CocoaPods`，你也可以删除`Pods`文件夹和`workspace`文件:

```
my-project.xcworkspace/
Pods/
```

## `xcodegen`同`CocoaPods`

`xcodegen`和`CocoaPods`可以一起使用。您需要首先生成一个项目，然后安装 pod:

```
xcodegen generate
pod install
```

您可以编写一个运行这些命令的简单脚本。如果使用其他自动生成的内容，也可以在此脚本中生成。

## 编辑生成的项目

可以编辑生成的项目进行实验，或者创建文件并添加到项目中。但是请记住，每次运行`xcodegen generate`时，所有的更改都将被丢弃。这就是为什么您需要在将更改推送到存储库之前在`project.yaml`中进行更改。

唯一的例外是文件。当您使用 Xcode 创建一个新文件时，它会出现在文件系统中。从文件系统中读取文件，所以即使在重新生成之后，它们也会出现在你的项目中。

# 结论

`xcodegen`是一个非常强大的工具，涵盖了 Xcode 的全部或几乎全部特性。它有助于避免合并冲突，并允许直接从文件树中生成项目，而无需手动将它们添加到项目中。

我向所有至少有 2-3 名开发人员参与同一个项目的团队推荐这个工具。尤其是大项目。

编码快乐！
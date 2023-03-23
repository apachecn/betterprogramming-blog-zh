# iOS 因素:构建高质量 iOS 应用的最佳实践

> 原文：<https://betterprogramming.pub/ios-factor-best-practices-for-building-high-quality-ios-apps-4046b1dd72d4>

## 每个主题都包含一个因素，描述了 iOS 应用程序开发的理想状态

![](img/5db2c6a74d173388731f94995c4c426d.png)

埃米尔·普林特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

那种感觉，当你从 Git 中取出旧项目时，它不能编译…

即使这个项目在 App Store，“生产就绪”，之前的开发者也没有维护好。可能的原因有很多—弃用 [Swift](https://developer.apple.com/swift/) 版本、外部依赖、证书缺失……

[*iOS-factor*](https://ios-factor.com/) 的灵感来自著名的[十二因子 App 框架](https://www.12factor.net/)，
一种编写高质量 web 服务的方法论。iOS-factor 使用相同的结构和相似的原则，重新编写并应用于 iOS 应用程序开发流程。

它旨在为构建高端 iOS 应用程序提供一组最佳实践。

每个主题都包含一个因素，该因素描述了 iOS 应用程序开发流程的某个类别的理想状态。

本文是一个总结。也可以看[全系列](https://ios-factor.com/)。这个项目是由费利克斯·克劳斯发起的。

# TL；速度三角形定位法(dead reckoning)

1.  [依赖关系](https://ios-factor.com/dependencies):显式声明并隔离依赖关系。
2.  [配置](https://ios-factor.com/config):代码中没有配置，附带默认配置，允许 OTA 更新。
3.  [开发/生产对等](https://ios-factor.com/dev-prod-parity):让开发、测试和生产尽可能相似。
4.  [部署](https://ios-factor.com/deployment):自动化您的部署，这样您就可以从任何机器上发布它。
5.  [更喜欢本地而不是远程](https://ios-factor.com/prefer-local-over-remote):让 iOS 应用足够智能，尽可能不使用后端。
6.  [向后兼容的 API](https://ios-factor.com/backwards-compatible-apis)。
7.  [应用版本化](https://ios-factor.com/app-versioning):自动化应用的内部版本号和版本号以保持一致性。
8.  [回滚](https://ios-factor.com/rollbacks):恢复导致问题的构建。
9.  [数据的持久性](https://ios-factor.com/persistence-of-data):存储数据时遵循苹果指南。

# 1.属国

理想情况下，您的构建工具从不依赖于系统级包的隐式存在。

它通过依赖项声明清单完整准确地声明所有依赖项。这包括 [Xcode](https://developer.apple.com/xcode/) 、 [CocoaPods](https://cocoapods.org/) 和 [fastlane](https://fastlane.tools/) 的确切版本。

显式依赖声明的好处是，它简化了新开发人员对应用程序的设置，以及拥有一个可靠的构建系统，该系统还能够以可重复的方式再次运行过去的构建。

由于 iOS 开发不能容器化，就像 web 开发的情况一样，我们仅限于使用第三方工具来满足这一需求，直到苹果提供官方解决方案。

# 2.配置

应用程序有时会将配置作为常量存储在代码中。这违反了 iOS-factor，它要求将配置与代码严格分离。配置在不同的部署中有很大的不同，代码则没有。

有许多方法可以在构建时注入配置值:

*   配置文件(如 [JSON](https://www.json.org/) 或 [YAML](https://yaml.org/) 文件)。
*   [CocoaPods-keys](https://github.com/orta/cocoapods-keys) 在构建期间正确隐藏密钥并将其应用到您的 iOS 应用程序。
*   定制解决方案(例如，使用构建阶段)。

由于 iOS 平台上的部署比服务器部署慢得多，您可能需要一种快速更新空中配置(OTA)的方式来快速响应问题。

OTA 配置更新功能强大，允许您立即:

*   运行 A/B 测试，以便仅为部分活动用户启用某些功能或 UI 更改。
*   旋转 API 密钥。
*   更新已更改的 web 主机或其他 URL。
*   远程禁用功能或隐藏按钮。

实现配置 OTA 更新的潜在方法:

*   实施您自己的解决方案。
*   专有 web 服务，如 [Firebase 远程配置](https://firebase.google.com/docs/remote-config/)。
*   像[这样的开源服务标志着](https://github.com/rwbutler/FeatureFlags)。

# 3.开发/生产平价

从历史上看，开发(开发人员在他们的本地机器上对应用程序进行实时编辑)和生产(应用程序部署在应用程序商店上，由最终用户访问)之间有很大的差距。

*   **时间差:**一个开发人员可能需要几天、几周甚至几个月的时间来开发代码。
*   **人员缺口:**所有 iOS 开发者写代码，只有一个人知道如何部署 app。
*   **工具差距:**开发人员可能正在使用一个运行不同版本的临时服务器。开发人员可能会使用与部署版本不同的 Xcode 版本。

iOS-factor 应用程序旨在通过缩小开发和生产之间的差距来实现[持续部署](https://avc.com/2011/02/continuous-deployment/)。

看看上面描述的三个差距:

*   让时间差距变小。开发人员可能会编写代码并在几天后部署。
*   让人员差距变小。编写代码的开发人员密切参与部署它，并观察它在生产中的行为。要做到这一点，最好的方法是完全自动化你的 iOS 应用程序的发布过程，并将技术诀窍放在代码声明中，而不是文档中。
*   使工具间隙变小。让开发和生产尽可能相似。遵循 iOS-factor 的[依赖关系](https://ios-factor.com/dependencies)因素的原则，使用`.xcode-version`文件，并明确定义所有其他依赖关系。

# 4.部署

如[依赖性](https://ios-factor.com/dependencies)因素中所述，代码库应该包括构建、测试和发布 iOS 应用程序所需的所有依赖性。

不幸的是，因为 Xcode 必须在 macOS 上运行，所以我们不能使用 [Docker](https://www.docker.com/) 或容器。

目前，作为 iOS 开发者，我们可以采取的最佳方法是:

*   使用 [Xcode::Install](https://github.com/krausefx/xcode-install) 自动安装 Xcode。
*   利用一个[。xcode 版本文件](https://github.com/fastlane/ci/blob/master/docs/xcode-version.md)指定确切的 Xcode 版本。
*   在配置文件中定义所有依赖关系(参见 d [依赖关系](https://ios-factor.com/dependencies)因素)。
*   使用像 [fastlane](https://fastlane.tools/) 这样的部署工具来自动化整个部署过程。
*   自动化代码签名(例如 [codesigning.guide](https://codesigning.guide/) )。
*   经常部署，最好是每周一次。

# 5.更喜欢本地而不是远程

近年来，一些开发团队已经开始使用需要更少开发工作的方法，以牺牲用户的应用程序体验为代价。他们将更多的逻辑转移到远程后端，并让 iOS 应用程序成为显示服务器结果的瘦客户端。

当应用程序在互联网连接不太好的情况下使用时，这种方法会让用户感到沮丧。

出于各种原因，应用程序应该尽可能多地在设备上执行业务逻辑和计算:

*   隐私:避免向远程服务器发送数据。
*   速度:向服务器发送数据并等待响应需要时间，并且可能会失败(例如不稳定的 WiFi)。
*   数据使用:用户通常有每月数据限制。
*   扩展:如果你的应用流行，你有责任扩展后端服务。
*   电池寿命:使用移动数据对电池寿命来说是昂贵的。
*   可靠性:一些国家仍然有不可靠的 LTE/3G 连接。

如果你的应用程序需要互联网连接(例如社交网络应用程序或拼车应用程序)，你的应用程序应该仍然可以在没有互联网连接的情况下工作(只读模式)，以访问历史数据(例如最近的骑行，最近的直接消息)。

# 6.向后兼容的 API

虽然大多数最终用户会在几周内更新到最新版本，但总有一小部分用户不会。

基本的概念是，不要更新现有的 API，而是添加一个新的 API，让它们并行运行。

```
https://your-api.com/1.0/drivers.json
https://your-api.com/1.1/drivers.json
```

# 7.应用版本控制

版本号和内部版本号一起工作，唯一地识别应用的特定应用商店提交 **:**

*   版本号(`CFBundleShortVersionString` ) —在 Xcode 中显示为`Version`。它也被称为营销版本:对最终用户可见的版本。它必须在每次公共应用商店发布时递增。
*   构建号(`CFBundleVersion` ) —在 Xcode 中显示为`Build`:一个递增的数字。

在今天的 iOS 开发过程中，没有理由手动更改这些数字。

不需要使用第三方工具，Xcode 内置了一个叫做`agvtool`的工具。([更多详情](https://developer.apple.com/library/content/qa/qa1827/_index.html))。

# 8.卷回

App Store 本身不允许回滚，因此本节将描述如何使用现有的技术实现类似的结果。

使用分阶段发布，您可以从活动用户的一个子集开始，慢慢地将一个构建推广到生产。

这种方法的主要好处是内置了在大量用户使用之前暂停部署的方法，允许您替换二进制文件。

然而，即使是分阶段发布，也没有办法完全撤销一个构建。

与分阶段发布一样，App Store 和 [TestFlight](https://developer.apple.com/testflight/) 版本只能通过以下方式更新:

1.  在版本控制系统中返回到您想要回滚到的状态。
2.  增加项目的版本号或内部版本号。
3.  构建和共同设计您的应用程序。
4.  通过测试服务或应用商店分发你的应用。
5.  用户必须更新他们手机上的应用程序。

上述步骤可以手动完成，但是，建议完全自动化该过程，以便您可以快速做出反应。

替代方案:放弃旧建筑:

1.  访问引入回归之前的旧构建(`.ipa`文件)。
2.  更新`Info.plist`文件中的版本/内部版本号。
3.  放弃构建。
4.  将其作为新版本分发。

然而，重新签名 iOS 应用程序通常会带来更多问题，尤其是因为 Xcode 命令行工具没有提供好的方法来做到这一点。

# 9.数据的持久性

根据苹果的指导方针存储数据和配置对于应用的生命周期至关重要，特别是在 iCloud sync、升级到新手机以及从备份恢复手机时。

确保您遵循苹果官方的 [iOS 数据存储指南](https://developer.apple.com/icloud/documentation/data-storage/index.html):

*   `Documents`:将此目录用于用户生成的内容，它将被备份。
*   `Caches`:将该目录用于可以重新生成的数据。
*   `tmp`:使用这个目录存放临时文件。
*   利用文件的`do not back up`属性。

不要在这些目录中存储敏感的用户信息(如密码或会话)。相反，使用[钥匙串 API](https://developer.apple.com/documentation/security/keychain_services) 。

# 结论

这是一个活项目，由 iOS 开发社区维护。

您可以在 [GitHub](https://github.com/ios-factor/ios-factor.com) 上找到完整的源代码，更新现有页面，或者添加新的因素。
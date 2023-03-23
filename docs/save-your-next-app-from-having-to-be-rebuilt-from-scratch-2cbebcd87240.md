# 让您的下一个应用程序免于从头开始重新构建

> 原文：<https://betterprogramming.pub/save-your-next-app-from-having-to-be-rebuilt-from-scratch-2cbebcd87240>

## 现在就计划吧，这样你就不会在以后烧掉它

![](img/1f50e8865dcb4f6a0e2ce745e179eb34.png)

费德里科·雷斯皮尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

想象你开始一个新项目。还没有编写代码，你有绝对的自由来构建它。

您从收集业务需求开始，最终讨论产品特性。移动应用的技术要求通常默认为一般特征，如响应迅速的用户界面、流畅的动画、稳定的工作等。

在这篇文章中，我收集了一系列隐含的技术需求，这些需求在开始时经常被忽略，但可能会导致代价高昂的重构，甚至是后来的项目重建。

> "程序员可以为设计良好的软件稳定地添加功能。"—肯特·贝克，[响应式设计](https://magazines.pragprog.com/2009/pragpub-2009-09.pdf)

我们无法预见未来，也无法为项目的每一个支点做好准备，但是我们可以设计出一个灵活的系统来进行某些修正。让我们开始吧。

# 程序导航

我把这个放在第一位，因为这个特性是这两个因素的极端组合:

1.  在项目开始时，这一点经常被忽视。
2.  当集成到预先存在的代码库时，它会产生灾难性的后果。

来自推送通知、spotlight 搜索或快速行动的深度链接被认为是最好的功能。MVP 通常不需要它们，经理可能会明确地告诉你现在不要做这个。

但是，即使没有深度链接，你的应用程序可能仍然需要你做一些非平凡的路由，比如打开一个不同的标签，然后呈现一个模态屏幕，或者关闭两个上下堆叠的模态屏幕。

有许多方法可以对这些本地导航事件进行编码:

*   使用一群代理将命令互相转发。
*   利用响应链。
*   关闭回调。
*   通过通知中心广播通知。

但是几乎每一个应用于未准备好的系统的解决方案都将是一个天文数字的代码味道，有许多漏洞，比如异步调度。

理想的情况是让你的应用程序准备好执行打开特定屏幕的命令，而不管现在显示的是什么屏幕，但这需要一个为项目的路由模型定制的高级屏幕协调器。

这个问题的一个“足够好”的解决方案是对 UIKit 应用程序使用标准的[协调器](https://www.hackingwithswift.com/articles/71/how-to-use-the-coordinator-pattern-in-ios-apps)模式，或者对 SwiftUI 使用[集中式导航状态](https://nalexn.github.io/swiftui-deep-linking/?utm_source=medium_bp)。

它不会给代码库增加太多的开销，但会让你的应用程序更加灵活和可测试，所以我倾向于在几乎每个项目中都包含这一点，除了小项目。

可测试性是另一个非常重要的原因，也是为什么您希望在您的项目中有解耦的编程控制的导航。

# 易于管理的数据流

应用程序的功能往往会随着时间的推移而增长。业务逻辑中案例数量的增加通常会导致应用程序中数据流的倍增。可能会有几个屏幕使用同一组数据，同步屏幕之间的变化可能会成为一个大问题。

一开始，如果我们没有设计一个清晰、统一的数据流，维护对状态和数据的控制的相关复杂性将随着我们添加的实体数量呈指数增长。

为了解决这个问题，我们可以求助于“真实的单一来源”原则，其中我们明确地避免了状态重复，并为 UI 刷新设计了基于订阅的机制。

[WWDC 19:通过 SwiftUI 的数据流](https://developer.apple.com/videos/play/wwdc2019/226/)解释了如何在 SwiftUI 中使用这一概念，而在 UIKit 中，我们需要投入额外的精力来设计状态更新分发渠道。

在 [Clean Swift](https://clean-swift.com/) 和 [Redux](https://github.com/ReSwift/ReSwift) 中使用的单向数据流已经被证明是一个非常可扩展的解决方案，用于管理日益复杂的应用程序的状态。

我将在 UIKit 的[状态管理指南](https://nalexn.github.io/state-management-guide-ios/?utm_source=medium_bp)中详细阐述这个主题。

# 易测性

关于测试的有用性有不同的观点。测试在一个 [TDD](https://www.guru99.com/test-driven-development.html) 工作流中可能是最高优先级的，就像它们对于一个原型或者一个 MVP 可能完全被忽略一样，尤其是在时间紧迫的情况下。

在您选择不编写测试的场景中，您仍然应该构建项目，以便可以在以后添加测试。

可测试性不仅允许测试的存在，而且最终有助于组件的可重用性和项目中更清晰的关注点分离。

这里有一种在项目中实现可测试性的方法:

*   将模块分解为多个责任( [SRP](https://en.wikipedia.org/wiki/Single_responsibility_principle) )。
*   [避免](https://www.swiftbysundell.com/articles/avoiding-singletons-in-swift/)使用任何全局变量，引用对象(包括单例)，而[注入依赖](https://ilya.puchka.me/dependency-injection-in-swift/)。

# 静态代码分析

我为一个新项目做的第一件事就是配置静态代码分析工具，比如 SwiftLint 和 cpd。

SwiftLint 是绝对的必需品。无论您是单独工作还是在团队中工作，这个工具都允许您坚持统一的代码风格，避免过于复杂或语法过多的代码。

最好的部分:它在你写代码的早期就警告你。这使得代码审查更有效率，并极大地有助于代码库的清晰性和可读性。当我想禁止使用某些 API 时，我经常添加自定义规则(例如， [NotificationCenter](https://objcsharp.wordpress.com/2013/08/28/why-nsnotificationcenter-is-bad/) )。

[复制/粘贴检测器](https://pmd.github.io/latest/pmd_userdocs_cpd.html) (cpd)不仅有助于识别明显重复的代码，还有助于识别结构非常相似的代码块。这些都是重构为可重用的方法或实体的很好的候选对象，所以即使你无意中复制了一些功能，它也是非常有用的。

# 建立工作关系网

网络是一个相当大的主题，所以我只列出随着项目的发展可能需要的东西:

*   [链接](https://nalexn.github.io/callbacks-part-3-promise-event-stream/#Promise_advantages)几个没有嵌套回调的请求。
*   处理来自服务器的格式错误的响应。
*   [正当认证](https://developer.apple.com/documentation/foundation/url_loading_system/handling_an_authentication_challenge)和 401 未授权问题的处理。
*   单元测试或离线演示模式的响应模拟。
*   取消正在运行的请求或执行自动重试的选项。

我更喜欢围绕标准 URLSession 编写一个定制的轻量级包装器，而不是使用任何第三方工具，如 Alamofire 或 Moya。定制包装器可以更容易地适应项目的需求，而普通 URLSession 的功能却被低估了。

不管怎样，Alamofire 和其他库都是 URLSession 的包装器，如果没有它也能轻松完成，为什么还要在项目中引入一个重量级的依赖项呢？

# 属国

因为我在项目中触及了第三方依赖的话题，所以这里有一些建议给你:在添加任何新的依赖之前，一定要三思。您希望在项目中尽可能少地使用这些工具的原因有很多:

*   通常，框架被过度工程化以覆盖更广泛的用例。
*   大规模动态库和框架增加了应用程序的启动时间。
*   你的应用程序体积膨胀。
*   Xcode 要花更多的时间在编译上。`DerivedData`文件夹变成了构建工件的巨大转储。
*   第三方代码通常质量一般，测试覆盖率低，漏洞多。
*   你不能指望每个依赖项都能很快迁移到新的 Swift 或 iOS 版本。随着时间的推移，许多图书馆都被废弃了。
*   如果您发现框架缺少您需要的一点点功能，您可以尝试提交一个 pull 请求。但是并不是所有的维护人员都会及时响应，所以你可能会被卡住。
*   框架可能会在每个主要版本中频繁地修改它们的 API。迁移到新版本可能非常耗费人力。

# 内存和电池消耗

引用苹果[能效指南](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html)的一句话:

> *即使是应用程序中很小的低效率累积起来，也会显著影响电池寿命、性能和响应能力。作为一名应用开发者，* ***你有义务*** *确保你的应用尽可能高效地运行。*

事实是，只要应用程序运行正常，产品经理或开发人员几乎从不认为内存和电池效率很重要。但它们应该是。这些参数直接影响用户体验，并最终将好的应用从最好的应用中分离出来。

我敢打赌，你曾经遇到过这样的情况，你最小化了 10 秒钟的应用程序，当你打回去的时候会重新启动，丢失你输入的所有文本。

这是应用程序忽略了[内存消耗策略](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background)的结果:它在后台保留的内存越多，iOS 终止应用程序以回收必要内存资源的几率就越高。

您想要做的是处理`applicationDidEnterBackground`并释放缓存和其他可以从磁盘上的副本恢复的资源。

# 状态恢复

当然，即使你释放了所有可能的内存来准备后台模式，也不能保证系统不会杀死应用程序。

缓解问题的一个方法是当用户回来时[恢复状态](https://developer.apple.com/documentation/uikit/view_controllers/preserving_your_app_s_ui_across_launches)。

但是，您需要小心，尊重用户的会话到期时间，尽早中止恢复，根据需要显示登录 UI。

对于用户花费大量时间并且他们的进度对他们很有价值的应用程序来说，状态恢复功能是至关重要的。数据丢失带来的挫败感可能会导致你的应用卸载和差评。

# 易接近

[可访问性](https://developer.apple.com/design/human-interface-guidelines/accessibility/overview/introduction/)帮助有障碍的人使用你的应用。虽然你并不总是明确支持它，但每个应用程序都会响应全局设置——例如增加的文本大小，这很容易破坏你的 UI。

你不希望这种情况发生，所以最好根据用户可以在“设置—可访问性”下调整的各种设置来测试应用程序。

如果您正在开发 SwiftUI 应用程序，您可以使用我专门为此目的创建的[工具](https://github.com/nalexn/EnvironmentOverrides):

![](img/9dadb9d541abb08878c8fafd8e09b79c.png)

# 本地化

大多数时候，开发人员被要求只为目标市场开发一个本地化的应用程序。一个突如其来的新需求可能会让开发者措手不及，那就是需要[本地化](https://developer.apple.com/internationalization/)一个已经为更多语言构建的应用。

但你可以为此做好准备。

在 UIKit 中，只需要一点原则，在项目中永远不要使用`"Plain text string"`,而总是将它们包装在`NSLocalizedString`中，就像这样:

```
NSLocalizedString("Text", comment: "")
```

之后，您可以运行一个[自动化工具](https://github.com/SwiftGen/SwiftGen),它将您项目中的所有字符串收集到一个您可以使用的`Localizable.strings`文件中。

在 SwiftUI 中，这稍微容易一点，因为`Text`视图默认认为字符串是可本地化的。

本地化可能不会自动产生预期效果的另一种情况是各种格式化程序，如`DateFormatter`或`NumberFormatter`。

所有的格式化程序都有一个属性`locale`，您需要通过设置为`Locale.current`或`Locale(identifier: ...)`来对其进行适当的配置。

本质上，这就是对你的所有要求。

您可以使用我上面提到的工具来测试应用程序如何针对不同的本地化进行调整。

# 主题

您应该始终避免在整个项目中复制粘贴颜色、字体名称和其他与外观相关的参数的硬编码值，因为有一天您可能会被要求进行轻微的重新设计。

为整个项目调整一种颜色或字体不应该涉及改变超过几行代码，最方便的方法是使用 [UIAppearance](https://developer.apple.com/documentation/uikit/uiappearance) 配置全局主题设置。

在 SwiftUI 中，您使用视图修改器(比如`.accentColor(...)`)，将它们应用到根视图来改变整个层次的外观。

考虑到现在的应用程序应该理想地支持可选的[黑暗模式](https://developer.apple.com/documentation/xcode/supporting_dark_mode_in_your_interface)，从一开始就以正确的方式做事情会更容易，并在以后为自己节省数小时繁琐的重构。

有关更详细的说明，您可以参考官方指南(上面的链接)。

# 日志、分析和崩溃报告

另一个“可选的”需求是需要为你的用户解决应用程序的问题。

无论是崩溃还是应用程序的其他意外错误行为，当这种情况发生时，你都不会有一个调试器附加到程序上。

崩溃报告工具是任何生产应用的必备工具。它不仅让您知道您的应用程序有多稳定，而且还为您提供了非常有用的故障排除信息，包括调用堆栈跟踪和关于用户发生崩溃的频率的统计数据。

市场上有许多解决方案，但我最喜欢的免费解决方案是谷歌的 [Firebase Crashlytics](https://firebase.google.com/docs/crashlytics) 。

崩溃报告平台通常带有一个收集使用分析的选项，因此请查看是否支持该选项，并记录关键用户的操作，例如访问提供付费功能或产品的屏幕。

虽然分析主要用于收集与业务相关的统计数据，但您可能仍然希望更彻底地记录日志，以便进行故障排除。

最后，记得从记录和分析中排除任何敏感信息，因为这个[可能会将](https://krebsonsecurity.com/2019/03/facebook-stored-hundreds-of-millions-of-user-passwords-in-plain-text-for-years/)变成贵公司的重大声誉和法律麻烦。

# 结论

正如您所看到的，有相当多的隐藏需求可能会也可能不会变得明显，所以请不要过度工程化。

> *“诱惑是现在就把这些设计想法放到系统中，因为你知道你最终会需要它们**。过早地过度设计会导致系统实际使用的反馈延迟，使添加功能变得更加复杂，并使修改设计更加困难。”—肯特·贝克，* [*响应式设计*](https://magazines.pragprog.com/2009/pragpub-2009-09.pdf)

虽然我完全同意这种说法，但是我在这篇文章中收集的大多数实践只需要最少的努力和训练。此外，它们几乎没有造成任何伤害的可能性。

从一开始就把事情做对并不总是可能的，但是这些技巧可以节省你几个小时不必要的工作。
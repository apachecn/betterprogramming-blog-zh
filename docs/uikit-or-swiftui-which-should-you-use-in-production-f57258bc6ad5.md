# UIKit 和 SwiftUI:在生产中应该使用哪个？

> 原文：<https://betterprogramming.pub/uikit-or-swiftui-which-should-you-use-in-production-f57258bc6ad5>

## SwiftUI 准备好上线了吗？

![](img/0f77d8e522335a6e2c7fd953ddf43911.png)

马里奥·多贝尔曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

苹果最近发布了 iOS 14，这意味着 SwiftUI 已经有了一年的缓冲期，不仅可以被爱好者在他们的宠物项目中采用，还可以被企业团队在他们的商业应用中采用。

字面上大家都说写 SwiftUI 代码很好玩，但是 SwiftUI 到底是玩具还是专业工具？如果我们想认真对待它，我们需要考虑它作为工具的稳定性和灵活性，而不是作为玩具。

在生产代码中什么时候开始使用 SwiftUI 比较合适？

如果你在 2020 年到 2022 年之间开始一个新的重大项目，这个问题很难回答！

尽管 SwiftUI 带来了所有的创新，即使到了 iOS 14，我们[仍然有漏洞，缺乏定制的灵活性](https://steipete.com/posts/state-of-swiftui/)。

虽然这可以通过情景调用 UIKit 来缓解，但您能估计最终有多少代码会用 UIKit 编写吗？从长远来看，SwiftUI 会成为一种负担吗？在这种情况下，你最好用 UIKit 写所有的东西。

我们只能赌 iOS 15 不会有 SwiftUI 的问题。意味着最多到 2022 年(随着 iOS 16 的发布)，我们才会有一个放松和充分信任 SwiftUI 的完美时刻。

在本文中，我将在两个场景中阐述如何构建项目:

1.  您正在支持 iOS 11 或 12，但考虑在可预见的未来将应用程序迁移到 SwiftUI。
2.  您正在支持 iOS 13+,但希望控制与 SwiftUI 相关的风险，并能够无缝地回退到 UIKit。

# 粘性 UI 框架

历史上，UI 框架是移动应用架构的核心。我们占领了 UIKit，并围绕它建造了一切。

考虑一下你上一个 UIKit 项目，试着评估一下要完全摆脱 UIKit 并用另一个 UI 框架代替它需要多少努力，比如 [AsyncDisplayKit](https://github.com/texturegroup/texture/) ？

对于大多数项目来说，这意味着完全重写。

Web 工程师会嘲笑我们，因为他们总是有大量的 UI 框架。因此，他们学会了应用“依赖规则”,并将 UI 视为系统中的外围“细节”,就像他们使用的数据库的具体类型一样。

这是否意味着我们这些移动开发者没有将 UI 从业务逻辑中分离出来？我们做到了，对吗？MVC，MVVM，毒蛇等等。在那里帮助我们，但我们还是被困住了。

移动应用很少负责任何核心业务逻辑，例如计算贷款利息和批准贷款。企业希望最小化这里的众多风险，所以他们在后端运行这样的逻辑。

然而，现代移动应用程序上运行着大量的业务逻辑，但这种逻辑是不同的。它只是更关注表现形式，而不是业务运行的核心规则。

这意味着我们需要更好地将表示相关的业务逻辑从我们使用的 UI 框架的细节中分离出来。

如果我们做不到这一点，难怪框架会被完整地嵌入到代码库中。

UIKit 和 SwiftUI 的 API 对于代码库都是粘性的。这些框架正在推动开发人员使它们超级集中，绑定到所有东西，直接用在根本不是 UI 的地方！

我们以 SwiftUI 中的`@FetchRequest`为例。它将`CoreData`模型细节混合在表示层中。看起来很方便。但同时，这也是对 CS 中多个软件设计原则和最佳实践的重大违反。这种代码在短期内节省了时间，但从长远来看，可能会对项目造成重大损害。

`@AppStorage`怎么样？UI 层中的数据 IO 操作。你如何测试它？你能容易地识别容器中的键名冲突吗？您能否将它无缝迁移到另一种数据存储类型，如 Keychain？

同样，开发速度被最大化，质量保证、可维护性和代码重用被忽略。

还有屏幕走线呢？

UIKit 总是小声对我们说:“嘶！就用`presentViewController(:, animated:, completion:)`；别烦那些讨厌的协调员！”

另一方面，SwiftUI 并没有窃窃私语。它冲着我们尖叫:“听着。你要么照我说的做，要么我就用最精细的方法杀了你全家！”(*)

有没有办法保护我们的代码库免受这些野蛮 API 的攻击？

当然可以！

(*)令人尖叫的 API 通常是一件好事——程序员犯错误的机会更少了。然而，当这些 API 不能正常工作时，就会成为一个巨大的问题，例如 SwiftUI 有问题的编程导航。

# 分离用户界面层

如你所见，框架到处都有陷阱。

你咬的钩子越多，就越难在特定的屏幕或整个应用上放弃使用这个框架。

如果我们希望系统足够坚固，能够从 UIKit 过渡到 SwiftUI(反之亦然),我们需要确保 UI 和系统其余部分之间的边界不是木栅栏，而是长城！

任何东西都不应该溜进来，甚至是字符串格式。

你能在没有 UIKit 或 SwiftUI 的情况下将一个 float 5434.35 转换成“$5，434.35”吗？太好了——让我们在别处做吧！

框架中用于屏幕路由的 API 是否强加了视图的紧密耦合？我们需要引入一个边界来隔离它们。

我们不仅需要从 UI 层提取尽可能多的逻辑，还需要使 UIKit 组件及其 SwiftUI 对应组件与它们所插入的套接字完全兼容。

我们如何让 UIKit 和 SwiftUI 达到一个共同的标准？

我们知道 SwiftUI 完全是数据驱动的，由反应式数据绑定提供支持。幸运的是，UIKit 可以用 MVVM 和反应式框架来扭曲。

这意味着数据源、委托、目标动作和 UIKit APIs 的其余部分应该在 UI 层中隔离。

线条`import UIKit`不应该出现在任何视图模型及更远的地方。

我应该注意到，只要 UI 组件是完全数据驱动的，屏幕模块的确切架构模式就不重要。为了便于举例，我将在本文中提到 MVVM。

现在。我们应该为视图模型使用哪个反应式框架？我们知道 SwiftUI 只与 [Combine](https://developer.apple.com/documentation/combine) 一起工作，而 UIKit 最好由 RxCocoa 支持。

两种方式都有可能，所以这个要看你能不能支持 iOS 13 (Combine)以及你有多爱 RxSwift。

两者兼顾吧！

# RxSwift 和 SwiftUI 之间的桥梁

Combine 从 iOS 13 开始提供，对于那些仍然需要支持 iOS 11 或 12 的人来说，这是一个交易破坏者。

这里我讲一个从(UIKit + RxSwift)迁移到(SwiftUI + RxSwift)的简单方法。

考虑这个最小的设置:

视图是数据驱动的:视图模型完全控制视图的状态变化。

让我们在不接触 ViewModel 代码的情况下将这个屏幕迁移到 SwiftUI。

有两种方法可以让它工作:

1.  定义一个新的`ObservableObject`，其中的`@Published`变量被绑定到来自原始视图模型的`Driver`(或`Observable`)。
2.  在 SwiftUI 的视图中调整每个`Driver`到`Publisher`并绑定到`@State`。

# 将可观察对象绑定到@Published

对于第一种方法，我们需要创建一个新的`ObservableObject`来反映原始视图模型中的每个可观察变量:

原始视图模型和适配器之间的值绑定代码应该尽可能简洁。下面是`Driver`和`Observable`的桥接情况:

这里我们所需要的是来自 RxSwift 的一个`Binder`，它将值分配给特定的`@Published`值。下面是执行桥接的`binder`函数的一个片段:

回到我们的视图模型，您可以在`Adapter`初始化中进行绑定:

这种方法的一个缺点是样板代码必须为你拥有的每个`@Published`变量重复。

# 将可观察对象绑定到@State

第二种方法需要更少的设置代码，并且基于 SwiftUI 视图消费外部状态的另一种方式:`onReceive` view modifier，为 local `@State`赋值。

这里的美妙之处在于我们可以在 SwiftUI 视图中直接使用原始的 ViewModel:

`viewModel.isLoadingData`是一个`Driver`，所以我们需要将它从 Combine 转换成一个`Publisher`。

社区已经提出了 [RxCombine](https://github.com/CombineCommunity/RxCombine) 库，它允许从`Observable`到`Publisher`的桥接，所以扩展它以支持`Driver`很简单:

# 将 UIKit 与联合收割机连接

如果你支持 iOS 13+，你可以考虑使用 Combine 来构建应用程序中的网络和其他非 UI 模块。

尽管将 Combine 与 UIKit 绑定有些不方便，但从长远来看，当您的项目完全迁移到 SwiftUI 时，选择 Combine 作为驱动应用程序中数据的核心框架应该是有回报的。

同时，您可以在`sink`功能中更新 UIKit 视图:

或者您可以使用前面提到的 RxCombine 库将`Publisher`转换为`Observable`，充分利用 RxCocoa 中可用的数据绑定:

我应该注意，如果我们选择 Combine 作为应用程序中的主要反应框架，RxSwift、RxCocoa 和 RxCombine 的使用应该仅限于 UIKit 视图的数据绑定，因此我们可以轻松地摆脱这些依赖关系以及应用程序中的最后一个 UIKit 视图。

在这种情况下，ViewModel 应该用 just Combine 构建(no `import RxSwift`！).

重温最初的例子:

当需要在 SwiftUI 中重建这个屏幕时，一切都会为您设置好:在 ViewModel 中不需要做任何更改。

# 关于路由的思考

在过去，我已经探索过[编程导航](https://nalexn.github.io/swiftui-deep-linking/)在 SwiftUI 中是如何工作的，根据我的经验，这是 SwiftUI 中仍然遭受各种故障和崩溃以及缺乏动画定制的部分。

久而久之，这个问题肯定会解决的，但是现在，我不会把路由交给 SwiftUI。

当选择退出 SwiftUI 的路由时，我们不会失去太多。只要 SwiftUI 由 UIKit 支持，与我们使用 UIKit 所能实现的相比，就不会有积极的性能差异。

在我为本文构建的示例项目中，我使用了传统的协调器模式(MVVM-R ),这种模式对于用 SwiftUI 的`UIHostingController`构建的屏幕来说工作得很好。

# 结论

如果我们想要控制与使用特定 UI 框架相关的风险，我们应该付出额外的努力来控制它在代码库中的扩展。

SwiftUI 的现有问题不应该阻止您至少在可预见的将来准备好您的项目迁移到这个框架。

从 UI 层提取尽可能多的业务逻辑，并使您的 UIKit 屏幕数据驱动。这样一来，迁移到 SwiftUI 将变得轻而易举。

我已经构建了一个[示例项目](https://github.com/nalexn/uikit-swiftui)，它带有普通的登录/主页/详细信息屏幕，展示了 UIKit 和 SwiftUI 视图如何成为一个外围虚拟详细信息，可以很容易地分离和替换。

有两个目标——一个运行在 UIKit 上，另一个运行在 SwiftUI 上，两者共享代码库的基本部分。
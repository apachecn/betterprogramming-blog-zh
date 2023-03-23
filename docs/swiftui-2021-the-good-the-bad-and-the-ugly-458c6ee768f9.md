# 2021 年的 SwiftUI:好、坏、丑

> 原文：<https://betterprogramming.pub/swiftui-2021-the-good-the-bad-and-the-ugly-458c6ee768f9>

## 生产中的 SwiftUI？还是不行

![](img/8e6d21aacca67166ffe7b39e460d3b18.png)

由[麦斯威尔·尼尔森](https://unsplash.com/@maxcodes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在过去的 8 个月里，我一直在 SwiftUI 中开发复杂的应用程序，包括我们最近在 App Store 中推出的 [Fave](https://apps.apple.com/us/app/fave-close-friends-only/id1541952688 https://apps.apple.com/us/app/fave-close-friends-only/id1541952688) 。我遇到了无数的限制，并为我遇到的大多数挑战找到了解决办法。

简而言之，SwiftUI 是一个非常棒且非常有前途的框架。我觉得是未来。但是，它可能还需要 3-5 年才能达到与 UIKit 一样的可靠性和健壮性。然而，这并不意味着你今天不应该使用 SwiftUI。我在这里的目标是帮助您理解权衡和缺点，以便您可以做出更明智的决定，SwiftUI 是否适合您的下一个项目。

# **好人**

## **1。SwiftUI 是一种乐趣，你可以构建得几乎太快了**

`addSubview`和`sizeForItemAtIndexPath`的日子已经一去不复返了，他们煞费苦心地计算框架，与约束角力，手工构建视图层次。SwiftUI 的声明式和反应式设计模式使得创建响应式布局像 React 一样容易，而且在引擎盖下还有苹果强大的 UIKit。构建视图和启动运行的速度快得令人难以置信。

## **2。SwiftUI 简化跨平台开发**

我最兴奋的一点是，你可以写一次 SwiftUI，跨 iOS (iPhone & iPad)、WatchOS、macOS 使用。你必须为 Android 和 Windows 开发和维护单独的代码库，这已经是一个很大的问题了，所以每一点都有助于减少不同代码库的数量。仍然有一些缺点，我将在“坏的”部分分享。

## **3。你可以免费获得漂亮的过渡、动画和组件**

您可以将 SwiftUI 视为一个实际的 UI 工具包，它提供了制作专业外观的应用程序所需的所有构件。如果你熟悉 CSS 转换，SwiftUI 有自己的版本，可以非常容易地创建完美的交互。声明性语法的美妙之处在于，事情“只是工作”并且看起来像魔术一样，但是也有不好的一面，我稍后也会谈到。

## **4。用户界面完全是状态驱动和反应式的**

如果你熟悉 React，SwiftUI 也是一样的。当你看到你的整个用户界面“反应”、动画和所有的东西时，你高兴地改变了`@State`、`@Binding`和`@Published`的属性，而不是回调地狱。你用`ObservableObject`和`@StateObject`来利用`Combine`的力量。这个方面是 UIKit 最酷的变化之一，感觉非常强大。

## **5。社区正在接受 SwiftUI**

几乎每个人都对 SwiftUI 感到兴奋。有如此多的资源可以用来学习 SwiftUI，从 WWDC，到书籍，到博客——信息就在那里，你只需要搜索它。不管是不是，我在这里整理了一个最好的社区资源列表。

拥有一个充满活力和支持性的社区将加速学习和开发，大量新的库将出现，使 SwiftUI 更加通用。

# 坏事

## **1。SwiftUI 中并非所有功能都可用**

有许多组件缺失、不完整或过于简单，其中一些我将在下面详细介绍。

使用`UIViewRepresentable` `UIViewControllerRepresentable`和`UIHostingController`有解决方法。前两个允许您将 UIKit 视图和控制器嵌入到 SwiftUI 视图层次结构中。后者允许您在 UIKit 中嵌入 SwiftUI 视图。Mac 开发也存在同样的三种(`NSViewRepresentable`等)。

这些桥是弥补 SwiftUI 所缺少的功能的一个很好的权宜之计，但它并不总是一个无缝的体验。此外，尽管 SwiftUI 的跨平台承诺很好，但如果没有可用的东西，您仍然需要为 iOS 和 Mac 实现两次桥代码。

## **2。导航视图还没有真正出现**

如果你想隐藏导航条而滑动手势仍然有效，你不能。我最终用我找到的一些代码创建了一个 [UINavigationController 包装器](https://gist.github.com/chrysb/d7d85e20d8c94fd3e0b753a4abd1c941)。这是可行的，但从长期来看，这不是一个好的解决方案。

如果你想在 iPad 上实现拆分视图，你还不能在纵向模式下同时显示主视图和细节视图。他们选择了一个笨拙的按钮来展示一个默认关闭的抽屉。显然，您可以通过添加填充来解决这个问题，这突出了在争论 SwiftUI 时您必须做的事情。

当你想以编程的方式导航时，这是一种时髦的做法。这里有一个[有趣的讨论](https://forums.swift.org/t/implementing-complex-navigation-stack-in-swiftui-and-the-composable-architecture/39352)。

## **3。文本输入非常有限**

`TextField`和`TextEditor`现在太简单了，你最终会回到 UIKit。我必须为`UITextField`和`UITextView`构建自己的`UIViewRepresentable`(支持自动增长)。要点如下:

## **4。编译器苦苦挣扎**

当你的观点变得更加重要，并且你已经尽你所能地把它分解出来，编译器仍然会气呼呼地告诉你:

> 编译器无法在合理的时间内对该表达式进行类型检查…

这让我慢了很多次。我已经很擅长注释掉代码，并将其缩小到导致问题的行，但在 2021 年调试这样的代码感觉真的很落后。

## **5。matchedGeometryEffect**

当我第一次发现[这个](https://developer.apple.com/documentation/swiftui/view/matchedgeometryeffect(id:in:properties:anchor:issource:))的时候，我觉得它太神奇了。它应该通过匹配一个出现和另一个消失时的几何图形来帮助您更无缝地转换两个不同身份的视图。我认为这将有助于从视图 A 到视图 b 的美丽过渡。

我一直希望能成功。最终，我会远离它，因为它不完美，而且当你在有很多项目的`List`或`ScrollView`中使用它时会导致崩溃。我只推荐在同一个视图中使用这个简单的过渡。当您在多个不同的视图之间共享一个名称空间时，事情开始变得奇怪，包括转换期间的视图剪辑。

## 6。手势受限

SwiftUI 附带了一组新的手势(即`DragGesture`、`LongPressGesture`)，可以使用`gesture`便利修饰符(如`tapGesture`和`longPressGesture`)添加到视图中。在你想做更复杂的交互之前，它们工作得很好。

比如`DragGesture`和`ScrollView`互动不好。即使使用`simultaneousGesture`修改器，在 ScrollView 中放置一个也可以防止滚动。在其他情况下，拖动手势可能会在没有任何通知的情况下被取消，从而使手势处于不完整状态。

为了解决这个问题，我构建了自己的`GestureView`，它允许我在 SwiftUI 中使用 UIKit 手势。我将在下一篇关于最佳 SwiftUI 库和解决方法的文章中分享这一点。

## **7。SwiftUI in Share Extension**

我可能错了，但是共享扩展仍然使用 UIKit。我通过利用`UIHostingController`使用 SwiftUI 构建了一个共享扩展，当加载共享扩展时有明显的延迟，造成了糟糕的用户体验。您可以尝试通过在中设置视图动画来屏蔽它，但它仍然有大约 500 毫秒的延迟。

## **优秀奖**

*   不能访问状态栏(不能改变颜色或截取点击)
*   `@UIApplicationDelegateAdaptor`需要，因为`App`仍然缺少
*   没有向后兼容性
*   `UIVisualEffectsView`导致< iOS15 中的滚动延迟(h/t @ [AlanPegoli](https://twitter.com/alanpegoli?lang=en) )

# 丑陋的

## **1。滚动视图**

这是迄今为止最大的缺点之一。任何开发过定制化 iOS 应用的人都知道我们有多依赖 ScrollView 来增强交互。

*   **主要障碍:** `LazyVStack`在[滚动视图中会导致口吃、抖动和意外行为](https://stackoverflow.com/questions/66523786/swiftui-putting-a-lazyvstack-or-lazyhstack-in-a-scrollview-causes-stuttering-a/67895804)。LazyVStacks 对于需要滚动的混合内容的长列表非常重要，比如新闻提要。**仅此一点就让 SwiftUI 无法投入生产:**苹果已经向我确认这是 SwiftUI 中的一个 bug。目前还不清楚他们什么时候会修复它，但当他们做到了，这将是一个巨大的胜利。
*   **滚动状态:**没有理解滚动状态的原生支持(滚动视图是拖动吗？滚动？偏移量是多少？).虽然有一些解决方法，但是它们可能会很挑剔和不稳定。
*   **分页:**没有对分页滚动视图的原生支持。所以，不要再做像可移动媒体画廊这样的事情了(但是如果你想要更接近的东西，可以使用`[SwiftUIPager](https://github.com/fermoya/SwiftUIPager)`)。从技术上讲，你可以将`TabView`与`PageTabViewStyle`一起使用，但我认为它更适合一些元素，而不是大型数据集。
*   **性能:**使用`List`是性能最好的，避免了`LazyVStack`的口吃问题，但由于过渡的工作方式，对于显示可变大小的内容来说仍然不理想。例如，在构建聊天视图时，转场很奇怪，并且剪辑了子视图，并且您无法控制插入动画的样式。

# 判决

我觉得你绝对应该学习 SwiftUI，自己去理解，去体验其中的乐趣。只是推迟完全采用它。

SwiftUI 不仅为简单的应用程序做好了准备，但在撰写本文时(iOS 15，beta 4)，我认为 SwiftUI 还没有为复杂的应用程序做好生产准备，主要是由于`ScrollViews`的问题和对`UIViewRepresentable`的严重依赖。这让我心碎。特别是对于消息产品、新闻源以及严重依赖复杂视图或想要创建手势驱动的定制体验的产品，目前还不想使用 SwiftUI。

如果您想要细粒度的控制和无限的可能性，我建议在可预见的未来坚持使用 UIKit。通过使用`UIHostingController`包含 SwiftUI 视图，您仍然可以从 SwiftUI 的某些视图(如设置页面)中获益。

# 未来会发生什么？

当我们开始着手我们项目的下一个大的迭代时。我知道这个新项目的交互范围超出了 SwiftUI 目前支持的范围。知道 SwiftUI 在某些关键方面存在不足让我很伤心，但我还没有准备好回到 UIKit，知道当它工作时在 SwiftUI 中构建是多么令人高兴。只是快多了。

SwiftUI 会匹配 UIKit 吗？如果是这样的话，我们可能还需要 3-5 年来移植所有基本的 UIKit APIs。如果没有，那么你总是可以直接进入 UIKit，用 SwiftUI 包装它。

我好奇的是苹果在 SwiftUI 上的投入有多大。他们的长期计划是让所有开发人员完全采用 SwiftUI，还是仅仅成为另一个界面构建器？我真的希望不会。我希望他们全押在 SwiftUI 上，因为 swift ui 的前景令人惊叹。

更多 SwiftUI 技巧和例子，[在 Twitter 上关注我](http://twitter.com/chrysb)。

# 更多视角

*   [swift ui 准备好了吗？](https://www.jessesquires.com/blog/2021/07/01/is-swiftui-ready/)
*   [SwiftUI 的缺点:为什么 SwiftUI 还没有准备好投入生产](https://www.iosapptemplates.com/blog/swiftui/swiftui-drawbacks)
*   [我使用 SwiftUI 的心得](https://link.medium.com/isXKLhaaCib)
# SwiftUI 的可选视图

> 原文：<https://betterprogramming.pub/optional-views-with-swiftui-ab551030296d>

## 更好地处理条件渲染

![](img/bfd98b08b34dac4874cd5f1137cdbb97.png)

丹尼尔·明库克·金在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

我们已经在[之前的文章](https://medium.com/better-programming/swift-optional-views-with-the-proxy-and-abstract-factory-patterns-5f24f8cf84b8)中看到可选视图有多常见。我们还看到，处理可选性的逻辑可以有不同的所有者。最后，我们看到了如何用 Swift + UIKit 实现可重用的可选视图。

这一次，我们将跳过介绍，通过一个例子直接进入如何用 SwiftUI 做同样的事情。

# Go Premium Widget

假设我们有一个[免费增值](https://en.wikipedia.org/wiki/Freemium)应用程序，用户不需要付费使用该应用程序，但我们希望让他们充分了解我们的高级计划，其中包括额外的功能。

我们将在应用程序的不同部分显示一个“Go Premium”小部件，用户可以点击它来启动这个过程。但是，由于显而易见的原因，当用户已经是高级用户时，不应该显示 Go Premium 小部件。

让我们首先以非可选的方式展示 Go Premium 小部件。我们将使用一个简单的`Text`,这样我们就不会迷失在细节中:

# 使小部件成为可选的

为了让这个小部件成为可选的，我们需要做一些改动。

我们首先从`ContainerView`中提取小部件的实例化，并使用一个协议来注入它:

现在我们可以重构`view`属性，根据用户是否已经是高级用户，返回`self`或`EmptyView`。

然而，`view`属性的返回类型有一个问题，因为`SwiftUI.View`不能被用作`PremiumWidget`的抽象。它用的是`Self`，所以不能当铅字用。我们也不能返回`some View`，因为[不透明类型](https://docs.swift.org/swift-book/LanguageGuide/OpaqueTypes.html)不能是协议需求的返回类型。

幸运的是，SwiftUI 至少提供了几种方法来实现这一点。一个是使用一个删除类型的视图 *—* `[AnyView](https://developer.apple.com/documentation/swiftui/anyview)`。另一种方法是使用[组](https://developer.apple.com/documentation/swiftui/group)。有些人声称`AnyView`有性能影响，但是你应该先阅读[这篇文章](https://medium.com/swlh/swiftui-performance-battle-anyview-vs-group-55bf852158df)，然后做出你自己的决定。无论如何，我们将展示如何用`AnyView`来实现，但是你可以随意使用其中任何一个。代码中的差异应该是最小的。

现在，我们有了一个基于用户是否是高级用户的可选小部件，但这里有几个重要问题:

*   `PremiumWidget`正在被实例化。
*   `PremiumWidget`阶层不应该有责任知道什么时候该展示什么时候不该展示。

# 将实例化提取到工厂类

我们将把`PremiumWidget`的实例化转移到一个工厂类，同时考虑到以下前提:

*   因为我们已经提取了`PremiumWidget`的实例化，所以`ContainerView`不应该被修改。
*   我们不想让`ContainerView`知道我们正在使用一个工厂类。

这就是为什么我们要使用[代理模式](https://en.wikipedia.org/wiki/Proxy_pattern)的原因:

由于`PremiumWidget`和`PremiumWidgetFactory`都符合`PremiumWidgetProtocol`，我们可以在`ContainerView`不知道正在使用哪一个的情况下注入其中任何一个。这将允许我们在不改变任何正在使用`PremiumWidget`的容器的情况下移除可选性。

这在 a `PremiumWidget`的具体例子中可能没有太大意义，但在 A/B 测试、可选特性可能变成非可选特性等情况下会有意义。

# 结论

感谢您的阅读。让我知道你的想法。

感谢 [Raul Menezes](https://twitter.com/rmenezes89) 审阅文章并建议使用`EmptyView`而不是 optionals。
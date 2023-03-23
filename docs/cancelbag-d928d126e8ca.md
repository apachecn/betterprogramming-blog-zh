# 用于组合订阅可变处理包

> 原文：<https://betterprogramming.pub/cancelbag-d928d126e8ca>

## 以声明性 SwiftUI 方式收集任何可取消的令牌

![](img/8517cb3952026c808ab0ca57bc06f774.png)

约西亚·维斯在 [Unsplash](https://unsplash.com/s/photos/bag?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

随着在 WWDC2019 上宣布 [SwiftUI](https://developer.apple.com/documentation/swiftui/) 和[组合](https://developer.apple.com/documentation/combine)框架，苹果清楚地勾勒出了其平台软件开发的未来愿景，而这一未来将是*反应式的*。

就像过去发生在 Objective-C、Autolayout 或 Swift 的 ARC 一样，苹果新技术的发布和它的广泛使用之间只有几年的时间。

因此，如果有人仍然不愿意学习函数式反应式编程，现在他们可以选择登上火箭或者留在面向对象 UIKit 的褪色世界中。

然而，苹果已经做了很好的工作，让开发者尽可能无缝地过渡。在其他反应式框架中，Combine 是简单性的杰作。

它提供了一个非常简洁和程序员友好的 API，对于基本的“值随时间的变化”的处理来说足够了。

# 休斯顿，我们有一个问题…

正如您从 API 的简洁性中所期望的那样，总会有人从他们习惯的反应式框架中错过他最喜欢的[花哨操作符](https://rxmarbles.com/)。

我明白。但感觉联合收割机缺少一些基本的东西，建议的替代方案看起来笨重而过时。我说的是订阅的内存管理。

[RxSwift](https://github.com/ReactiveX/RxSwift) 为我们提供 [DisposeBag](https://github.com/ReactiveX/RxSwift/blob/master/RxSwift/Disposables/DisposeBag.swift) 。它的竞争对手 [ReactiveSwift](https://github.com/ReactiveCocoa/ReactiveSwift) ，有一个对应的: [Lifetime](https://github.com/ReactiveCocoa/ReactiveSwift/blob/master/Sources/Lifetime.swift) 。

但是联合呢？订阅返回一个类型为`[AnyCancellable](https://developer.apple.com/documentation/combine/anycancellable)`的令牌，除了将它存储在实例变量中，我们不能将它放在任何地方:

联合收割机到底需不需要一个`CancelBag`？

Michael Long 在他的帖子中提出的一个问题对我来说有一个明确的答案:“是的，联合收割机缺少它。”

**更新:Xcode 11.1 在对`AnyCancellable,`的扩展中添加了方法** `store(in: Set<AnyCancellable>)`，因此代码现在可以这样重构:

尽管这看起来更干净，但行数保持不变。

但是让我们更进一步，实现 [@michaellong](http://twitter.com/michaellong) 提出的[变量](https://medium.com/@michaellong/rxswifty-and-his-variadic-disposebag-1682ecceaf41) `[DisposeBag](https://medium.com/@michaellong/rxswifty-and-his-variadic-disposebag-1682ecceaf41)`。

上面的`collect`函数使用了 Swift 5.1 中新的`[@functionBuilder](https://blog.vihan.org/swift-function-builders/)`属性，该属性允许来自 [SwiftUI](https://developer.apple.com/documentation/swiftui/) 的视图容器(如`VStack`)获取一个没有任何(`,`)分隔符的元素数组。

所以，现在我们可以收集所有的订阅令牌，而无需显式调用`.store(in: &subscriptions)`

关于`CancelBag`实现的全部要点(大约 20 行代码)可以在 GitHub 的[中找到。](https://gist.github.com/nalexn/33f14af1d163ea476ee499c0459824b2)
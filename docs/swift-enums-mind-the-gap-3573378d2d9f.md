# 迅速 Enums，小心差距:EnumKit 救援

> 原文：<https://betterprogramming.pub/swift-enums-mind-the-gap-3573378d2d9f>

## 轻松匹配枚举案例，提取它们的相关值，转换它们，等等。使用 EnumKit

![](img/700cb558cf59dfb7155d85adccb1f796.png)

Swift 中的枚举非常强大。凭借其能够拥有关联值的超能力，enums 为 Swift 用户提供了一个全新的用例类别。

我们使用枚举来表达错误、事件、动作、输入和输出。然而，要访问一个枚举事例的关联值，并不像访问一个结构或类的变量那样简单。

在 MERLin 中，我们使用枚举来描述模块中可能发生的事件。

我邀请您阅读[Events-Driven Architecture in iOS with MERLin](https://medium.com/better-programming/events-driven-architecture-in-ios-with-merlin-63d211e64698)以了解更多关于什么是事件和模块的信息，但是现在，我将只把事件定义为:

系统发送的一种信息，用来通知收听者状态的变化。

描述可在电影详细信息页面上生成的事件的简单枚举

今天，我们知道了两种访问枚举相关值的方法:

*   使用 switch 语句。
*   使用模式匹配。

使用开关或模式匹配并没有错，但有时，在我们的算法中，我们可能对一个特定的情况感兴趣。

只有一个 case 和 default 的开关有点让人心烦，那么模式匹配只适用于 if 和 guard 语句。这两种模式在反应式环境中都是丑陋的，并且可能会经常重复。

现在假设在您的算法中，您对验证事件不是`rated`感兴趣。

这个问题只能用除了排除情况之外的所有情况的开关来解决。在这种用例中，模式匹配变得完全无用，除非您愿意让一个空的 if 包含所有的 else 逻辑，因为它不会返回一个我们可以取反的布尔值。

对于`MovieDetailPageEvent`的具体情况，我们可以使枚举符合`Equatable`并有更好的代码，但这并不总是容易或可能的。

![](img/b4c1cab2872eb96a5cb48609edeef122.png)

[EnumKit](https://github.com/gringoireDM/EnumKit) 是一个非常轻量级的库，旨在使 enum 能够轻松地访问和转换相关值，匹配 enum 事例，并处理 enum 事例集合。

你需要做的就是让你的枚举符合标记协议`CaseAccessible`，它没有一致性要求。

使用`CaseAccessible`，提取一个关联值就像访问一个数组值一样简单:`event[case: MovieDetailPageEvent.rated]`。

由于`CaseAccessible`是一个协议，我们也可以编写扩展来更好地支持其他数据结构中的枚举，如 array、observable (RxSwift)和 publisher (Combine)。

EnumKit 配备了对`Sequence`的扩展，扩展到 enum case 函数的数组，如`compactMap`、`flatMap`、`forEach`和`filter`。

联合收割机的扩展`Publisher`在分支中准备就绪，RxSwift 的扩展在 [RxEnumKit](https://github.com/gringoireDM/RxEnumKit) 中可用。

让我们来看看我们之前用 observable 的例子用 EnumKit 会是什么样子。

EnumKit 还提供了一种通过下标更新关联值的简单机制。

对相关值的访问是 Swift 中一项长期要求的功能。虽然可能需要一点时间来商定将这一特性融入语言的方法，但 EnumKit 现在已经可以在 [CocoaPods](https://cocoapods.org/) 和 [SwiftPM](https://github.com/apple/swift-package-manager) 上使用了。

# 限制

不幸的是，EnumKit 并不完美。虽然它经过了 100%的单元测试，但是在模式匹配和相关值的提取方面存在一些限制，我想在这里说明一下:

*   它不能很好地处理过载的情况。在某些情况下，与同名枚举事例的模式匹配会导致 Xcode 崩溃。
*   关联值不能包含闭包。提取会成功，但是使用以这种方式提取的闭包可能会导致崩溃。

尽管有这些限制，EnumKit 还是极大地改进了我们使用 enums 的方式。如果您正在使用`CaseAccessible`，只要确保您有唯一命名的枚举案例，没有闭包作为参数。
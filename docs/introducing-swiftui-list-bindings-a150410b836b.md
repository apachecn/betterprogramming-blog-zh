# SwiftUI 列表绑定简介

> 原文：<https://betterprogramming.pub/introducing-swiftui-list-bindings-a150410b836b>

## SwiftUI 现在允许在列表中使用项目绑定。看看它是如何在幕后实现的

![](img/4c7d8cf02dbfe4942fae39173e419ed9.png)

图片由作者提供。

列表可能是 iOS 应用中最受欢迎的 UI 元素之一，自`UITableViewController`首次推出以来，我们已经走过了漫长的道路。在 UIKit 中创建列表并不完全是火箭科学，但它确实需要一些仪式。

SwiftUI 让创建列表变得非常简单。看看这个显示待办事项列表的片段:

只用三行代码，我们就能创建一个简单的列表视图。

一旦我们试图使列表行中的项目可编辑，事情就会变得稍微复杂一些。例如，`TextField`需要绑定到我们想要编辑的元素:

然而，直到现在，SwiftUI 还没有提供一种简单的方法来访问集合项的可变绑定。这就是为什么我们最终构建了这样的代码:

这段代码不仅看起来比实际需要复杂得多，而且它还迫使 SwiftUI 重新呈现整个列表，即使我们只更改了列表中的一个元素。这可能会导致缓慢的用户界面更新和闪烁。

从 WWDC 2021 开始，SwiftUI 支持列表元素的绑定。要使用这个特性，我们需要做的就是将集合的绑定传递到列表中，SwiftUI 会将当前元素的绑定传递到闭包中:

这段代码更容易阅读，看起来与我们开始时的原始代码相似。最棒的是:您可以将这段代码回部署到任何支持 SwiftUI 的 iOS 版本中。

# 在幕后

我相信你会欣赏这给你的代码带来的更多的简单性。更简单的代码意味着更少的错误，这也使得它更容易读写。

但是它是如何工作的呢？

嗯，有几件事凑在一起，使这成为可能。让我们首先看看是什么使得在`List`(顺便说一下，还有`ForEach`)中使用绑定成为可能。

让我们跳到`List`初始化器的定义(使用`⌃ + ⌘ + J`或`⌃ + ⌘ + Click`):

快速浏览一下[文档](https://developer.apple.com/documentation/swiftui/list?changes=latest_minor)(打开 API 差异)可以发现这是 12.5 到 13.0b1 的一个新增内容。

让我们解开这个新的初始化签名告诉我们什么:

*   第一个参数(`data: Binding<Data>`)定义了初始化器期望一个在`Data`上通用的绑定(例如，一个数组，就像我们的例子)。
*   第二个参数(`@ViewBuilder rowContent: @escaping (Binding<Data.Element>) -> RowContent`)定义了结尾闭包，我们在其中定义了列表的主体。这个签名向我们展示了闭包将接收一个绑定，这个绑定是数据集合的元素类型的泛型。

这使得我们可以传入一组绑定，并在`List`的主体中一个接一个地接收它们。

但这只是等式的一部分！为什么我们可以使用`$`符号语法？

为了理解这一点，我们需要做更多的 API 考古——或者从从事这方面工作的人那里得到一点提示。谢谢，[荷莉](https://twitter.com/hollyborla)。

[SE-0293](https://github.com/apple/swift-evolution/blob/main/proposals/0293-extend-property-wrappers-to-function-and-closure-parameters.md) (将属性包装器扩展到函数和闭包参数)讨论了将属性包装器扩展到函数和闭包参数。这就是为什么我们能够首先在尾随闭包上使用`Binding`。提案的[关闭部分](https://github.com/apple/swift-evolution/blob/main/proposals/0293-extend-property-wrappers-to-function-and-closure-parameters.md#closures)讨论了是什么使`$`标志成为可能:

> 对于接受投影值的闭包，如果支持属性包装器和投影值具有相同的类型，如 SwiftUI 的属性包装器，则属性包装器属性不是必需的。如果`Binding`实现了`init(projectedValue:)`，它可以用作闭包参数上的属性包装器属性，而无需显式编写该属性:
> 
> 让我们使用 Binding:(Binding<int>)-> Void = { $ value in</int>
> 
> ...
> 
> }”

果不其然，如果我们看看`Binding`的 API 差异，它就在那里:`init(projectedValue: Binding<Value>)`是 Xcode 12.5 到 Xcode 13.0b1 的一个[增加。](https://developer.apple.com/documentation/swiftui/binding/?changes=latest_minor)

现在你知道了:由于在 SE-0293 中所做的工作，我们可以使用`List`和`ForEach`内部的绑定，并向`List`(参见[文档](https://developer.apple.com/documentation/swiftui/list/init(_:rowcontent:)-25c5f?changes=latest_minor))、`ForEach`(参见[文档](https://developer.apple.com/documentation/swiftui/foreach/init(_:content:)-96gx9?changes=latest_minor))和`Binding`(参见[文档](https://developer.apple.com/documentation/swiftui/binding/init(projectedvalue:)?changes=latest_minor))添加各自的初始化器。

正如 [Holly 提到的](https://twitter.com/hollyborla/status/1402386082633633794)，闭包参数的新`$`语法是该语言的一个编译时方面，只要你在用 Swift 5.5 编译，它就能工作。

# 反向展开

这就给我们带来了以下问题:为什么这个可以[回部署](https://twitter.com/luka_bernardi/status/1402363202923491332)到更早的 iOS 版本？闭包参数的新`$`语法只是拼图的一部分，但是我们也有新的 API(额外的初始化器),需要在以前版本的操作系统上提供。

我们如何使新的 API 对早期版本的运行时可用？

Swift repo 上有一整篇文档讨论了[库进化](https://github.com/apple/swift/blob/main/docs/LibraryEvolution.rst#always-emit-into-client)的主题，即“在不破坏源代码或二进制兼容性的情况下更改库的能力”

该文档对`@_alwaysEmitIntoClient`有些含糊，但似乎用该属性注释函数、计算属性或下标将导致代码被发送到客户端二进制文件(即我们的应用程序)，从而使得在 Swift 运行时的先前 ABI 稳定版本上使用新的 API 成为可能。似乎这就是团队为这个特定功能所做的。

如果你想更深入地了解这个话题， [Library Evolution 博客文章](https://swift.org/blog/library-evolution/)提供了更多关于底层概念的细节，以及什么时候启用 Library Evolution 支持是个好主意(什么时候不是)。

# 关闭

我总是觉得很有趣的是，我们经常认为理所当然的语言和框架的看似很小的特性中融入了多少工程学。感谢所有为此付出努力的人！

正如[马特](https://twitter.com/ricketson_)在[swift ui](https://developer.apple.com/videos/play/wwdc2021/10018/)的新功能中提到的(在 7:37 的标记处)，这个特性将帮助我们构建更无错误(和高性能！)应用程序，所以现在是检查您的代码并更新您的所有列表以使用新功能的好时机。

感谢阅读！

*原发布于*[*https://peterfriese . dev*](https://peterfriese.dev/swiftui-list-item-bindings-behind-the-scenes/)*。*
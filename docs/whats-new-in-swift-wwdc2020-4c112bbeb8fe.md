# Swift 在 WWDC 2020 中的新功能

> 原文：<https://betterprogramming.pub/whats-new-in-swift-wwdc2020-4c112bbeb8fe>

## 苹果公司今年发布的所有产品的详细介绍

![](img/4cc3d72680603e67ea478561fad59ac7.png)

# 内部基准:二进制大小

![](img/6c03fd3d82626a9ccc9ff288fcfe1055.png)

为了跟踪进度，我们对 iOS 自带的一个应用进行了快速重写。

在随后的每个版本中，我们缩小了差异。有了 Swift 5.3，我们的代码大小降到了 Objective-C 版本的 1.5 倍以下。

然而，不同风格的应用程序会产生不同的二进制大小。

# MovieSwiftUI 二进制大小

![](img/a0a8aba114d59345370783f9e442f69c.png)

在 Swift 5.3 中，SwiftUI 应用程序的代码大小有了重大改进。我们看到它的应用程序登录代码减少了 40%以上。

现在，二进制文件的大小对于下载时间是至关重要的。但是当你运行应用程序时，它是我们所谓的*干净记忆的一部分。*这是可以清除的内存，因为它可以在需要时重新加载。所以它没有*脏内存、*应用程序在运行时分配和操作的内存那么重要。

# 肮脏的记忆

## 内存布局示例

**物镜-C**

与基于引用类型的语言相比，Swift 使用值类型有一些基本优势。

![](img/a6ec3c07454f6bbd67db12cb847841c7.png)

在 Objective-C 中，对象变量只是指针。所以数组保存了指向模型对象的指针。反过来，这些对象持有指向其属性的指针。您分配的每个对象都有一些开销、性能和内存使用。

这一点非常重要，Objective-C 为微小的 ASCII 字符串提供了一种特殊的小字符串表示，允许将它们存储在指针中，从而节省了分配额外对象的时间。

**雨燕**

![](img/6f42ea2182ee38cefbf1188b249776b5.png)

Swift 对值类型的使用避免了通过指针访问这些值的需要。因此，UUIDs 可以保存在`Mountain`对象中，Swift 的小字符串可以保存更多的字符，多达 15 个代码单元，包括非 ASCII 字符。

![](img/21b21f6d390055a2675915cd82aceacd.png)

最后，所有的`Mountain`对象可以直接在数组存储中分配。所以除了几个字符串之外，所有东西都保存在一个连续的内存块中。像这样使用值类型，Swift 程序可以获得显著的内存优势。

# 堆使用

## *400 个模型对象*

![](img/69632817fa747f22c2638c229a652c08.png)

因此，如果我们检查由 400 个这些模型对象组成的数组所使用的热内存，我们可以看到 Swift 模型数据更加紧凑。

## *模型和语言/库开销*

![](img/5bf935759b28b33e60d704fad1382caa.png)

由于运行时开销，一些 Swift 程序以前仍然使用更多的热内存。以前，Swift 在启动时会创建大量缓存和内存。这些缓存存储了协议一致性和其他类型的信息，以及用于将类型过渡到 Objective-C 的数据。

## *Swift 5.3*

![](img/381a2ea97ec2dcdf16e374ab853d4cf1.png)

所有语言运行时都有一些开销，但是在 Swift 的例子中，开销太大了。在 Swift 5.3 中，这种开销已经被大幅削减，Swift 版本的应用程序现在使用的热内存不到去年版本的三分之一。

为了充分利用这些改进，应用程序的最低部署目标需要设置为 iOS 14。

**降低用户空间堆栈中的 Swift 运行时**

![](img/7c3c03afea903322ab03f17b555637ec.png)

在大多数应用程序中，这些差异通常并不明显，但像这样优化 Swift 的内存对于在苹果系统中进一步推动 Swift 至关重要——能够在 demons 和低级框架中使用它，其中使用的每一个字节的内存都很重要。

Swift 的标准库现在位于基础库之下。这意味着它实际上可以用来实现浮动在 Objective-C 层之下的框架，而以前必须使用 C。

# 诊断学

在这个发布周期中，编译器的错误和警告有了很大的改进。

## Swift 编译器中的新诊断子系统

![](img/9533f651399e96c33cf8c8a5f4a90ed3.png)

Swift 编译器有一种新的诊断策略，可以产生更精确的错误，指出源代码中出现问题的确切位置。有新的试探法用于诊断导致可操作错误的问题原因，并提供了如何修复问题的指导。

![](img/2eccbaec024f6fbcb4f695040f408012.png)

这是一个一年前 Swift 5.1 编译器在 SwiftUI 代码中产生的难以理解的诊断的例子。

![](img/28156989112dd881be0d082ca66517b3.png)

快进到今天，诊断明显更好了，有一个错误告诉你到底是什么问题。

在诊断问题时，编译器还会在内部记录更多关于问题的信息，因此它现在会生成附加注释。

![](img/4e471b2fd85f35f4ea6258fa7f60b0b4.png)

在这种情况下，应用修复自然会引导开发人员为不完整的`TextField`初始化提供缺失的部分。

# 代码完成

这涵盖了从编译器在`SourceKit`中提供的代码完成推理，到 Xcode 代码编辑器中的体验。

## 改进的类型检查推理

![](img/5d26358aa12bc606155d848d63b4e350.png)

首先，候选补全的推断有了显著的改进。这里，当在不完整的字典文字中使用时，编译器推断三元表达式中的值。

## 代码完成:KeyPath 作为函数

![](img/279e22f181f544cc2833188544a6441f.png)

代码完成还为语言的一些更动态的特性提供了您所期望的值，比如使用`KeyPath`作为函数。

## 代码完成速度比 Xcode 11.5 快 15 倍

除了完成结果的质量之外，代码完成性能也得到了极大的提高，在某些情况下提高了 15 倍。

## SwiftUI 代码的代码完成性能

![](img/45959455a2d405b6ab0a9e2bc4cb793b.png)

这对于编辑 SwiftUI 代码尤其有益。

# 代码缩进

Xcode 中的代码缩进也由开源的 SourceKit 引擎提供支持，已经得到了显著的改进。

![](img/e5562021ee0a12fe0b1076e2900a0170.png)![](img/22833caae2f61e86d13cf063c1b435bb.png)

在这里，您可以看到来自开源 MovieSwiftUI 项目的 SwiftUI 代码中的一项改进。之前你有时会得到一些链式访问的自然缩进。

![](img/20630b5bd1e1203004c1b1ea5d96d5e6.png)

但是现在，它们在视觉上完全对齐了。这些和其他改进将对您的编辑体验产生显著影响。

# 排除故障

## 针对运行时故障的更好的错误消息

![](img/60aa50ffc2572d05d2761d8bea351644.png)

当调试信息可用时，调试器现在将显示常见 Swift 运行时故障陷阱的原因，而不只是显示不透明的无效指令崩溃。

## 增强了调试的健壮性

**使用模块进行调试的利弊**

*   LLDB 使用 Clangemodules 来解析类型
*   LLDB 可能会遇到与编译时不同的模块故障

Swift 使用 Clang 模块从 Objective-C 导入 API。为了解析关于类型和变量的信息，LLDB 需要导入在当前调试上下文中可见的所有 Swift 和 Clang 模块。

虽然这些模块文件有大量关于类型的信息，但由于 LLDB 有整个程序及其所有动态库的全局视图，导入 Clang 模块有时会以编译时不会出现的方式失败——例如，当来自不同动态库的搜索过程发生冲突时。

## *使用 Swift 中 Objective-C 类型的 DWARF 调试信息*

*   LLDB 现在可以使用调试信息解析 Swift 中的 Objective-C 类型
*   增加核心调试功能的可靠性，如 Xcode 的变量视图

作为后备，当这种情况发生时，LLDB 现在还可以从 DWARF 调试信息中导入 C 和 Objective-C 类型，以便快速调试。这极大地提高了 Xcode 变量视图和表达式计算器等功能的可靠性。

# 官方 Swift 平台支持

*   苹果平台
*   Ubuntu 16.04，18.04，20.04
*   CentOS 8
*   亚马逊 Linux 2
*   Windows(即将推出！)

随着这些努力，在更多地方使用 Swift 的机会越来越多。

# AWS Lambda 上的 Swift

无服务器功能是客户端应用程序开发人员将其应用程序扩展到云中的一种简单方式。

![](img/3b43b14d17bde267529530e4c9b52754.png)

现在在 Swift 中使用开源的 Swift AWS 运行时很容易做到这一点。

```
import AWSLambdaRuntimeLambda.run { (_, evnet: String, callback) in
	callback(.success("Hello, \(event)"))
}
```

所需的代码量就像写“Hello，World！”

# 语言和图书馆

## 语言

![](img/57478da115620feb134550c31cb4db30.png)

[迅捷进化](http://apple.github.io/swift-evolution)

## 多重结尾结束语法(SE-0279)

```
// Multiple trailing closure syntaxUIView.animate(withDuration: 0.3, animations: {
	self.view.alpha = 0
})⬇️⬇️⬇️UIView.animate(withDuration: 0.3) {
	self.view.alpha = 0
}
```

从一开始，Swift 就支持所谓的尾随闭包语法，当它是一个闭包时，它允许您将方法的最后一个参数弹出括号。

它可以更简洁，嵌套更少，但又不失清晰，使得调用点更容易阅读。

然而，尾随闭包语法仅限于最终闭包的限制限制了它的适用性。

```
UIView.animate(withDuration: 0.3, animations: {
	self.view.alpha = 0
}) { _ in
	self.view.removeFromSuperview()
}
```

在这种情况下，尾随闭包使得代码更难阅读，因为它的作用不明确。更糟糕的是，它将一个调用点的完成块的含义变成了另一个调用点的动画块。

对调用点混淆的担忧导致 Swift 风格指南禁止在一个方法调用(比如这个)有多个闭包参数时使用尾随闭包语法。

```
UIView.animate(withDuration: 0.3, animations: {
	self.view.alpha = 0
}, completion: { _ in
	self.view.removeFromSuperview()
})UIView.animate(withDuration: 0.3) {
	self.view.alpha = 0
}
⬇️
UIView.animate(withDuration: 0.3, animations: {
	self.view.alpha = 0
}) { _ in
	self.view.removeFromSuperview()
}
```

因此，如果我们需要添加一个额外的闭包参数，我们中的许多人会发现自己不得不重新调整代码，这似乎是不必要的。

**SE-0281**

```
UIView.animate(withDuration: 0.3) {
	self.view.alpha = 0
}
⬇️
UIView.animate(withDuration: 0.3) {
	self.view.alpha = 0
} completion: { _ in
	self.view.removeFromSuperview()
}
```

Swift 5.3 的新增功能是多结尾闭包语法。这将尾随闭包语法的好处扩展到了具有多个闭包参数的调用，并且不需要重新配置来追加一个闭包参数。

多结尾闭包语法也非常适合 DSL。SwiftUI 的新`Gauge`视图用于指示某个值相对于某个整体容量的级别。

![](img/655163f63794b3d35812c00de193d812.png)

通过利用多结尾闭包语法，`Gauge`能够优雅地、渐进地揭示 is 定制点。

**API 设计:尾随闭包语法**

```
// API design : trailing closure syntaxextension Collection {
	func prefix(while predicate: (Element) -> Bool) -> SubSequence
}message.body = "Hello WWDC!\n--Kyle"
let summary = message.body.prefix { !$0.isNewline }
assert(summary, "Hello WWDC!"
```

对于 *take* 来说，一个更好的名字可能是类似于 *prefix，*的东西，这表明结果被锚定到集合的开始。

![](img/d94a98aa7affe02790116b842fb28991.png)

重要的是，方法的基本名称阐明了第一个结尾闭包的作用，因为它的标签将被删除，即使它不是方法的第一个参数。

# 作为函数的关键路径表达式(SE-0249)

![](img/b6c45eee9273e8075fc73ce6fa7be6ca.png)

Swift 4.1 引入了智能`KeyPath`:类型表示对属性的未调用引用，可用于获取和设置它们的底层值。

![](img/31273cc0f90452e7b881288aacb12c4c.png)

⬇️

![](img/d6860e61fa18f9ef9d19a690c93af48c.png)

当你设计一个 API 时，如果你希望调用点是一个简单的属性访问，那么`KeyPath`是一个很有吸引力的函数参数的替代品，因为它们更简洁，嵌套更少。

![](img/6cad57fa7a9e400944d11cd6b3cc2385.png)

⬇️

![](img/786a2e8b0063e1e38f16ba40813adc78.png)

你可以使用一个`KeyPath`表达式作为函数。这意味着您可以将一个`KeyPath`参数传递给任何具有匹配签名的函数参数，并且您可以删除过去为了接受`KeyPath`而添加的任何重复声明。

# @main (SE-0281)

基于类型的程序入口点工具。

![](img/9ced2bd78e4e60b84bce6e1440f20619.png)

从 Swift 1.0 开始，你可以在你的应用委托上使用`UIApplicationMain`属性来告诉编译器生成一个运行你的应用的隐式`main.swift`。

在 Swift 5.3 中，这一功能已经普遍化和民主化。

如果你是一个库的作者，只需要在你希望你的用户从其获得入口点的协议或超类上声明一个静态的 main 方法。

![](img/3b00d36e77fb6d0cc8d575e409fdbeb9.png)![](img/fe542281a70fc3e677ef4265e1a268ae.png)![](img/cacfc1c7c3fb6c5bb5c0006e86643d8e.png)

这将使您的用户能够用`@main`标记该类型，并且编译器能够代表他们生成一个隐式的`main.swift`。

这种标准化的委托程序入口点的方式应该使启动和运行变得更加容易，无论您是使用命令行工具还是现有的应用程序。

# 提高闭包中隐含“自我”的可用性(SE-0269)

![](img/8dadf2c72cf2784cd0cfe8233d088ba2.png)

为了减少对潜在保留周期的关注，Swift 要求在转义捕获它的闭包时显式使用`self`。

但是当你被要求在一行中包含许多`self`点时，就会开始觉得有点多余。

在 Swift 5.3 中，如果您在捕获列表中包含了`self`，那么您可以在闭包的主体中省略它。

![](img/a51d1f80fecda056bbdca2a3ca8ac5cd.png)

你仍然需要明确表达你捕捉自我的意图。但是现在，这种明确性的代价只是一个声明。

![](img/1100ec706ac23aa6706274e73d22f52d.png)

然而，有时甚至是单次使用`self`。可能觉得没有必要——就像在 SwiftUI 中,`self`倾向于成为值类型，使得引用循环的可能性大大降低。

![](img/a22900ae97f1b4679d4a1a62ec8791e0.png)

在 Swift 5.3 中，如果`self`是一个 struct 或 enum，你可以在闭包中完全省略它。

# 多模式捕获子句(SE-0276)

![](img/653684ac913ebfbb12c5d69837d3f600.png)

从历史上看，`do` catch 语句没有`switch`语句表达能力强，导致人们求助于`catch`子句中的嵌套开关。

![](img/7575ace0b588145116bedbf24c8e3d34.png)

在 Swift 5.3 中，`catch`子句的语法得到了扩展，提供了`switch`案例的全部功能。这允许您将多克劳斯模式匹配的类型直接展平到`do` `catch`语句中，使其更易于阅读。

# 枚举增强

![](img/375e4a29488ef39b6ef54f818c85110f.png)

自 Swift 4.1 以来，编译器已经能够为各种类型合成等价的和散列的一致性。

但是，有时候，您会遇到使用比较运算符非常方便的情况。

![](img/f0dd374b2304718eea0463839f9406ab.png)

在 Swift 5.3 中，编译器已经学会了如何为合格的枚举类型合成可比较的一致性。

## 将病例枚举为方案见证

![](img/22c56bc1ab6f92d9e8d80b40bafa5d53.png)

在 Swift 5.3 中，enum cases 现在可用于满足`static var`和`static func`协议要求。

# 嵌入式 DSL 增强功能

![](img/a1641cc700066829530a48a4262a6043.png)

这包括集体使用子元素的构建器闭包和基本的控制流语句，如`if` / `else`。

![](img/ebc441be7037438e33aea05ac20b8f18.png)

在 Swift 5.3 中，嵌入式 DSL 已经扩展到支持模式匹配——像`if let`和`switch`这样的控制流语句。

![](img/b1167354d9044fb9a173e227b243bfe9.png)

我有一个主要用户界面的主窗口和一个应用程序设置的偏好窗口。

以前，要像这样在主体的顶层使用 DSL 语法，需要用特定的 builder 属性对其进行标记。

![](img/f8a436ca23b80a9c75517ef567248f8d.png)

在 Swift 5.3 中，将不再需要 builder 属性，因为我们正在教编译器如何从协议要求中推断出它。

# 软件开发工具包(Software Development Kit)

## 浮动 16

![](img/9858e670df965add66875e351a5e9700.png)

Float16 是 IEEE 754 标准浮点格式。Float16 只占用两个字节的内存，而单精度 Float 需要四个字节。

## 苹果档案

![](img/ea5ca006efcc6370f86a75f3a7148c22.png)

一种新的模块化存档格式基于苹果用于提供操作系统更新的久经考验的技术。

## 惯用的 Swift API

![](img/4aaefd54735b396e4b6f089c056ce397.png)

这包括一个 FileStream 构造函数，它利用了另一个新库 [Swift System](https://developer.apple.com/documentation/swift_packages/target/3197895-systemlibrary) 。

## Swift 系统

![](img/d4b01033f63bfe8b7790066f7fa14b95.png)![](img/26324ec578ac9740360d30f2116904da.png)

通过 Darwin overlay 导入的原始弱类型接口可能很挑剔，容易出错。

![](img/ea724e7ccfaa95fc3590121fc9f0f76f.png)

Swift System 使用强类型`RawRepresentable`结构、错误处理、默认参数、名称空间和函数重载等技术包装这些 API，为 SDK 的更惯用的 Swift 系统层奠定了基础。

# OSLog

![](img/11eadf8d60786d30aeb9498329123127.png)

如果您仍然使用`print`作为您的日志解决方案，现在是重新考虑的最佳时机。

# 包装

## 快速数字

![](img/16d6f05fda682fefd61eed7837b2e903.png)

Swift Numerics 的复数与 C 语言的复数在布局上是兼容的，但是更快更准确。

## Swift 参数解析器

![](img/abbc1a1e336c74a72fb9257a98967af5.png)

一个新的用于命令行参数解析的开源 Swift 包。

![](img/b3a878d33f3b0150fb128a4fb319fd2d.png)

## Swift 标准库预览

![](img/ebbb3e301dfef416c0803418310d70fb.png)

现在，您可以将`StandardLibrary` 特性提案的实现作为一个独立的`SwiftPM`包来提供。
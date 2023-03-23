# Swift 5.3 有什么新功能？

> 原文：<https://betterprogramming.pub/whats-new-in-swift-5-3-f5584dd4e4ed>

## 可比枚举、多模式 catch 子句、闭包中不需要 self 等等

![](img/1b9c860dc74b1ed0cbae7a39888e021c.png)

照片由 [Alexandru Acea](https://unsplash.com/@alexacea?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

[![](img/4559de80fb63bfa9a7c3d7a9ceaa962f.png)](https://raulferrer.dev)

Swift 5.3 中的新功能(其发布流程由苹果公司于 3 月 25 日宣布)可以在代码级别(例如，多模式捕捉、多结尾闭包)和 Swift 包管理器的使用(添加二进制依赖项或资源)中找到。在这里，我详细介绍了 Swift 5.3 中的一些新功能(请记住，我们希望在 WWDC20 上看到 Swift 5.3 的新测试版，尽管为了测试它，您可以在[Swift.org](https://swift.org/download/#snapshots)下载它)。

# 枚举的综合“可比”符合性( [SE-0266](https://github.com/apple/swift-evolution/blob/master/proposals/0266-synthesized-comparable-for-enumerations.md) )

由于这个新的增加，我们可以在没有关联值的`enum` s 中采用`Comparable`协议，或者有关联值但它们采用了`Comparable`协议。这样，所实现的是能够使用例如>或<来比较相同`enum`的两种情况。

# 在上下文通用声明中使用“where”

我们希望通过这次修改来消除将`where` 子句附加到只能引用外部泛型参数的成员声明的限制。例如:

用Swift 5.3，可以表达为如下:

# “didSet”语义的重新定义( [SE-0268](https://github.com/apple/swift-evolution/blob/master/proposals/0268-didset-semantics.md) )

在 Swift 5.3 之前，当使用的*在属性中进行设置时，getter 总是被调用来获取该属性的`oldValue`(尽管这个值没有被引用)，从而对性能产生影响。在 Swift 5.3 中，流程的效率已经提高，因为现在如果我们不在`didSet`内部调用`oldValue`，就不会访问它。*

# 如果没有给定参考循环，则消除闭合中的“自我使用”( [SE-0269](https://github.com/apple/swift-evolution/blob/master/proposals/0269-implicit-self-explicit-capture.md) )

直到现在，在闭包内部，显式地使用`self`是必要的，即使引用循环不会发生。例如:

有了Swift 5.3，`self`可以被删除:

# 多模式“捕获”条款( [SE-0276](https://github.com/apple/swift-evolution/blob/master/proposals/0276-multi-pattern-catch-clauses.md) )

到目前为止，Swift 只允许每个`catch`块使用一种错误类型，这使得我们可以减少错误处理中的重复代码。也就是说:

现在有了 Swift 5.3，我们可以将两个错误合并到一个`catch`块中:

# 一种新的“浮动 16”型( [SE-0277](https://github.com/apple/swift-evolution/blob/master/proposals/0277-float16.md)

在 Swift 5.3 中，由于在移动图形、HDR 图像和机器学习中的广泛使用，增加了这种新类型。

# 多个尾随闭包( [SE-0279](https://github.com/apple/swift-evolution/blob/master/proposals/0279-multiple-trailing-closures.md) )

*尾随闭包*是指 Swift 中函数的最后一个参数是一个闭包。在这种情况下，Swift 允许您直接在括号内传递这个闭包，而不是作为参数传递:

在 Swift 5.3 中，这个选项不局限于函数的最后一个参数，所以现在我们必须在函数中添加多个闭包(简单地通过添加新标签):

# Swift 包管理器改进

对于我们这些使用 Swift Package Manager 创建和导入 Swift 包的人来说，Swift 5.3 提供了一些非常有趣的改进:

*   **包经理资源(** [**SE-0271**](https://github.com/apple/swift-evolution/blob/master/proposals/0271-package-manager-resources.md) **)。**现在，Swift 软件包可以整合资源(如图像、音频)。不仅如此，还可以应用某些规则来处理这些资源。
*   **包管理器二进制依赖关系(**[**SE-0272**](https://github.com/apple/swift-evolution/blob/master/proposals/0272-swiftpm-binary-dependencies.md)**)。**您可以使用二进制格式的依赖项，如 Firebase 或 Google Analytics。
*   **条件依赖(**[**SE-0273**](https://github.com/apple/swift-evolution/blob/master/proposals/0273-swiftpm-conditional-target-dependencies.md)**)。**该选项允许根据平台指定添加哪些依赖项。
*   **本地化资源(**[**SE-0278**](https://github.com/apple/swift-evolution/blob/master/proposals/0278-package-manager-localized-resources.md)**)。**除了向 Swift 产品包添加资源的可能性，它们还可以本地化。

# 结论

Swift 5.3 包括代码或 Swift 包管理级别的有趣改进。但也有其他改进值得期待，例如 Swift 5.3 将支持 Windows 等平台和新的 [Linux](https://swift.org/blog/additional-linux-distros/) 发行版。
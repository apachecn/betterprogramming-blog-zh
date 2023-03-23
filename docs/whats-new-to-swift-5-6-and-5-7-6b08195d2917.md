# Swift 5.6 和 5.7 的新功能

> 原文：<https://betterprogramming.pub/whats-new-to-swift-5-6-and-5-7-6b08195d2917>

## 了解 Swift 5.6 和 5.7 的下一步发展

![](img/e93c0c8b43722499526a015e1c3e156c.png)

# Swift 5.6

Swift 5.6 于 2021 年 11 月发布，预计将很快问世。让我们快速浏览一下这门语言的新内容。

## [SE-0290](https://github.com/apple/swift-evolution/blob/main/proposals/0290-negative-availability.md) :不可用关键字

使用新的`#unavailable`关键字编写反向可用性条件的可能性。例如，假设我们的应用程序支持 iOS 13 之前的大量 iOS 版本，我们必须手动加载主窗口。因此，在 Swift 5.6 之前，我们将采取如下措施:

现在，借助 Swift 5.6，我们可以做更具可读性的事情:

## SE-0315 :键入占位符

“类型占位符”,它指导编译器根据通常的类型推断规则来填充类型的该部分。类型占位符拼写为下划线(“`_`”)。例如，让我们定义一个`UIColors`的字典，然后让 5.6 Swift 编译器推断类型。

## [SE-0320](https://github.com/apple/swift-evolution/blob/main/proposals/0320-codingkeyrepresentable.md) :字典编码改进

到目前为止，字典编码令人惊讶地受到限制，只允许使用`Int`或`String`类型作为键。这个提议引入了一个名为`CodingKeyRepresentable`的全新协议，它允许你拥有符合这个协议的任何类型的可编码字典键。在下面的例子中，我创建了一个符合这个协议的自定义类型`ID`，它需要提供一个`codingKey`和一个带有通用编码键的 init。那么对具有类型`ID`的键的字典进行编码就变得简单了。

## [SE-0337](https://github.com/apple/swift-evolution/blob/main/proposals/0337-support-incremental-migration-to-concurrency-checking.md) :支持并发迁移

Swift 5.5 引入了从语言中消除数据竞争的机制，包括`Sendable`协议来指示哪些类型具有可以跨任务和参与者边界安全使用的值。然而，Swift 5.5 并不强制使用`Sendable`，而是可选的，这意味着数据竞争可能会发生。因此，这个建议意味着默认情况下编译器将检查这些可能发生的数据竞争。例如:

## 存在主义`any`

Swift 现在允许使用`any`关键字显式编写存在类型，从而在存在类型和协议一致性约束之间创建了语法区别。在 Swift 5 中，现在任何可以使用存在类型的地方，`any`关键字都可以用来明确表示存在类型:

在 Swift 6 中，存在类型需要用`any`明确拼写:

# Swift 5.7

Swift 5.7 对不透明类型做了一些小的改进，以提高可读性和可见性。让我们快速浏览一下这些变化。

## [SE-0341](https://github.com/apple/swift-evolution/blob/main/proposals/0341-opaque-parameters.md) :不透明功能参数

不透明类型现在可以用在函数和下标的参数中，它们为泛型参数的引入提供了一种简化的语法。这一更改从函数声明中删除了大量样板代码。在这个新变化到来之前，我们发现自己编写了如下的通用函数:

这个提议将不透明结果类型的语法扩展到参数，允许指定通用的函数参数，而不需要与通用参数列表相关联的样板文件。上面的`horizontal`函数可以表示为:

## [SE-0328](https://github.com/apple/swift-evolution/blob/main/proposals/0328-structural-opaque-result-types.md) :结构不透明的结果类型

与上面的非常相似，现在我们可以使用不透明类型作为函数的结果类型，变量的类型，或者下标的结果类型。在所有情况下，不透明结果类型必须是整个类型。例如:

希望你喜欢并发现这篇文章有用。
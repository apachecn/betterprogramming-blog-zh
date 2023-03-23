# 如何在 SwiftUI 中有条件地应用视图修改器

> 原文：<https://betterprogramming.pub/conditionally-applying-view-modifiers-in-swiftui-c5541711eb41>

## 根据某些属性或变量应用视图修改器

![](img/30dd54bc7512483c0843f115cb1121cf.png)

图片来自[苹果开发者](https://developer.apple.com/documentation/swiftui)。

在 SwiftUI 中开发时，有时我们倾向于有条件地应用视图修饰符。例如:

不幸的是，我们不能直接这样做。经过一番搜索和摸索，我找到了一个很好的解决方案，这要感谢这个[堆栈溢出帖子](https://stackoverflow.com/questions/59029560/how-to-apply-modifier-or-view-by-condition)。让我们先看看实现:

首先，我们在类型`View`上创建一个扩展函数。这允许我们在任何类型的视图上调用`conditionalModifier`。我们使用一个[通用函数](https://docs.swift.org/swift-book/LanguageGuide/Generics.html)，因为这个函数可以应用于任何符合`View`的类型。

该函数有两个参数，一个是决定是否应用`transform`(即视图修改器，如`.foregroundColor(...)`)的`condition`。

第`@ViewBuilder`部分可能会让一些人感到困惑。Majid 有一篇关于 SwiftUI 中的 ViewBuilders 的很好的博客文章。简而言之，这允许我们有条件地返回两种不同类型的内容(`transform(self)`和`self`)，而不会将它们的类型擦除到`AnyView`。

函数体非常简单:如果条件为真，则应用`transform`。现在你知道了！您可以像这样使用这个扩展:

我们可以更进一步。假设我们想要在条件为假的情况下应用一个不同的修饰符。根据我们当前的设置，我们必须:

这不是很优雅。为了改善这一点，我们可以在我们的`conditionalModifier`函数中增加一个参数——当条件为假时应用的另一个修饰符闭包:

不算太差！
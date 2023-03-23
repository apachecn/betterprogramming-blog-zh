# WWDC 22 日推特综述:iOS 16 版 SwiftUI 4 亮点

> 原文：<https://betterprogramming.pub/wwdc-22s-twitterati-roundup-highlights-from-swiftui-4-for-ios-16-bc0ab47cde82>

## 通过推特回应新功能

![](img/15625261bd7eb10268913cab60158073.png)

照片由 [Rubaitul Azad](https://unsplash.com/@rubaitulazad?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

现在，WWDC 22 的兴奋已经消退，下面看看一些最好的推文，帮助你从这次活动中了解大多数开发者的公告。

# 一个只有图表的 SwiftUI 框架，可以更好地可视化数据

从 iOS 16 开始，我们现在有了一个图表框架，可以轻松创建带有可定制轴和图例的条形图、线图、面积图、点图或规则图。

了解如何用几乎任何视图注释`PointMark` SwiftUI 图表——在下面的例子中，使用 SF 符号:

# SwiftUI 中颜色的渐变

对于相同颜色的线性渐变，就不必再使用`LinearGradient`构造函数了。保罗展示了如何在`color`上调用`gradient`来直接给你的视图带来效果:

# ImageRenderer API，但带有缩放

我最喜欢的 SwiftUI 新特性是使用`ImageRenderer`将视图导出为图像的能力。

但是你知道如何匹配视图的分辨率吗？幸运的是，有一个`scale`属性可以在 ImageRenderer 实例上调用，如下面由 Daniel 发布的 [tweet 所示:](https://twitter.com/danielsaidi/status/1538081270714052609)

# SwiftUI 列表有了一个新的修饰符

我们可以直接设置可编辑列表选项——比如在新的 init 函数的`edits`参数中滑动以删除或重新排序:

# 更强大的 SwiftUI 上下文菜单

在菜单顶部显示预览的上下文菜单？你猜对了:

# 带有可变颜色的 SF 符号 4

Xcode 14 中内置的 SF 符号搜索是一个受欢迎的新增功能。但新的可变颜色功能提供了很多机会来显示状态驱动的符号……如下面的推文中所示的进度指示器:

# SwiftUI 网格

对网格的现成支持肯定有助于在 SwiftUI 中构建更复杂的布局。就像下面的 tweet 演示(带有 gist 示例)展示了如何使用 SF 符号和时间轴视图的网格。

# SwiftUI 中的新布局协议

为了构建定制的容器视图，SwiftUI 今年发布了一个布局协议。这里有一条推文展示了如何利用它:

# SwiftUI AnyLayout

为了动态地改变布局容器的类型而不重组它的子视图，我们有了一个新的`AnyLayout`结构:

# SwiftUI _CircleLayout(目前是一个私有 API)

想要创建径向等距的视图？这里有一条推文展示了如何使用`_CircleLayout`——目前是私人的。

# 具有智能自动完成功能的 Xcode 14

以前，Xcode 会自动完成一个函数/初始化器的参数标签，即使它已经写好了——这导致了冗余。Xcode 14 可以更有效地处理这一问题，如下文所示:

# SwiftUI 中的本地照片拾取器

# SwiftUI 标签内容

一个`LabeledContent`视图允许你给任何视图添加一个标签——而不用像`HStack` - `VStack`那样嵌套。还有呢？这对可访问性也有好处！

# SwiftUI 重新修复的导航 API

在 iOS 16 中，SwiftUI 弃用了`NavigationView`，转而支持`NavigationStack`。此外，还有一个`NavigationPath`来以编程方式存储和修改路由堆栈:

此外，正如 [Majid 的推文](https://twitter.com/mecid/status/1539936720766046209)所示，建立深度链接和移交是如此容易。

# SwiftUI 空间手势

您现在可以点击一个视图并获得它的位置— `global`或`local`(本地是相对于它的超级视图)。这里有一个用于原型制作的`SpatialTapGesture`的便捷演示:

# SwiftUI 内部阴影

为了给 SwiftUI 视图添加更多效果，我们现在可以利用内部阴影，例如:

```
Circle().fill(.red.gradient.shadow(.inner(radius: 10)))
```

Paul 演示了如何将它与`CoreMotion`一起使用，为您的视图带来 3D 效果:

这为 WWDC 22 的 SwiftUI Twitterati 增添了活力。我迫不及待地想看到我们可以使用新功能构建的令人惊叹的应用程序。
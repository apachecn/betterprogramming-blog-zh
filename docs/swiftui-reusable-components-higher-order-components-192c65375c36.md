# SwiftUI:可重用组件/高阶组件

> 原文：<https://betterprogramming.pub/swiftui-reusable-components-higher-order-components-192c65375c36>

## 如何使用和操作 HOC

![](img/63e10b3f662c721d2fae2ea2db429525.png)

泰勒·拉斯托维奇在 [Unsplash](https://unsplash.com/search/photos/iphone?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

** * Safari 似乎无法显示代码片段，请尝试使用 Chrome***

如果您曾经使用过 React 或 React-Native，那么在重用组件行为和 UI 时，高阶组件是首选的设计模式。

这种模式对 iOS 来说也并不陌生。查看这篇关于在 iOS 上创建定制组件的文章。

# **SwiftUI 的 HOC 语法—正确的方法**

让我们把*真的* 简单点吧。这是我们在 SwiftUI 中创建高阶组件所需的所有代码:

*注意:在初始化器中，我特意将* `*_ childView*` *设置为符合一个闭包类型，该闭包类型返回我们的泛型* `*View*` *，这使得代码更具可读性。或者，也可以设置成这样…* `*_ childView: C*` *，调用* `*ContainerView(Text("Hello!"))*`

## 代码分解

1.  `ContainerView`为我们的`View`声明了一个[通用](https://docs.swift.org/swift-book/LanguageGuide/Generics.html)约束，也称为类型约束语法
2.  我们将泛型存储为名为`childView`的属性
3.  在`body`内我们返回`childview`
4.  简单！已成功创建 HOC！

# 实际例子

让我们严肃起来，如果没有一个好的实际例子，我们就无法真正欣赏 HOC，让我们开始吧！

![](img/bb318e25a6c10e3c1a71b18da421e7b6.png)

## 创建可拖动的特设

如果我们尝试创建一个组件，让每个孩子都有能力变得可拖动，并在释放时弹回到原来的位置，会怎么样？

首先，我们需要创建一个新的视图组件，我们将其命名为`DraggableView` ，它采用了我们之前编写的特殊语法。

其次，我们必须为拖动手势和弹簧动画编写业务逻辑。

我们可以通过传递子组件进行渲染来将`DraggableView`付诸实施！

# 最后的想法！

瞧啊。就这样，我们结束了！我没能找到任何关于为 SwiftUI 创建 HOC 的文章，但是我希望你能像我喜欢写这篇文章一样喜欢读这篇文章！

我还推荐查看这篇关于创建你自己的自定义视图修改器的文章，就像他们的名字一样，他们修改你的视图。

让我知道你的想法！
# 如何在 SwiftUI 中的兄弟视图(和 UIViewRepresentables)之间传递闭包

> 原文：<https://betterprogramming.pub/passing-closures-between-swiftui-sibling-views-and-uiviewrepresentables-1f81a6cf5be6>

## 在组件之间共享状态

![](img/4c2b4c11410b68e091fe32e14b3885ac.png)

由 [Jose Murillo](https://unsplash.com/@jcmu?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

让我们来看下面的场景:您有一个带有两个子视图`ChildView1`和`ChildView2`的`ParentView`。在`ChildView1`上，你有一个按钮可以触发`ChildView2`中的一个动作。

现在点击按钮，我们希望将文本字段中的文本更改为更合适的内容。让我们首先为闭包定义一个 typealias。如果你不知道闭包是什么，它基本上就是一个方法。你可以在文档中读到更多关于闭包[的内容。](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)

让我们将下面的 typealias 添加到我们的`ParentView`声明之上:

```
typealias OnClickHandler = (() -> Void)
```

所以就变成了:

```
typealias OnClickHandler = (() -> Void)

struct ParentView: View {
    ...
}
```

并将其初始化为我们的`ParentView`中的`@State`属性:

```
struct ParentView: View {

   @State var onClick: OnClickHandler = { }
   ...}
```

这里的想法是，在`ParentView`中定义的这个`onClick`是我们唯一的真理来源。我们不希望在调用堆栈的某个地方初始化另一个闭包。我们希望这一点传递给我们双方的孩子。

在按钮所在的`ChildView2`中，我们将它添加为`@Binding`，因为它已经在`ParentView`中初始化，而`ChildView2`此时只对它进行操作。然后我们将它作为动作添加到我们的按钮:

您会注意到，我们删除了旧的闭包，在这个闭包中我们将打印我们的消息，而只是将我们的作为参数传递。这不是强制性的，但是更短更干净。

此时，您的`ParentView`正在通知您，当您初始化`ChildView2`时，您缺少了`onClick`参数，所以让我们添加:

你会注意到我们将`$onClick` 传递给了`ChildView2`，因为我们将属性定义为`@Binding`，所以我们使用`$`来传递绑定而不是值。

我们现在要对我们的`ChildView1`做同样的事情——添加一个绑定属性——但是这一次我们也要编写当点击按钮时被调用的函数，并且我们要将该函数分配给我们传递的闭包:

这里的神奇之处是在文本元素上调用`onAppear` 。这意味着当那个字段出现时(想想 UIKit 中的`viewDidAppear`，我们将运行下面的代码块。在这个代码块中，我们给我们的`onClick`闭包分配了一个函数来修改我们的字符串的值。

如果你想变得更有趣或者你的方法更大，你甚至可以将整个代码块提取到一个不同的方法中，并将其分配给`onClick`:

现在，通过 SwiftUI 和 Combine 的魔力，您成功地链接了两个互不了解的视图。恭喜你！

# **奖金**

“如果我想在没有`onAppear`的地方用一个视图和 UIViewRepresentable 来做这件事怎么办？”

这是一个很好的问题，亚历克斯！

在这种情况下，我们将使用函数:

你可以看到我已经把它发送到一个后台线程。这是因为编译器会在运行时通知我们“在视图更新期间修改状态，这将导致未定义的行为。”

这是苹果的说法，当视图被重绘时，我们在更新状态。

仅此而已。完整代码可在 [GitHub](https://gist.github.com/trusk89/9e9742e5e3b1663d568bc9dc71dcb0c7) 上获得。编码快乐！
# UIKit 动画很混乱——这就是 SwiftUI 可以解决这个问题的原因

> 原文：<https://betterprogramming.pub/uikit-animations-are-messy-heres-why-swiftui-may-fix-it-ad1303cd0003>

## UIKit 中的动画是可行的，但是 SwiftUI 可以使这个过程更加方便

![](img/d55034580ad6988313f22041ea3c3ea8.png)

里卡多·维亚纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

iOS 动画不仅感觉和外观都很棒，而且非常容易创建。所以我怎么好意思说他们“乱七八糟”呢？

为了理解这一点，让我们回到 iOS 开发的早期——那时我们用 Objective C 编写代码，Xcode 是一个有多个窗口和没有标签的 IDE(实际上，Xcode 3 是垃圾)——并尝试了解从那时起 UIKit 发生了什么。

# 你记得承诺吗？

一些`UIView`属性是动画的——背景颜色、框架、居中等等。

要制作一个视图的动画，你所要做的就是修改它的属性并用`beginAnimations`和`commitAnimations`函数包装它:

```
UIView.beginAnimations(nil, context: nil)UIView.setAnimationDuration(0.2)view.frame = newFrameUIView.commitAnimations()
```

在上面的例子中，视图帧被动画化为`newFrame`。

你不能再简单了，是吗？

这个例子的主要问题是很难将这个动画与屏幕流的其余部分同步。我们必须使用一个委托函数在动画结束时采取行动(说到古代历史，没有块或闭包)。

换句话说，开发人员需要一个完成块来将动画链接在一起或更好地控制动画事件。

幸运的是，在 iOS 4 中，苹果给了我们一个新功能，叫做`UIView`基于块的动画。

# UIView 基于块的动画

有了`UIView`基于块的动画，设置动画、链接动画或与 UI 流的其他部分同步变得非常容易:

```
UIView.animate(withDuration: 0.2) {
 	self.view.frame = newFrame
 } completion: { finished in
    self.doSomethingAtTheEnd()
}
```

这不是很可爱吗？

后来，苹果增加了关键帧动画来帮助我们在应用程序中创建更复杂的动画。

但是后来苹果推出了自动布局。

# 自动布局

在 iOS 6 中，苹果增加了自动布局，基于块的动画变得更加复杂。

在自动布局中，我们不改变视图框架，而是改变它的一个(或多个)自动布局约束。自动布局系统基于一组被优先化的其他约束来处理框架变化。

看看下面的代码片段:

```
UIView.animate(withDuration: 0.2) {
   self.leadingConstraint.constant = 50
}
```

与上一个改变帧的例子不同，运行这个例子不会提供任何动画。原因是约束中的变化会导致父视图中的其他变化，因此它们将仅在接下来的 UI 呈现周期中应用。

那么，解决办法是什么呢？在动画块内部调用`layoutIfNeeded()`即可:

```
UIView.animate(withDuration: 0.2) {
    self.leadingConstraint.constant = 50
    self.layoutIfNeeded()
}
```

简单吧？没那么快。哪个视图需要刷新其布局？动画视图还是其父视图？

因为动画视图影响它的兄弟，我们需要重新布局父视图。

自动布局是一个约束系统，一个约束的更改可能会影响多个视图。因此，我们需要考虑父约束的改变可能会有副作用。Errggg！

# 核心动画层和自动布局

但这并没有结束。

每个`UIView`都基于一个`CALayer`(代表核心动画层)。

每个`UIView`都有一个主层，我们可以在主层上添加子层(就像子视图机制一样)。

核心动画层允许我们为视图添加形状、渐变、阴影和更多效果。

主`CALayer`边界总是等于它的`UIView`边界。但是子层的帧是静态的，我们需要不断更新它们以保持它们与我们的`UIView`同步。

一种方法是覆盖系统在每个 UI 刷新周期中调用的`layoutSubviews()`方法(如果需要的话)。

```
override func layoutSubviews() {
    super.layoutSubviews()
    sublayer.frame = self.bounds
}
```

所以，一方面我们在动画块内部调用`layoutIfNeeded()`。另一方面，我们在`layoutSubviews()`中更新`CALayer`帧。

感觉有点别扭，但是应该管用，不是吗？

嗯，没有。

似乎核心动画层和自动布局不能很好地协同工作。自动布局仅与`UIViews`相关。

如果我们想给用户界面添加一个独特的层，最好是基于该层创建一个`UIView`并将其添加到视图层次结构中。

换句话说，忘记正在被很好地转换的`CALayers`的层次结构。创建一个专用的`UIView`并将其连接到自动布局系统。

# 为什么我们需要动画？

在这一点上，我们应该已经理解了 UIKit 动画的一些不正常的工作。自动布局不能很好地适应，而且`CALayer`问题让我们头疼。

所以，我们需要问自己:动画的目的到底是什么？

毕竟，我们不是在经营皮克斯或迪士尼工作室。

动画不仅仅是为了好看。它们还提供了一种向用户反映 UI 状态变化的好方法。

事实上，大多数动画就是这样做的——向用户表达屏幕上发生了什么变化。

如果一个新的屏幕从底部弹出，应该是有原因的。屏幕之间的转换为用户提供了他们从哪里来以及应该返回哪里的上下文。

SwiftUI 作为一个声明式框架，自然地处理状态变化，并负责响应数据更新、按钮按下等等。

一旦我们用动画包装了这些状态变化，我们实际上解决了我刚才提到的关于 UIKit 的大部分陷阱。

SwiftUI 等声明式框架似乎与动画齐头并进，可以快速实现。

# 如果我不用 SwiftUI 呢？

尽管 UIKit 不是一个声明式框架，但我们可以从 SwiftUI 借鉴一些原则或思想。

我们可以使用视图模型创建自己的状态，并在每次状态更新时“刷新”屏幕。

这意味着将会有一个地方并且只有一个地方所有的约束或显示的数据被修改。

一旦我们用动画块包装了“刷新”代码，我们可能会获得类似于 SwiftUI 中存在的用户体验——UI 和状态之间的直接匹配，动画效果很好。

# 摘要

我知道 UIKit 中的动画对于一些开发者来说似乎非常方便，并且可能被认为是自奥利奥冰淇淋以来人类发生的最好的事情。

我个人觉得很乱，和其他 iOS 库比如自动布局或者核心图形不同步。

我认为 SwiftUI 解决了这些问题中的大部分，即使它没有解决，它也可以教会我们如何在我们当前的 UIKit 代码库中更好地处理它。
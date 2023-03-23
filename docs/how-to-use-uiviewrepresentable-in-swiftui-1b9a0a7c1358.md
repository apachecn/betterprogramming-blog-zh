# 如何在 SwiftUI 中使用 UIViewRepresentable

> 原文：<https://betterprogramming.pub/how-to-use-uiviewrepresentable-in-swiftui-1b9a0a7c1358>

## 将 UIKit 的所有手势添加到您的 SwiftUI 库中

![](img/72e79eb6c797f5e41df36997b6fe4896.png)

照片由[戴恩托普金](https://unsplash.com/@dtopkin1?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

我喜欢 SwiftUI。这是 Swift 语言的一大补充。但是我一直听到开发者说它还没有准备好迎接黄金时代，我不得不同意这一点。也就是说，我有一种挥之不去的感觉，也许在这方面我可以做得更多。

在这篇短文中，我将尝试这样做。我打算通过`UIViewRepresentable`在 SwiftUI 中实现几个 UIKit 手势。对于那些想要添加更多 UIKit 的按钮、旋钮和挂钩的广泛库的人来说，这也应该是一个很好的参考/模板，可以这么说，这是 SwiftUI 中所缺少的。

让我们开始吧。UIKit 总共有七种手势。SwiftUI 有五个。可以说，最重要的是点击、长按、旋转、拖动和放大(你可以自定义放大，也可以捏造其他的，但我不想重写 iOS 库)。如果我们已经在 UIKit 中得到它，让我们使用它。UIKit 具有点击、挤压、旋转、平移、滑动、屏幕边缘平移和长按功能。其中一些的实现相当微妙。

在 UIKit 下，swipe 可以理解你拿着设备的方式，并获得正确的方式，因此向左滑动始终是向左滑动。尽量用拖来实现。你可以，但是你需要更多的代码来考虑手持设备的方式。这是一个在 UIKit 中完整实现与在 SwiftUI 中部分实现的比较突出的例子。

不管怎样，让我们开始吧。我们从一个结构开始，当然，还有你在`UIViewRepresentable`中需要的两个方法:

在第一个函数(`makeView`)中，我们将定义我们的手势。在第二个(`updateUIView`)内，如果需要的话我们会刷新它们。我们已经定义了一个视图，我称之为`v`。

接下来，我们需要定义我们将调用的方法，以将手势数据传递回 SwiftUI:

好了，我们都准备好了。现在，我们所需要做的就是添加一些手势和那个无处不在的框架组合起来。在`makeView`中，我们可以定义几个滑动手势:

然后我们用同样的方法添加到视图中，当然:

```
v.addGestureRecognizer(leftSwipe)
v.addGestureRecognizer(rightSwipe)
```

以及我之前定义的`Coordinator()`类的目标函数:

最后，SwiftUI 代码将所有东西整合在一起:

现在你有了:让 UIKit 直接与 SwiftUI 对话的快速实现指南。这里有一个要点，将所有这一切结合在一起。还有一点代码，但没什么重要的:

# 结论

如果你喜欢读这篇文章并想知道更多，我需要给你介绍我的同事 Anupam Chugh 关于同一主题的优秀文章。这是这首曲子的高级版本。
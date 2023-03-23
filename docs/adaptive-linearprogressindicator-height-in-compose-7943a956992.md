# 构图中自适应线性渐进指示器高度

> 原文：<https://betterprogramming.pub/adaptive-linearprogressindicator-height-in-compose-7943a956992>

## 将您的可组合文件渲染为您想要的大小

![](img/f26d40566a5eeccdc7302556afdf5bcc.png)

照片由 [Sid Balachandran](https://unsplash.com/@itookthose?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当使用 Jetpack Compose 构建组件时，您可以按照您想要的方式渲染它们，这要归功于无数的修改器属性。

大多数时候，您可以通过覆盖这个参数来定制任何组件——您也应该总是提供一个，尤其是在构建 SDK 的时候。不过，有争议的是，一些可组合组件带有一组更严格的规则。`LinearProgressIndicator`就是这种情况。

# `LinearProgressIndicator`用例

原生 SDK 公开了一个名为`LinearProgressIndicator`的可组合组件，它显示一个水平进度条。为了配合[材料导轨](https://material.io/develop/ios/components/progress-indicators/progress-views)，高度配合`LinearIndicatorHeight`(相当于`4.dp`)。

下面是 Composable 的实现(在我写这篇文章的时候，使用 Compose 1.2.1):

您可能会注意到，可组合组件在内部用固定值覆盖了它的大小。

当然，这并不妨碍你去改变它！因为 Composable 自然地公开了一个`Modifier`参数，所以您可以提供自己的大小。但是，如果你的尺寸适合另一个组件的尺寸呢？

是时候看看想要的输出了。我们想画一个进度条，覆盖一个按钮的全部内容。

![](img/7e4d731947a9bfff13d123db68e73aa2.png)

如果我们分解这个可组合的，我们有:

*   答`Button`
*   答`LinearProgressIndicator`
*   一个封装了`Button`和`LinearProgressIndicator`的`Box`

我不会覆盖`Button`中的文本和图标，因为它们不相关。

这就引出了这段代码:

如果您试一试，您会注意到进度条:

*   仍然有一个`4.dp`高度，因此没有填充按钮。
*   不会在圆角处被夹住。试试红色，你会马上看到它。

我们可以很容易地解决第二个问题。一种方法是将造型委托给`Box`而不是`Button`，然后裁剪其内容。

```
val shape = *RoundedCornerShape*(8.*dp*)
*Box*(
    modifier = modifier
        .*background*(
            color = MaterialTheme.colors.primary,
            shape = shape,
        )
        .*clip*(shape),
)
```

让我们进入最有趣的部分。我们可以传递一个硬编码的值来改变进度条的高度。但是我们希望它能动态匹配容器的高度，不管里面装的是什么。

# 调整`LinearProgressIndicator`的大小

首先，有几种方法可以解决这个问题。您可以通过使用一个`Layout`并按照您想要的方式调整大小来深入到定制组件的艺术中。在大多数情况下，这是更好的选择，因为它带来了最灵活的模式来绘制组件。

另外，你可以决定把`LinearProgressIndicator`丢在`Canvas`上，自己画出来！因为这个进度条是为了匹配材质指南，所以最好不要进一步调整它。这将是在`Canvas`中绘制的最简单的形状之一。你所需要做的就是自己处理动画部分。

现在，为了这个例子，我们仍将使用我们的`LinearProgressIndicator`并通过几行额外的代码获得想要的结果。我们需要检索`Button`高度并将其传递给`LinearProgressIndicator`。我们可以利用另一个有用的叫做`onSizeChanged()`的`Modifier`。这个 lambda 在被重绘为参数时会传递它的大小。收到后，在`Dp`中转换它，然后记住它，以便我们的`LinearProgressIndicator`用它的新高度重新组合。

为了完整起见，下面是可组合的最终代码:

仅此而已！这种简单的技术允许您根据其他组件大小来调整组件的大小。请注意，您可以对任何类型的组件重复使用这种模式，尽管大多数时候，系统应该根据您的需要使用`Modifier`属性来推断它们的大小。
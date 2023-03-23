# 在 UIView 中绘图

> 原文：<https://betterprogramming.pub/drawing-in-an-uiview-4bc754483242>

## 如何用手指绘画并导出图像

![](img/ba98c9fbb0a21bb0fd0e78f6f390416d.png)

图片由来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1445053) 的 [Aline Ponce](https://pixabay.com/users/ponce_photography-2473530/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1445053) 拍摄

在这篇短文中，我们将探索如何在我们的应用程序中添加一个视图，用户可以用手指在其中绘图。

创建一个绘图是非常容易的。首先，我们将看到如何获得触摸和跟踪运动的位置。下一步是在`CGContext`中连接这些点，得到我们的图。最后，我们将看到如何将创建的绘图重置并导出为`UIImage`。

# **获取触摸位置**

我们从跟踪视图内部的触摸开始。

`// 1` —首先，我们创建一个可重用的`UIView`子类来包装绘图功能。在这个类中，我们有一个包含`CGPoint`的数组。外部数组表示线，其中每条线由多个点组成。

`// 2` —由于这个类是`UIView`的子类，我们可以覆盖方法`touchesBegan(_:with:)`和`touchesMoved(_:with:)`，这允许我们获得触摸的位置。这些方法可以访问一组触摸，从中我们可以获得第一个触摸点，并将其转换到该视图的坐标系中。

`// 3` —现在我们有了自己的观点，我们需要将它添加到`lineArray`。因为`touchesBegan`标志着一个新行的开始，我们首先向它添加一个空数组`CGPoint`。接下来，我们将第一个点添加到这条新线上。

`// 4` —以下所有点将在`touchesMoved`中被跟踪。就像之前一样，我们访问当前点并将其添加到数组中。最后，通过调用`setNeedsDisplay()`，我们触发了视图的重绘。

我们将在下一个方法中看到如何画出我们的点。

# 划清界限

下一步是画出我们的观点。

`// 1` —为了绘制点，我们需要覆盖方法`draw(_:)`。在这里，我们通过调用`UIGraphicsGetCurrentContext()`来访问当前的`CGContext`。这样的上下文定义了一个绘图目的地，并保存了一个参数来呈现给定的点，比如颜色和线宽。

该上下文将被传递给另一个方法，该方法将进行实际的绘制。一旦我们看到如何将图形导出到`UIImage`，我们为什么使用单独的方法就清楚了。

`// 2` —在`draw(inContext:)`中，我们从配置给定的上下文开始。我们将它的线宽设置为 5，颜色设置为黑色，通过将线帽设置为`.round`来使线的两端变圆。当然，你可以使用任何你喜欢的配置！

`// 3` —接下来，我们开始实际绘制上下文。为了做到这一点，我们迭代了线的数组。

`// 4` —对于每一行，我们获取第一个点，并通过调用`context.move(to:)`将这个点设置为我们的起点。

`// 5` —现在我们用`context.addLine(to:)`给后面的每个点加一条线，用`context.strokePath()`画线。

# 重置和导出图形

最后，让我们看看如何清除绘图或将其导出为图像。

`// 1` —删除图纸就像重置我们的`lineArray`并调用`setNeedsDisplay`一样简单。这将触发我们的点的一个新的渲染，但是因为没有更多的了，视图将是空的。

`// 2` —要导出我们的绘图，首先，我们需要创建一个与视图本身大小相同的新`UIGraphicsBeginImageContext`。这也将把当前上下文设置为新创建的上下文，我们通过调用`UIGraphicsGetCurrentContext`来访问它。

我们可以重用我们的`draw(inContext:)`方法，但是这一次，我们传入新创建的图像上下文。

`// 4` —最后，我们通过调用`UIGraphicsGetImageFromCurrentImageContext()`获得图像，这将使用我们的绘图创建一个`UIImage`。

# 后续步骤

现在我们已经有了一个基本的视图，您可以继续通过添加颜色或使线条可配置来扩展它。

这里有一个由罗恩·克利弗在[raywenderlich.com](https://www.raywenderlich.com)做的[很棒的教程](https://www.raywenderlich.com/5895-uikit-drawing-tutorial-how-to-make-a-simple-drawing-app)，他不仅增加了这些功能，还展示了使用多个`UIImageViews`的另一种绘图方式。

[](https://www.raywenderlich.com/5895-uikit-drawing-tutorial-how-to-make-a-simple-drawing-app) [## UIKit 画图教程:如何制作简单的画图 App

### 了解如何使用 UIKit 绘图 API 在 Swift 中创建简单的绘图应用程序。版本 Swift 4.2，iOS 12，Xcode 10 更新…

www.raywenderlich.com](https://www.raywenderlich.com/5895-uikit-drawing-tutorial-how-to-make-a-simple-drawing-app)
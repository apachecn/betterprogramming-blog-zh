# 在 SwiftUI 中使用 AnimatablePair 制作形状动画

> 原文：<https://betterprogramming.pub/animating-shapes-using-animationpair-in-swiftui-cedbb227e4c6>

## 将形状从正方形转换为三角形，然后再转换回来

![](img/9294dcd58bea0ad38ce85978d9bb493f.png)

由 [Delila Ziebart](https://unsplash.com/@delilaziebart?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# 形状

我们可以从使用类型为`Shape`的结构和绘制并返回正方形的路径函数来制作正方形开始:

![](img/6074f5963c038886b6ceb82c6f9a810e.png)

在我们的`ContentView`中，我们可以在一个`GeometryReader`中添加我们的形状。这将允许我们给我们的形状一个框架，这取决于我们的屏幕宽度。这将使我们的形状在肖像模式下的所有设备上看起来都一样。为了让我们的形状在风景模式下看起来更好，你可以使用`min`功能来查看宽度和高度哪个更小，然后使用最低值来调整框架。

![](img/8325c9605aa5e6747da8dabbcc1e33d8.png)

当你运行代码时，你会发现我们的正方形。如果你想给正方形添加一个笔画，你可能会得到一个编译器错误(`“Value of type ‘some View’ has no member ‘stroke.’”`)为了避免这个错误，我们可以用一个笔画覆盖一个相同的形状:

![](img/67d6c63c98c27391a5dda0b6eab44dc2.png)

# `AnimatablePair`

现在我们可以将下面的代码片段添加到我们的形状中。这段代码将允许我们的形状通过逐渐改变`divideByValue`和`multiplyByValue`的值来制作动画。

例如，如果 *x* =1，那么当我们将它的值改为 *x* = 0 时，该值将逐渐变化为 0.9→0.8→0.7→0.6……→0.0。

![](img/ce11020a827b1f49a8a9a249469beb27.png)

在我们的`ContentView`中声明两个状态变量，将它们传递给我们的形状。使`divideByValue` = 1，使`multiplyByValue` = 0。

![](img/3b3f5405ba2f02928ad0e29bd8496ac4.png)![](img/79a77115dafd287ed46a3e1e2646b192.png)

# 动画

我们为正方形添加的前两条路径线中的 *x* 值如下:

```
path.addLine(to: CGPoint(**x: 0**, y: rect.minY))
path.addLine(to: CGPoint(**x: rect.maxX**, y: rect.minY))
```

要将我们的正方形形状改为三角形，我们需要将 *x* 值改为`rect.maxX/2`:

```
path.addLine(to: CGPoint(x: (rect.maxX / 2), y: rect.minY))
path.addLine(to: CGPoint(x: (rect.maxX / 2), y: rect.minY))
```

我们第一行的 *x* 需要在 *x* = 0 和 *x* = `rect.maxX /2` 之间切换才能做出两种形状，我们第二行的 *x* 值需要从 *x* = `rect.maxX`变为`rect.maxX /2`。

用下面的代码替换我们的路径函数。我们将让`divideByValue`在 1 和 2 之间切换，让`multiplyByValue`在 0 和 1 之间切换:

![](img/beef5f7a27b4629f90e3b441e44f2e25.png)

现在，我们添加一个按钮，它将改变我们在`ContentView`中声明的状态变量的值。按钮使用我们的`multiplyByValue`状态值在`Square`和`Triangle`之间切换文本:

![](img/51b00ed8cc44813601731336b0021700.png)

由于`multiplyByValue`在`withAnimation`里面，这将使我们的文本动画。我们不希望这种情况发生，因为它会在切换到单词`Triangle`之前显示`Tria…`。为了避免这种情况，我们可以声明一个布尔变量，并让它在按钮内的`withAnimation`之前改变它的值:

![](img/60a9d76e99f5353ae2203ec6e1b70bcc.png)![](img/573dded51080b6dd83326475fafbbb6e.png)

全部完成！您可以运行代码并查看形状动画:

![](img/f7e24da3e952e9b671f9587b931ae75a.png)

使用`AnimatablePair`的形状动画示例
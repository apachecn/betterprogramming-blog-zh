# 用喷气背包作曲的摇摆效果艺术

> 原文：<https://betterprogramming.pub/the-art-of-swing-effect-with-jetpack-compose-e6f4c8cb0d88>

## 学习如何用锚点旋转物体

![](img/7e01f5d040ffca91bc7e2b521ece885e.png)

照片由 [Remy_Loz](https://unsplash.com/@remyloz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Compose 已经走过了一段挑战如何构建声明式 UI 的漫长道路。而动画对象也不差。该 API 提供了一个统一的工具包，让你润色你的效果。

话虽如此，重新发现一个新的 API 可能会令人沮丧。当你试图重现一个你知道如何用以前的动画 API 表现的效果时，你会遇到困难。这就是我试图像钟摆一样摆动一个物体时发生的事情。以下是我想要实现的目标:

![](img/d2ded4e21666be659e38df0d501cff89.png)

摇摆效应

乍一看，动画看起来像从一个角度到另一个角度的无限来回旋转。没什么特别的，对吧？让我们看看进展如何！

# 构建可组合的

在实现上述效果之前，让我们构建我们想要摆动的可组合组件。为了举例，我将不再赘述箭头或旋转形状的内部。

对于这个组合，我们需要两样东西:我们要旋转的形状和固定它的钉子。鉴于其简单性，我们可以对两个组件都使用一个`Surface`——使用一个`Canvas`也是非常好的。我们可以将`Panel`和`Nail`组件封装在一个`Box`中，用`TopCenter`对齐来放置钉子。

摇摆可组合

结果应该是这样的:

![](img/7b3614ba2bd9bead0267aa5829c964bc.png)

为了旋转我们的`Panel`组件，我们必须提供一个动态角度。由于我们希望形状无限摆动，我们可以利用带有`rememberInfiniteTransition`的转换 API。然后，我们通过改变其从起点`angleOffset`到最终目的地的值来更新角度——出于对称的目的，我使用了它的负对应物，但您可以选择其他方式。

对于动画部分本身，我使用了一个`tween`插值。Compose 附带了许多内置动画来实现所需的效果。我强烈建议你看一看他们的[全面覆盖的文档](https://developer.android.com/jetpack/compose/animation)。

最后，我们使用`rotate`方法将这个角度应用到`Panel`的`Modifier`上。以下是更新后的`Panel`代码:

它的输出:

![](img/4565985bdbeb89c8044968ee54d4e844.png)

自身旋转的面板

不是我们所期待的，对吧？钉子看起来不像是在钉嵌板。这背后有一个简单的原因:旋转锚定在面板的**中心**。我们需要把它的原点转移到它的定位点:钉子。

# 移动变换原点

在 Compose 之前，改变旋转变换的锚点没什么大不了的。例如，`[RotateAnimation](https://developer.android.com/reference/android/view/animation/RotateAnimation)`允许您直接在其构造函数中更改透视:

```
public RotateAnimation (
    float fromDegrees, 
    float toDegrees, 
    float pivotX, 
    float pivotY
)
```

使用 Compose，`rotate`方法不允许您更改它的轴心，也不允许您更改任何其他看似相关的修饰符。直到你找到它:`transformOrigin`。

首先要理解的是，所有修饰符转换方法都将层操作包装在一个可组合对象上。`rotate`方法对此并不陌生:

```
@Stable
fun Modifier.rotate(degrees: Float) =
    if (degrees != 0f) *graphicsLayer*(rotationZ = degrees) else this
```

它用给定的角度修改层的`rotationZ`属性。这暗示我们，改变支点将来自于修改一个`graphicLayer`属性。

`graphicLayer`修改器有一个默认为`TransformOrigin.Center`的`transformOrigin`属性。如果仔细观察它的实现，就会发现它有两个属性:`pivotFractionX`和`pivotFractionY`。通过改变原点，我们可以将它定向到我们的钉子位置。

给定钉子的位置，这意味着水平居中，几乎在 y 轴的顶部。然后我们将旋转直接应用到`rotationZ`属性上。

它会产生以下效果:

![](img/27462306447ac43f7b916dc77f2943d6.png)

这正是我们想要做的！如果像我们在介绍性动画中看到的那样，元素已经旋转了呢？

我们首先需要旋转我们的`Swing`组件:

注意`Box`的校准已经变为`TopStart`。

我们还调整了我们的变换原点，因为我们通过旋转`Box`改变了我们的参照。

它给出了以下动画:

![](img/825317813494c1650588d0dab07786a9.png)

# 转变的艺术

改变动画轴心并不仅仅包括旋转。所有的转换都可以从这个属性中受益。你应该意识到它的存在及其效用。这对你未来的动画可能会派上用场。

重新发现一个新的 API 有时会令人沮丧。不过，要有信心，作曲团队会帮你的。如果你觉得 Compose 遗漏了什么，我建议你加入 Kotlin Slack 频道。成员互相支持，使社区成长。有一个专门的房间用来讨论与写作相关的话题。这就是我来寻求帮助的地方——感谢[罗曼·盖伊](https://medium.com/u/c967b7e51f8b?source=post_page-----e6f4c8cb0d88--------------------------------)指引我找到了解决方案。

最后，以上所有内容都应该与 Compose Multiplatform 一起工作——在这段代码中没有任何特定于 Android 的内容。

更多文章即将推出！编码快乐！
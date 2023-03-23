# 带有动画完成回调和动画修改器

> 原文：<https://betterprogramming.pub/withanimation-completion-callback-with-animatable-modifiers-2df8a657cbfd>

## SwiftUI 中缺少的方法

![](img/196bf84d73518220ba141e625a7424d6.png)

科尔顿·迪安·马歇尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

SwiftUI 在动画方面非常棒，因为它用`withAnimation`和`animation(...)`这样的方法为你做了很多事情。你可以简单地传递你想让它动起来的东西，SwiftUI 会确保你的视图从一种状态平滑地移动到另一种状态。

然而，有时你会希望拥有和你习惯的 UIKit 一样的功能，允许你在动画完成后更新状态。当你在寻找动画完成后获得回调的方法时，你会意识到这并不简单。事实上，动画完成处理程序没有内置的 API。

自定义的`AnimatableModifier`实现允许我们在特定属性的动画完成后获得回调。在大多数情况下，这足以构建所需的实现。

# 一个示例实现

为了理解它是如何工作的，我们将从一个简单的示例实现开始，其中我们将一个简单的文本标签的不透明度制作成动画。您可以想象这是您的介绍动画，之后您想要触发导航到不同的视图:

该视图包含我们的介绍标签，该标签的动画在`onAppear`回调中触发。我们使用一个`introTextOpacity`值在动画中轻松地将不透明度从 0 改变到 1。

该代码还包含我们的最终实现，用于在`introTextOpacity`属性动画完成时获得回调。每当此属性的动画结束时，都会调用回调。这是通过定制实现`AnimatableModifier`协议实现的。

# 使用动画修改器触发动画完成

为了在`withAnimation`触发的动画完成时获得回调，我们必须实现`AnimatableModifier`协议的自定义实现。

`AnimatableModifier`协议要求实现`ViewModifier`和`Animatable`协议，并且可以调整视图的动画方式。它还要求我们通过`animatableData`属性返回动画数据，我们将用它来验证动画是否完成。

为了理解这是如何工作的，我将分享动画修改器的最终代码实现(我将在后面解释):

`AnimationCompletionObserverModifier`采用符合`VectorArithmetic`协议的通用动画属性。需要这种类型来确保输入实际上是可动画化的，并且我们没有观察到永远不会动画化的东西。

我们的身体只是回到了最初的视角。这是因为我们不是真的在制作动画，而是在观察一个属性的动画。

这可能很难理解，所以让我们一步一步地分解它:

1.  当视图初始化时，动画修改器被实例化。
2.  它将我们的动画属性作为输入，该属性最初被设置为初始值。在我们的例子中，这将是 0 的不透明度值。
3.  当动画开始时，我们的动画修改器将被结果值实例化。在我们的例子中，这是 1。
4.  此时`animatableData`和`targetValue`都被设置为 1，但是`didSet`回调不是由`init`方法触发的。换句话说，我们的`notifyCompletionIfFinished`还没有调用。
5.  一旦动画出现，动画数据属性将更新为旧值。在我们的例子中，这意味着它被更新为 0。这也将触发`didSet`和`notifyCompletionIfFinished`方法，这将导致负匹配。
6.  `notifyCompletionIfFinished`方法继续验证输入值是否与我们预期的结果匹配，如果匹配，它触发完成回调。

所有的魔法都发生在`animatableData`属性内部。在制作动画时，SwiftUI 使用该属性将旧的输入值更改为新的目标值。该值将被设置为旧值，直到动画完成。

换句话说，一旦动画完成，它将符合我们的预期结果。这正是我们如何知道一个动画是完整的。

# 创建一个视图扩展来访问完成回调

建议您为自定义修饰符编写自己的视图扩展方法，因为这只会提高自定义修饰符的可读性。在我们的例子中，这意味着我们需要编写一个方法来设置动画监视器:

该方法将以动画形式显示的属性作为输入，并要求传入完成回调。

此方法的一个实现示例如下:

```
Text("Welcome to SwiftLee")
    .opacity(introTextOpacity)
    .onAnimationCompleted(for: introTextOpacity) {
        print("Intro text animated in!")
    }
```

# 结论

SwiftUI 中的动画是通过`withAnimation`和`animation(...)`这样的方法触发的。默认情况下，动画完成时不可能得到回调。自定义动画修改器允许我们构建一个自定义解决方案，一旦某个属性的动画完成就触发回调。

感谢阅读！
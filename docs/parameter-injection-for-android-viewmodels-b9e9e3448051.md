# Android 视图模型的参数注入

> 原文：<https://betterprogramming.pub/parameter-injection-for-android-viewmodels-b9e9e3448051>

## 使用 SaveStateHandler 和 Hilt 处理 Android 视图模型中的参数注入

![](img/cb06336ad056eab1f56a65219f092369.png)

约书亚·雷德科普在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

将依赖项注入到我们的视图模型中已经是很好的实践了。它保持了实现的灵活性和易测试性。

但是提供给屏幕或片段的参数呢？例如，分割参数或组成导航参数。通常使用类似于`init`的方法从视图接收参数并设置`ViewModel`。这给我们需要注意的`ViewModel`增加了额外的步骤。因此，在构造函数中获取依赖项和参数会更好。

# 设置

对于这个例子，让我们保持简单，主要关注处理参数。

我们创建了一个有两个屏幕的应用程序:

1.  屏幕 1 只是一个按钮。点击它会得到一个随机数，并导航到屏幕 2，将随机数作为参数传递。

![](img/9d510878e534886e7f1270bf231cee1c.png)

1.  屏幕 2 接收随机数，创建一个`View`状态，并简单地将结果显示为文本。

![](img/8d675e591d5636b8f56853783abab128.png)

屏幕是用 Jetpack Compose 创建的，该示例也使用 Composes NavHost 导航，但是相同的`ViewModel`代码适用于活动和片段。唯一的区别是允许用作参数的类型。在下面的设置中，组合导航只允许我们将参数作为导航路径字符串的一部分进行传递。

正如我们所看到的，我们的第二个屏幕有 route `details/{randomNumber}`声明参数`randomNumber`。

# 处理保存的状态

现在来谈一个重要的问题。我们如何在导航后的第二个屏幕上检索 ViewModel 中的参数？

`SavedStateHandle`类包含了我们需要的信息，它可以直接注入到`ViewModel`的构造函数中。

```
class DetailsFlowViewModel(
    savedStateHandle: SavedStateHandle
) : ViewModel() {
    ...
}
```

无论有没有像 Hilt 这样的依赖注入框架的帮助，这都是可能的。

`SavedStateHandle`为我们提供了两种获取参数的方法。

```
operator fun <T> get(key: String): T?fun <T> getStateFlow(key: String, initialValue: T): StateFlow<T>
```

根据我们想要达到的目标，我们可以使用任何一种方法。在我们的例子中，我们希望提供一个从`ViewModel`到`UI`的`View`状态流。所以，还是用`getStateFlow`吧。

> *重要提示:因为我们使用的是组合导航，所以在将参数转换为实际的 Int 类型之前，我们必须首先将参数作为字符串进行检索。有了片段参数，就有可能直接获得一个 Int 类型的参数。*

# 更进一步

我们已经可以直接向`ViewModel`的构造函数提供我们的参数。但是仍然有一个缺点:`ViewModel`构造函数没有告诉我们它到底想要什么，但是，例如，在测试中，我们需要知道在将类型`String`的`randomNumber`传递给构造函数之前，将它设置为`SavedStateHandle`。听起来这需要很多关于实现细节的知识。

如果构造函数只是告诉我们:我想要 Int 类型的参数`randomNumber`，不是更好吗？

我们可以借助像 Hilt 这样的依赖注入框架来实现这一点。

> *长话短说，我不打算在这篇文章中详细介绍刀柄的基本用法。如果你想了解 Hilt，你可以去它的* [*Android 开发者教程*](https://developer.android.com/training/dependency-injection/hilt-android) *。*

首先，我们创建一个`Qualifier`注释，允许我们标识我们的参数。

```
@Qualifier
@Retention(AnnotationRetention.BINARY)
annotation class RandomNumber
```

用`Qualifier` `RandomNumber`我们可以创建一个小的刀柄模块，在`ViewModel`范围内提供我们的参数。

我们在`ViewModelComponent`中安装模块，使参数在它被注入的`ViewModel`的生命周期内可用。实际的`provideRandomNumber`方法是我们之前在`ViewModel`中的代码，有一点不同。我们不使用`Flow`而是直接获取值。

有了模块，我们的`ViewModel`就变得简单了。

我们使用`Qualifier`请求我们想要的参数，并简单地使用它来创建我们的`View`状态。

# 结论

如本文所示，使用参数注入确实比注入`SavedStateHandle`或创建 init 方法需要更多的代码，但它更好地分离了我们应用程序的不同方面，允许更可读和可测试的代码。

使用`SavedStateHandle`、`Hilt`和`Activity`的不同变体的完整示例可以在 [GitHub](https://github.com/a-frank/viewmodel-parameters) 上找到。

如果你想知道，同样的概念可以用 Koin 来实现。

下一集见。
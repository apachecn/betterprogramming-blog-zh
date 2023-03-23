# 支持 Android 的生命周期感知视图模型

> 原文：<https://betterprogramming.pub/empowered-lifecycle-aware-viewmodel-for-android-f495de9a8170>

## 将生命周期事件委托给视图模型

![](img/93b7983d91b14f13c18fdc695ddb25f1.png)

马特·哈德森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

视图模型的存在是为了从视图中去掉所有的业务逻辑。无论您如何构建视图模型，一些逻辑仍然与视图的生命周期紧密相关。例如，一个小的用例是在每次打开屏幕时执行一些跟踪。假设这个屏幕注入了一个 ViewModel，并且这个 ViewModel 理所当然地拥有跟踪逻辑，那么您需要检测何时触发这样的操作。

因为生命周期属于视图的领域，所以您可以将它留给视图负责——例如，覆盖`onResume`函数。

但是我们不要忘记为什么我们要使用视图模型。最终，我们打算将这个业务逻辑提取到 ViewModel 中进行测试。有了这个架构，我们可以对实现进行单元测试——已经用正确的参数调用了跟踪，如果您使用的是 [Mockito](https://site.mockito.org/) ，就调用`verify`。

可悲的是，我们无法测试我们的行为。我们不能断言跟踪是在用户打开屏幕时发生的。只是跟踪被正确地实现了。这是一个巨大的差异！更不用说它不能很好地扩展。如果有人无意中删除了您视图中的这部分代码，您将得到:

*   这是一种倒退，因为你不再跟踪你打开的屏幕。
*   视图模型中的一些死代码。
*   绿色单元测试覆盖了一个未插电的用例。

现在我已经说明了我的情况，让我们看看如何让我们的视图模型生命周期意识到。

# 生命周期感知组件

了解生命周期可以让您观察特定视图发生的任何生命周期事件。基本上，您将这些事件从视图转移到视图模型。

ViewModel 已经通过它的`onCleared`回调来响应生命周期事件。ViewModel 的生命周期与视图不同，但当不再需要它时会得到通知。

为了进一步增强其生命周期意识，我们可以依赖 Google 的 Jetpack 库`[androidx.lifecycle](https://developer.android.com/topic/libraries/architecture/lifecycle)`。当您想让一个组件具有生命周期意识时，只需实现它的`LifecycleObserver`接口，并将其声明为您想要观察的生命周期所有者的观察者。如果你不需要对所有的生命周期事件做出反应，你可以使用`DefaultLifecycleObserver`界面。

有了这个生命周期感知视图模型，您不再需要在视图中覆盖`onResume`。视图模型负责所有的业务逻辑。恭喜你！

但是测试呢？所以我们才这么做对吗？

# 测试，测试

我编写了一个空壳来测试我们的视图模型

为了使这个测试有效，我们需要一个从一种状态转换到另一种状态的机制。我们需要三样东西:

1.  一个`LifecycleRegistry`来操纵生命周期状态。
2.  一个`LifecycleOwner`我们传递到`LifecycleRegistry`。
3.  A `LifecycleObserver`:我们用例的测试视图模型。

为了创建一个`LifecycleRegistry`，我们可以使用专门为测试设计的`createUnsafe`方法。我们将把`LifecycleOwner`注入到`LifecycleRegistry`中作为模拟:

```
val lifecycleOwner = mock<LifecycleOwner>()
val lifecycleRegistry = LifecycleRegistry.createUnsafe(lifecycleOwner)
```

然后我们需要将视图模型注册到`LifecycleRegistry`。

```
lifecycleRegistry.addObserver(viewModel)
```

最后，您可以指向所需的生命周期状态:

```
lifecycleRegistry.*currentState* = Lifecycle.State.*RESUMED*
```

这是我们测试过的类:

您现在可以基于特定的生命周期状态来测试您的视图模型了！我们可以更进一步，去掉一些样板文件。

一种方法是将其提取到一个类中，在其构造函数中采用生命周期观察者。

在您测试过的类中，创建它的一个实例，并使用提供的公共方法更改生命周期状态。

# 结束语

测试实现的行为可以帮助您构建可伸缩的应用程序。使用 MVVM 架构时，确保将所有业务逻辑提取到视图模型中。

通过使您的视图模型具有生命周期意识，您可以将一些属于视图的逻辑委托给视图模型。您的视图只能更新其状态并执行来自 ViewModel 的效果。仅此而已！

虽然本文关注的是视图模型，但这并不妨碍您让其他组件具有生命周期意识。在一些用例中，当业务持有者组件需要访问这些信息来执行一些操作而不必将它们暴露给视图时，这可能会很方便。

总是保持你的视图代码最少。
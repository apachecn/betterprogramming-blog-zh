# 关于 Jetpack 的 7 件令人讨厌的事情

> 原文：<https://betterprogramming.pub/7-annoying-things-about-jetpack-compose-according-to-me-bdb3c8fdf7d0>

## compose 中令人讨厌的错误和缺失的功能，这是我在全职工作时将一个遗留应用程序迁移到 compose 时亲自遇到的。

![](img/cfa59659868614e53a01c8b8e738cb08.png)

Jetpack Compose 可以说是 Android 开发领域最激动人心的创新之一。它给陈旧的 XML UI 系统带来了新鲜空气，带来了许多改进。

最重要的是，它的发展速度也是惊人的。自从它稳定下来已经快一年了，正在被各个团队采用。但并非一切都是完美的。Jetpack compose 仍然有一些不完全成熟或尚未开发的领域。在这篇文章中，我将回顾一些恼人的错误和缺失的功能，这些都是我在全职迁移一个遗留应用以进行 compose 和我自己的一些附带项目时亲身遇到的。

# 1.预览性能

在 Android studio 中，我们可以设置预览，以便查看我们在 compose 中实现的设计。但是就性能而言，这个预览版与旧的 XML 预览版相差甚远。更改不会立即显示在屏幕上，需要重建才能看到。但是随着时间的推移，compose 预览版将会出现，并且很可能超过 XML 预览版。最近有一些更新来支持交互性和预览动画。

# 2.缺少图书馆

根据您开始使用 compose 的时间，与我们通常习惯的 UI 库相比，您会发现非常少的 UI 库。我已经为一些常见的问题创建了定制的解决方案(从好的方面来看，这给了我更多编写可组合件的经验)。这当然是意料之中的，因为 compose 还相对年轻。

# 3.性能监控

性能是一个应用程序非常关键的一部分，我们作为开发人员的工作就是为我们的用户保持流畅的体验。不幸的是，如果没有合适的工具来调试性能，我们就无法做到这一点。我们仍然可以使用调试旧的 XML UI 的工具，但是我认为我们需要新的工具和系统来构建声明性系统。根据我的经验，大多数 UI 在编写时的延迟都是由对状态如何工作的误解引起的，最终导致了多次不必要的重新编写。Google Play 团队也意识到了这一点，并构建了一个[自定义修改器](https://github.com/android/snippets/blob/master/compose/recomposehighlighter/src/main/java/com/example/android/compose/recomposehighlighter/RecomposeHighlighter.kt)，用于在优化过程中跟踪重组。像这样的工具应该更容易访问，直接进入 compose。Android Studio 中也可以有警告，以避免导致 UI 滞后的常见反模式。

# 4.广告(ad 的复数)

大多数应用程序依靠广告获得收入。因此，你可能需要以某种方式在你的应用程序中实现广告。但是如果您在应用程序中使用 compose，您可能会遇到一些必须切换到 XML 视图的情况。在 jetpack compose 中，Ad 库本身还不可用。当您用 xml 定义广告的文本元素，但仍然需要将它与撰写主题的设计相匹配时，这就成了一个问题，尤其是对于原生广告。`AndroidView`确实提供了非常好的互操作性，但我认为这只是连接两个世界的垫脚石。为了获得更好的体验，广告库应该提供广告容器的可组合版本。

# 5.导航动画

任何超过一个屏幕的应用程序都需要一个系统来管理应用程序的导航。喷气背包导航是官方推荐的方式，但它有一个难以忽视的奇怪的[缺陷](https://issuetracker.google.com/issues/172112072#comment25)。屏幕之间有一个渐变动画，这是不可配置的，也不能关闭。还有另一个[问题](https://issuetracker.google.com/issues/197140101)跟踪当导航时动画将被添加，但是现在，你可以使用伴奏导航库并调整或关闭动画。

# 6.列表动画

为 RecyclerView 编写适配器和视图持有者的日子已经一去不复返了。现在我们有了可以在几秒钟内创建的 LazyLists。但在动画方面，还是比不上`RecyclerView`。已经有一些[更新](https://developer.android.com/reference/kotlin/androidx/compose/foundation/lazy/LazyItemScope#(androidx.compose.ui.Modifier).animateItemPlacement(androidx.compose.animation.core.FiniteAnimationSpec))添加了重新排序的动画(尽管已经有一些[问题](https://issuetracker.google.com/issues/150812265#comment18)对此进行了报道)，但是仍然没有添加和删除动画的官方实现。我一直在我的应用程序中使用这个[解决方案](https://github.com/davidliu/LazyColumnItemDiffAnimations)，直到有一个正式和稳定的实现。

# 7.飞虫

最后一个看起来很小，但对用户如何看待这个应用有很大的影响。嵌套滚动是某些 UI 模式中的常见构建块。无论是一个折叠的应用程序栏还是一个带有列表的模态，它都有很多用途。在这些情况下，滚动性能应该是反应性的和平滑的。失败的话，应用程序可能会觉得不自然或者不是本地的。 [fling bug](https://issuetracker.google.com/issues/179417109) 在滚动时提供了错误的速度值，导致列表瞬间停止滚动，甚至开始向错误的方向滚动。

以上是迁移写作时遇到的 7 件烦心事。如果你想分享我的沮丧，或者你已经找到了解决这些问题的适当方法，请告诉我

感谢阅读，祝你好运！

*最初发表于*[T5【https://sinasamaki.com】](https://sinasamaki.com/post/2022-04-05-compose-7-shortcomings/)*。*
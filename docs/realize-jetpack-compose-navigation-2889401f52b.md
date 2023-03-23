# 修正了 Jetpack 组合导航的问题

> 原文：<https://betterprogramming.pub/realize-jetpack-compose-navigation-2889401f52b>

## Jetpack Compose 中导航的常见陷阱以及我们如何处理它们

![](img/d7fdc332fae602c1196afc26874feb75.png)

我们团队已经在[余辉](https://play.google.com/store/apps/details?id=com.afterglow.client&hl=en&gl=US) app 中使用 Compose 六个月了。在这篇文章中，我想分享我们所面临的困难，并提出我们所找到的解决方案。

# Jetpack 合成中的导航库

我找到了 3 个最流行的导航解决方案: [Jetpack Compose Navigation](https://developer.android.com/jetpack/compose/navigation) 、 [Voyager](https://github.com/adrielcafe/voyager) 、 [Decompose](https://github.com/arkivanov/Decompose) 。

我不会在这里详细比较每个解决方案。而且，没有一个是最好的，唯一正确的。我宁愿告诉你哪个库更适合你的项目。

*   voyager——一个非常方便简单的库。伟大的**小**和**宠物项目**。它也适合于你准备承担以下风险的更严重的项目:*没有对 iOS 导航的支持，库支持弱，有应用崩溃的问题*。
*   分解——是唯一一个提供成熟的**多平台导航**的流行库。一个好的**稳定解决方案**，有作者的**优秀支持。如果你已经使用了 Arkadii Ivanov 的**[**MVIKotlin**](https://github.com/arkivanov/MVIKotlin)**库**，那么它将很好地融入你的项目。但是很多人*注意到，一开始*很难理解库的用法。如果您承诺在您的项目中使用该库，请准备好花几个工作日来实现它。如果你有一个很大的团队，你还必须编写简化的导航文档，并准备 LiveTemplates 来去掉大量的模板代码。
*   Jetpack Compose Navigation — **大多数项目的可靠库**。如果上面描述的解决方案不适合你，请随意使用谷歌的导航库。我对这个库非常怀疑，知道 XML 世界中 Jetpack 导航的问题。然而，这个图书馆被证明是非常好的。不存在关键问题。很容易在任何项目中实现它，并实现任何级别的导航复杂性。Jetpack 导航不会阻止编写 KMM 应用程序的能力，因为导航本身发生在框架级(视图层)。

因此，我们选择了 Jetpack Navigation，因为它最适合我们的项目，并且风险最小。我注意到 Voyager 也可以适用于许多项目，但作为一家初创公司，我们不敢带一个库，它有许多潜在的问题，在 [GitHub 问题](https://github.com/adrielcafe/voyager/issues)中有描述

# 动画片

在将 Jetpack 导航嵌入到应用程序中之后，您会发现您将无法配置开箱即用的动画。

谷歌的 Jetpack 导航中的动画还处于实验阶段。对于这样的实验，Google 在 Compose 下有一个单独的扩展集合，叫做伴奏者。

在[伴奏导航动画](https://google.github.io/accompanist/navigation-animation/)的帮助下，你可以在 10 分钟内设置好作曲风格下的任何屏幕间转换动画。

# 在淡入淡出动画中消除闪烁的屏幕

如果活动窗口的背景不同于合成屏幕的背景，那么在添加过渡动画后，您会注意到屏幕在过渡期间似乎在闪烁。这是因为 Compose 不会将一个屏幕覆盖在另一个屏幕上，就像在活动之间导航或在 Android 片段之间导航时执行“添加”事务一样。Compose 先播放破坏屏幕的动画，之后马上播放创建新屏幕的动画。如果您添加了淡入淡出动画，那么在过渡中间的某个地方，您会注意到活动窗口的背景，它不同于合成屏幕的背景。

修复这种闪烁很简单:从编写屏幕中移除背景，并将其添加为活动窗口的背景。如果您在每个屏幕上使用自己的背景，那么在用户切换到的屏幕的合成动画结束时，将背景设置为当前屏幕的活动窗口。

从*活动*中，您可以通过以下方式更改*活动窗口*背景:

```
*window*.setBackgroundDrawable(BitmapDrawable)
```

您可以从撰写更改*活动窗口*的背景:

```
*val activity = LocalContext.current as Activity
LaunchedEffect(activity) {
    activity.window*.setBackgroundDrawable(BitmapDrawable)
}
```

## 多活动中的内存泄漏

![](img/258c6bc05e532f7214d2ecba533c02d6.png)

2018 年，[通过路线传递参数](https://medium.com/u/dbdc1597ce6b#nav-with-args)。这是一种非常优雅的数据传输方式。*路线*显示用户传递到下一个屏幕的内容。很容易记录。在进程死亡后，在引擎盖下合成恢复参数。

但是有一个问题:不能在 Compose 导航中传递复杂的对象。一方面，引用数据类型不能被持久化，因此它们在进程死亡后仍然存在(除非它们已经被预先序列化)。

另一方面，Android 开发者已经习惯于使用`Serializable`和`Parcelable`类型在屏幕之间传递复杂的数据。然而，即使是这些也不能在 Compose 中传递。

有对`NavType.ParcelableType`的支持，但事实上，如果不自定义序列化成字符串，通过它传输数据是不可能的。这个问题在 StackOverflow 上被问了很多次:[这里](https://stackoverflow.com/questions/65610003/pass-parcelable-argument-with-compose-navigation)，这里[这里](https://stackoverflow.com/questions/69059149/how-pass-parcelable-argument-with-new-version-of-compose-navigation)。但是撰写导航的创造者仍然[自信](https://developer.android.com/guide/navigation/navigation-pass-data#supported_argument_types)在屏幕之间传输复杂的数据是不可能的。

> 注意:通过参数传递复杂的数据结构被认为是一种反模式。每个目的地应该负责根据最少的必要信息(如项目 id)加载 UI 数据。这简化了流程再造，避免了潜在的数据不一致。

但我不这么认为，我完全赞同[的立场](https://t.me/android_declarative/44297) [阿尔卡季·伊万诺夫](https://medium.com/u/ca9741bc2980?source=post_page-----2889401f52b--------------------------------):

> …，<primitive id="" as="" string="" or="" number="">并不总是足够的，Parcelable 非常有用。例如，对于用户的屏幕，除了其标识符之外，可能还需要关于打开该屏幕的上下文的信息。如果这是我个人资料中的朋友，或者是搜索屏幕中的用户，或者是其他地方的用户。可能需要以不同的方式显示它们。查询的关键字可以是一组参数。在这种情况下，通常使用不同的组合来创建一个密封类可能是有意义的。而且是的，如果设计改变了，那么改变这个类就足够了，所有使用的地方都会停止编译。但是如果标识符类型变成了字符串而不是数字，那么在运行时一切都会崩溃。如果你知道怎么做，有了深度链接，一切都很好。</primitive>

顺便说一下，我们将准确地保存`Parcelable`，而不是`Serializable`或任何其他引用数据类型，因为`Parcelable`是在 Android 中序列化数据的最佳方式:它易于创建，并且可以快速序列化为一种相当紧凑的形式。

互联网上有很多在屏幕间转移`Parcelable`的方法，但它们都归结为三种:

1)在先前的后堆栈中保持可打包:

在当前屏幕上…

```
navController.currentBackStackEntry?.arguments = Bundle().apply {
    putParcelable("article", article)
}
navController.navigate("article")
```

在下一个屏幕上…

```
val article = navController.previousBackStackEntry?.arguments
    ?.getParcelable<Article>("article")
```

> 缺点:当通过 popBackStack()关闭上一个屏幕时，下一个屏幕的数据将会丢失

2)通过束传递数据，忽略屏幕路由:

```
fun NavController.navigate(
    route: String,
    args: Bundle,
    navOptions: NavOptions? = null,
    navigatorExtras: Navigator.Extras? = null
) {
    val routeLink = NavDeepLinkRequest
        .Builder
        .fromUri(NavDestination.createRoute(route).toUri())
        .build() val deepLinkMatch = graph.matchDeepLink(routeLink)
    if (deepLinkMatch != null) {
        val destination = deepLinkMatch.destination
        val id = destination.id
        navigate(id, args, navOptions, navigatorExtras)
    } else {
        navigate(route, navOptions, navigatorExtras)
    }
}
```

> 缺点:
> 1。不可能像在 Compose 中习惯的那样继续通过路由传输简单的数据(日志导航会有困难)。
> 2。这种方式是不可靠的，因为我们使用的方法仅用于导航库的内部目的。它们可以随时关闭。

3)从外部或内部将数据序列化为 route 内部的字符串 NavType:

```
class AssetParamType : NavType<Device>(isNullableAllowed = false) {override fun get(bundle: Bundle, key: String): Device? {
        return bundle.getParcelable(key)
    } override fun parseValue(value: String): Device {
        return Gson().fromJson(value, Device::class.java)
    } override fun put(bundle: Bundle, key: String, value: Device) {
        bundle.putParcelable(key, value)
    }
}
```

> 缺点:
> 1。我不得不写很多额外的代码
> 2。序列化和反序列化需要大量时间，并且会明显影响肉眼合成的渲染速度

在研究了上面描述的所有方法后，我们意识到没有一个适合我们，并决定提出我们自己的解决方案，它既快速有效，又不违背图书馆的原则。

如果我们不去探究实现导航包装器的细节，那么原理是基于在`HashMap<String, Parcelable>`类型的`parcelableArguments`中存储数据。

当在下一个屏幕上接收数据时，我们必须将`*parcelableArguments*`包装在`rememberSaveable{}`中，这样*可打包的*参数可以在进程死亡后继续存在。

你可以点击 GitHub Gist 下面的链接，了解更多关于我们的方法。在那里，我试图列出最重要的代码片段来解决传递可打包对象的问题。

GitHub Gist: [传递 parcelable 参数](https://gist.github.com/VitalyPeryatin/e31385e59f4670a3977d009f0d712c7f)

# 结论

我们已经讨论了在 Compose 中实现导航时可能遇到的主要问题。我们找到了任何人都可以轻松集成到他们的项目中的解决方案。我们希望现在您使用 Compose 会少受些苦，多些乐趣！

让我们一起让这篇文章对我们大家更有用。写出你将如何解决文章中描述的问题。我希望得到您的反馈！
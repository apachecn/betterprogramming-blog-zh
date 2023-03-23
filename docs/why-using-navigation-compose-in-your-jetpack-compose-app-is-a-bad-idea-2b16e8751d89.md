# 为什么在你的 Jetpack 撰写应用程序中使用导航撰写是一个坏主意

> 原文：<https://betterprogramming.pub/why-using-navigation-compose-in-your-jetpack-compose-app-is-a-bad-idea-2b16e8751d89>

## 知道导航写作的陷阱

![](img/c44e3f4ef715b799cd480308686c6546.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

如果你正在阅读这篇文章，你可能会想:

“嗯，我想知道在我的 Jetpack Compose 应用程序中使用什么来导航。我是应该像 Google 建议的那样使用导航合成，还是坚持使用片段，只在呈现视图时使用合成？”

在本文中，我将讲述我在 Jetpack Compose 应用程序中使用导航合成和片段的经历。你会学到哪种方法更好(在我看来),以及使用每种方法会遇到什么样的障碍。

# 我们开始吧

如果你在谷歌上搜索“Jetpack Compose Navigation ”,你可能会看到导航合成库作为第一批结果之一。谷歌将其描述为一个新的导航组件，支持用 Compose 编写的应用程序，并声称“由于它，我们可以在组件之间导航，同时利用导航组件的基础设施和功能。”

听起来很完美，但真的是这样吗？我们应该谈谈新的导航看起来像什么…

谷歌真的想创建一个框架，能够管理生命周期、导航和围绕它的一切，同时消除使用片段的需要。他们的想法是创建一个可组合的导航图(`[NavHost](https://developer.android.com/jetpack/compose/navigation#create-navhost)`)，而不是一个 XML 文件，我们可以在其中定义应用程序可以导航到的路线。其实仔细看，片段就是这样被 navBackstackEntry 代替的。不幸的是，它运行得不是很好，而且有很多问题。

# 路线路径

首先—路由路径。

谷歌可能羡慕 Flutter、React 和其他类似的框架，并认为 Android 的最佳解决方案是在 web 应用程序中定义屏幕路径——尽管我们创建的是移动应用程序，而不是 web 应用程序，对吗？

这对我们意味着什么？

这意味着每个路由看起来像一个 URL 结尾，例如

*   `/users` —用于用户列表屏幕
*   `/users/2` —用于 id = 2 的用户的详细屏幕。

从现在开始，开发人员必须保持路径及其部分的顺序，记住它们及其参数，或者为各个屏幕创建常量，这已经意味着我们要开始比以前多得多的工作。

接下来发生的事情更令人困惑。

# 导航参数

以前，使用片段和安全参数，我们可以在 XML 中定义给定片段的参数。当创建从一个屏幕到另一个屏幕的动作时，我们有一个自动生成的代码，要求我们输入正确类型的参数，并将它们打包到`Bundle`中。接下来，要在目标片段中提取这些参数，我们可以使用`by navArgs()` 委托，它会自动为我们完成。

我们能够将许多不同的类型作为参数传递，比如 Int、String、Boolean 等。还包括自定义的，比如枚举、可序列化的和可打包的。支持类型的完整列表可以在[这里](https://developer.android.com/guide/navigation/navigation-pass-data#supported_argument_types)找到。

在导航作曲上也一样方便吗？*完全没有。*

参数只能在给定路由的 URL 路径中传递，例如:

*   路径参数— `/users/{arg1}/details/{arg2}`
*   或者更复杂的是，您还可以在问号后面传递查询参数和可选参数— `/users/{arg1}/details?{arg2}={arg2_value}&{arg3}={arg3_value}`

哦，不要忘记，对于您想要使用的每个目的地和参数，*您* 负责所有的类型安全。组合导航不会告诉你，你把一个整型参数错当成了一个字符串。这只会使应用程序崩溃。

每个参数都必须在`NavHost`的`composable`中声明:

并且随后也用适当的类型提取:

所以现在，程序员不仅要自己管理所有的路径，他还必须记住特定屏幕采用什么参数，参数的键和数据类型以及它们的顺序。

你会在编译时得到错误信息吗？没有。只有在应用程序运行时，才会出现错误或应用程序崩溃。

# 一切都是一串

此外，不要忘记，由于屏幕路径就像一个 URL，每个参数都必须作为字符串传递，并且必须进行编码。

假设我们想将这个字符串作为参数传递:

```
val urlParam = "[https://translate.google.com/?hl=en&tab=TT](https://translate.google.com/?hl=en&tab=TT)"
```

为了导航，我们必须这样写:

```
navController.navigate("path/arg=${URLEncoder.encode(urlParam)}")
```

然后像这样接收它:

```
URLEncoder.decode(bundle.getString("arg_key"))
```

更好的是，如果您在包含`‘\n’` 字符的字符串上使用`URLEncoder.encode(…)`，它会因为`‘%0A’`而崩溃，所以使它工作的唯一方法是首先使用`Base64`编码。

这使得导航组合 API 完全是非类型安全的。

# 枚举、可序列化和可打包的呢？

就像我之前提到的，我们想要传递的任何参数，不管是什么类型，都必须转换成一个字符串，才能添加到路径中。

## 枚举

撰写导航不会让您将`Enums` 作为参数传递，但这可以用不同的方式来完成。理论上，我们可以将它转换成一个字符串，并在目标屏幕上调用`valueOf`方法来查找我们之前作为字符串传递的 enum 中的值。

## **可打包的和可序列化的**

这就是乐趣的开始。也不支持在组合导航中将`Serializable`和`Parcelable`作为导航参数传递。

我在网上看到很多人说，进入`backStackEntry`参数并手动传递扩展`Serializable`或`Parcelable`的对象是可能的。

**然而…**
这只是一个谷歌不推荐的黑客行为，也没有给出任何保证这一定行得通。

就我个人而言，我遇到过很多这样传递参数导致应用程序崩溃的情况。

不相信我？去这里读读。

## 我们能做些什么吗？

嗯，理论上…是的。我们可以将我们的`Serializable`或`Parcelable`对象转换成 JSON，并将其作为字符串传递。我为那些想以这种方式传递参数的人感到难过。这是一种方法，但是它太愚蠢了，带来了太多不必要的样板文件和复杂性，我甚至不会考虑使用它。

## **更新**

自从 Compose 版本 1.0.3 和 NavigationX `2.4.0-alpha10`以来，我们现在能够创建自定义的`NavType`:

假设你有一个这样的类:

你可以这样定义`NavType` :

并使用它:

这似乎比我们以前拥有的要好，但是仍然要求我们在内部使用 JSON 。还可以想象一下，每当您想要传递一个 Parcelable 时，就创建一个新的定制`NavType`。这只是给我们的应用程序添加了更多的样板代码。此外，Google 的文档中没有提到这个解决方案也能与组合导航一起工作。

# 但是为什么呢？

现在主要的问题出现了——为什么？

为什么导航排版是那样的？为什么它不支持可序列化，可打包，为什么它有这么多问题？

很有可能 Google 希望鼓励开发者只传递对象的 id，而不是它的静态副本。例如，如果我们操作房间数据库并使用 Flow 观察数据，这将是有意义的。例如，如果在此期间有什么东西改变了这个数据，那么用户会自动在屏幕上看到当前的值。

的确，也许在一些像这样的情况下，这是有意义的。不幸的是，经常有这样的情况，我们希望有意识地传递一个静态对象，或者至少传递它的一部分，以便用户可以立即看到屏幕上的数据。之后，我们可以在后台加载一些细节，或者简单地检查数据是否是最新的。

# 只用片段

好的，那么我应该如何使用 Compose 来避免这些问题呢？答案很简单。使用片段和旧导航。

## **您仍应使用片段的原因**

想用什么都可以: **XML** ， **Compose** ，你选。最简单的方法是将`**ComposeView**` 添加到`**Fragment**`视图中，并将给定`**Fragment**`的`**Composable**` 设置在那里。

不是`LaunchedEffect`、`DisposableEffect`之类的粉丝？你不必使用它们，因为你可以用老方法写一些代码，就像在片段中一样。

根据你使用的 DI 框架，如果你使用导航合成，你也可能会遇到一些问题。
在我目前的项目中，我们正在使用`Koin`，不幸的是，在新的导航中`SavedStateHandle`无法注入到`ViewModel`中。这个问题当然要由 Koin 团队来修复，但是目前来说，如果你使用`Fragments` 并使用`by viewModel()` delegate 注入`ViewModel`就消失了。

## 当然，碎片并不完美。在这种情况下，你应该考虑他们的哪些问题？

首先，你在用片段。这可能一开始看起来很滑稽，但有些人不喜欢他们，例如，杰克·沃顿。简而言之，他说你可以使用片段，但背后攻击是一场噩梦。

你有更多的代码。毕竟，对于每个屏幕，您必须同时编写片段和组合。

另一件事是，您需要管理两个生命周期——片段生命周期和可组合生命周期。这乍一看似乎有问题，但幸运的是，有一个快速的方法来处理它。只需在`ComposeView`上使用`setViewCompositionStrategy`方法，并设置可组合组件的生命周期相对于片段的生命周期应该如何表现。

最后但同样重要的是，您必须记住，当将一个`Parcelable`对象作为一个安全的 Args 参数传递时，您还必须将它添加到 proguard 配置中，否则您的产品将会崩溃。

# 摘要

那么使用碎片是不是最好的解决方案呢？大概不会。

在我看来，如果 Google 在 Compose 中为我们提供一个现成的、有效的、良好的导航 API，那将是最好的。不幸的是，正如我之前提到的，我们现在可以忘记它。目前，我提出的解决方案是我自己使用的，我觉得还可以，至少目前是这样。

我们还应该记住，许多库和工具还没有 100%支持 Compose，有时用老方法、片段甚至 XML 编写东西的能力正是我们所需要的。

感谢阅读。

```
**Want to Connect?**✉️ Android Dev Newsletter — If you enjoy learning about Android like I do and want to stay up to date with the latest, worth reading articles, programming news and much more, consider [subscribing to my newsletter](https://androiddevnews.com/).🎙 Android Talks Podcast — If you’re a Polish speaker and want to listen to what I have to say about Android, architecture, security and other interesting topics, [check out my podcast](https://androidtalks.buzzsprout.com/)
```

你可以听听我做的关于这个话题的播客:
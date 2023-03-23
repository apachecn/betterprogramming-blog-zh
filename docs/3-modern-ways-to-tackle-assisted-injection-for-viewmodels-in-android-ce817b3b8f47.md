# 解决 Android 中视图模型辅助注入的 3 种现代方法

> 原文：<https://betterprogramming.pub/3-modern-ways-to-tackle-assisted-injection-for-viewmodels-in-android-ce817b3b8f47>

## 用 Dagger 和 SavedStateHandle 保存您的状态

![](img/64514c91112ded46416915df534070a3.png)

[张家瑜](https://unsplash.com/@danielkcheung?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

如果您正在使用 [Dagger](https://dagger.dev/) 作为依赖注入(DI)框架，那么您很可能已经面临无法提供所有依赖的情况。

让我们从一个简单的例子开始。首先，我们希望创建一个需要存储库来运行的管理器:

要用 Dagger 提供存储库依赖，您最终会得到这样的结果(假设在某个`Module`中提供了`Repository`):

现在让我们假设您需要一个标识符来从存储库中获取特定的资源。您需要将这个标识符传递给管理器，让注入的存储库查询所需的资源:

因为 Dagger 不能提供这个运行时参数，所以您可以从构造函数中提取它，并通过以下方式之一手动提供它:

*   使用`lateinit`并祈祷管理器在使用它之前收到它的值，否则应用程序将崩溃:

*   声明标识符可空并处理可空性，虽然这没有意义(因为没有它管理器就不能操作):

*   创建一个包含所有可注入参数的工厂，然后通过一个`create`方法给出运行时参数:

那么你就需要注入`Factory`而不是经理。最后，您将使用`create`方法传入标识符:

总的来说，所有这些解决方案都是可行的，但是它们都有局限性。此外，我们还有两个未解决的问题:

1.  提供一个`ViewModel`并不像上面的例子那样简单。
2.  我们还没有解决如何在`ViewModel`中存储我们的状态。

# 提供保存状态的视图模型

如前所述，`ViewModel`的 DI 不像其他对象那样工作。即使您可以让 Dagger 提供您的构造函数参数，您仍然需要使用一个`ViewModelProvider`来创建一个`ViewModel`实例。

提供者希望拥有者和工厂都可以传递注入的参数:

```
ViewModelProvider(owner, factory).get(MyViewModel::class.java)
```

所有者定义了`ViewModel`的范围。根据它，系统将知道是创建一个新的`ViewModel`还是检索一个现有的满足所有者的。大多数时候，它要么是一个`Activity`，要么是一个`Fragment`。

复杂的部分是工厂自带的。当向您的`ViewModel`提供参数时，您需要将这些参数传递给扩展 androidX `ViewModelProvider.Factory`的定制工厂。

我不会详述，因为有很多方法可以做到这一点——手动或利用[匕首多绑定](https://blog.kotlin-academy.com/understanding-dagger-2-multibindings-viewmodel-8418eb372848)。

至于保存状态，androidX 给出了一个基于`SavedStateHandle`对象的机制。它接受一个`Bundle`，您可以将想要存储的数据放在这里，并直接从您的`ViewModel`中检索。

在前面的例子中，我们将标识符插入到一个`Bundle`中，并将其传递给一个`SavedStateHandle`实例。这里，我公开了一个静态的`map`方法来从标识符参数中创建`SavedStateHandle`，并且我可以在任何时候从`ViewModel`中检索它:

您需要在项目中包含这种依赖关系:

```
implementation("androidx.lifecycle:lifecycle-viewmodel-savedstate:${latest_version}")
```

# 辅助注射抢救

我们试图找到一个解决方案，让我们的 DI 用提供的参数和运行时参数构建一个对象。此外，我们还想在`ViewModel`内保存我们的状态。

经过多次实验后，我向您展示了三种可行的替代方案及其优缺点。

最终，选择一个解决方案主要取决于您的项目设置。

事不宜迟，我们的候选人来了！

## 1.优雅的视图模型注入库

我本来可以先给大家展示一下 Square 制作的[辅助注射](https://github.com/cashapp/InflationInject)库。然而，[乔丹·汉森](https://medium.com/u/105afd7f0a0b?source=post_page-----ce817b3b8f47--------------------------------)走得更远，在方形图书馆的顶部创建了一个[图书馆](https://github.com/hansenji/ViewModelInject)。它提供了一个简洁的 API:

*   通过自定义的`@ViewModelInject`注释生成`ViewModel`工厂。
*   接受带有方形`@Assisted`注释的`SavedStateHandle`参数。

很棒，对吧？它解决了我们所有的需求，没有任何样板文件。这就是我们前面的例子使用这个库的样子:

为了实现这一点，你需要为每个 Gradle 模块创建一个 Dagger 模块*。*和它的命名有关。假设我正在名为`awesome-module`的 Gradle 模块中创建`ViewModel`。Dagger 模块必须是:

最后，您可以注入生成的工厂，并通过 intent 的捆绑包使用它的`create`方法。该库将通过在`intent.extras`中提供`Activity`的包来为您构建`SavedStateHandle`:

我们可以更进一步，用 Kotlin 委托创建我们的`ViewModel`:

那么上面的例子就变成了:

尽管这个库符合我们的期望，但值得一提的是，您将需要依赖这个库和来自 Square 的 AssistedInject。此外，当 Dagger 最近支持这种 DI 模式时，这两个库都被弃用了。

最后，我们注意到这个库不能很好地处理增量构建。生成的代码经常碍事，迫使您临时禁用`gradle.properties`文件中的增量构建来重置缓存。

## 2.新人剑柄

虽然[剑柄](https://dagger.dev/hilt/)在 2020 年才刚刚出现在我们的雷达上，但是这个很有前途的 DI 库在几周前才刚刚稳定下来(撰写本文时)。所有的目光都投向了这位新候选人。

它不仅使用与 Dagger 相同的机制，而且还消除了配置项目使用 DI 的所有障碍。

对于辅助注射，[手柄将其支撑在盒外](https://dagger.dev/hilt/view-model.html)。你所需要做的就是用`@HiltViewModel`注释你的`ViewModel`类，并在构造函数中给出一个`SavedStateHandle`参数。当创建`ViewModel`时，Hilt 将解析它的依赖项并提供一个`SavedStateHandle`。甚至不需要注射一个`Factory`。希尔特为你做一切！

要使用内置的 Kotlin 委托，您需要添加以下依赖项:

```
implementation("androidx.activity:activity-ktx:{latest_version}")
implementation("androidx.activity:fragment-ktx:{latest_version}")
```

他们将 Kotlin 委托给`Activity`和`Fragment`，这将懒洋洋地创建您的`ViewModel`:

```
// From an Activity
val viewModel: MyViewModel by viewModels() // Activity scope// From a Fragment
val viewModel: MyViewModel by viewModels() // Fragment scope
val viewModel: MyViewModel by activityViewModels() // Activity scope
```

总而言之，使用 Hilt 的辅助注射可以归结为这几行:

从理论上讲，Hilt 看起来为我们的问题提供了最先进和最简洁的 API。然而，如果我能给出我的两美分:

1.  `ViewModel`的辅助注射是自动进行的，对于未经训练的眼睛来说可能会感到困惑。了解它在引擎盖下的工作原理会有所帮助。否则，请做好调试应用程序的准备。
2.  Hilt 几乎没有达到稳定，大多数项目不使用这个 DI 库。此外，迁移到 Hilt 并不便宜，而且可能无法提供与当前 DI 系统相同的功能。

## 3.升级版匕首

最后但同样重要的是，我们又回到了老匕首！从[版本 2.31](https://dagger.dev/dev-guide/assisted-injection.html) 开始，匕首支持辅助注射。

它结合了两个注释来告诉匕首如何注射你的`ViewModel`。在我们之前的示例中，实现是这样的:

所有的`@AssistedFactory`接口必须符合两点:

1.  它必须包含一个方法。
2.  这个方法必须返回`ViewModel`类类型。

`SavedStateViewModelFactory`声明通用契约，这样你只需要标注你的工厂接口，让它扩展这个接口。

至于创建`ViewModel`，你需要注入工厂并给它`Activity`的包来创建`SavedStateHandle`辅助对象。为了简化这一点，我们将创建自己的 Kotlin 委托:

接下来就是注入工厂，并把它交给我们的委托，用给定的`SavedStateHandle`调用它的`create`方法:

有了这个，一切都用纯匕首工作，代价是一个科特林代表协助你。如果我们将其与 Hilt 进行比较，它会更加冗长，因为您需要:

*   用`@Assisted`注释声明所有辅助参数。
*   用带有所有`@Assisted`参数的`@AssistedFactory`注释声明一个工厂(顺序很重要！).
*   注入工厂，并把它交给一个 Kotlin 委托(与 androidX 委托不兼容)。

话虽如此，你不需要任何额外的第三方库。你也不会弄清楚刀柄是如何连接所有东西的。

# 你应该选择哪种解决方案？

无论你选择什么样的解决方案，请记住，它总是取决于你的项目的限制和你在使用阿迪解决方案时的价值。

第一个方案我用了很久，很满意。然而，我正在考虑转移到第三个解决方案，因为我看不出现在有理由迁移到 Hilt。如果我要开始一个全新的项目，我可能会考虑第二种选择。
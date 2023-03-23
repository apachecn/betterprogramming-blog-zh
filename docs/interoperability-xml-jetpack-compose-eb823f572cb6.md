# XML 和 Jetpack 组成了互操作性

> 原文：<https://betterprogramming.pub/interoperability-xml-jetpack-compose-eb823f572cb6>

## 如果您还在犹豫是否将项目从老式的 XML 视图迁移到 Jetpack compose，那么您来对地方了。在这篇文章中，我将带你走近作曲世界

![](img/377bb89d6eb3dfbefcb2077b1c329faa.png)

2020 年初，我在 Jetpack Compose 的初始阶段开始了我的冒险，当时我已经对一种新的 UI 实现方式充满了热情。

不幸的是，目前许多功能都缺少后台处理、下拉菜单、导航等。但是让我们快进到 2022 年。最后，Jetpack Compose 已经发展到生产就绪阶段，具有惊人的互操作性 API，使迁移比以往任何时候都更容易。

# 为什么选择 Jetpack Compose？

Jetpack Compose 是一个用于构建 UI 的现代工具包，具有一系列优点。尤其是对于整个应用程序切换到一种编程语言，这让我们摆脱了 XML 文件。

它大大减少了命令式 UI 中用于查找视图和设置 UI 逻辑的样板代码，从而简化并加速了 UI 开发，因为声明式 UI 中的逻辑是构建在组件内部的。旁边的合成器使得创建带有状态的动画更加容易，这些状态的值在运行时可以很容易地修改。

此外，使用 Jetpack Compose 导航要方便得多，因为在 XML 文件中设置所有规则是非常痛苦的。另一个好处是视图启动时的性能提高，因为布局就像你的应用程序的其他部分一样被编译，不需要被放大。完全迁移后，应用程序的大小和构建时间减少。

# 如何开始？

您现有的基于视图的 UI 不需要一次重写整个 UI。完全支持互操作性，允许 Compose 和视图在您的代码库中共存，因此您可以使用 Compose 添加新功能或将一些小的现有元素移动到 Compose 世界，当您开始觉得准备好并爱上 Compose 时，您可以使用推荐的迁移策略，称为自下而上的方法，该方法依赖于逐个重写屏幕上的最小视图，直到您可以将`ViewGroup`和`RootView`移动到 Compose 世界。

在接下来的步骤中，您将学习如何为 Compose 配置环境，可能的场景是什么，如何在视图中实现 Compose &在 Compose 中实现 View，以及如何在 Compose 中处理片段。

# 梯度设置

打开构建特性合成和`ViewBinding`，并添加基本合成依赖项

```
android {
    ...
    buildFeatures {
        compose true
        viewBinding true
    }
    composeOptions {
        kotlinCompilerExtensionVersion '1.3.2'
    }
}
dependencies {
    ...
    implementation "androidx.activity:activity-compose:$version"
    implementation "androidx.compose.runtime:runtime-livedata:$version"
    implementation "androidx.compose.ui:ui:$version"
    implementation "androidx.compose.ui:ui-tooling-preview:$version"
    implementation "androidx.compose.material:material:$version"
    implementation "androidx.compose.ui:ui-viewbinding:$version"
    implementation "androidx.navigation:navigation-compose:$version"
}
```

# 在视图中合成

*   `**ComposeView**` **在 XML 布局中—** 向 XML 布局添加撰写视图

```
<androidx.compose.ui.platform.ComposeView
    android:id="@+id/composeView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```

从 XML 布局访问`Compose`世界:

```
binding.composeView.apply {
    setViewCompositionStrategy(ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed)
    setContent {
        MaterialTheme {
            Text(text = "Hello in Compose World!")
        }
    }
}
```

*   `**ComposeView**` **直接作为布局—** 将`ComposeView`实现为一个小部件视图，并访问 Compose world

```
override fun onCreateView(
    inflater: LayoutInflater, container: ViewGroup?,
    savedInstanceState: Bundle?
): View = ComposeView(requireContext()).apply {
    setViewCompositionStrategy(ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed)
    setContent {
        MaterialTheme {
            Text(text = "Hello in Compose World!")
        }
    }
}
```

## 用于合成视图的视图合成策略

默认情况下，`ComposeView`有四种合成策略，每当视图脱离窗口时，compose 就会处理合成。但是，在某些情况下，此默认值可能是不可取的。

*   `DisposeOnDetachedFromWindow`
*   `DisposeOnDetachedFromWindowOrReleasedFromPool`(默认)
*   `DisposeOnLifecycleDestroyed`
*   `DisposeOnViewTreeLifecycleDestroyed`(片段、过渡、生命周期感知定制视图)

# 在撰写中查看

*   `**AndroidView**` **定制视图—** 在工厂中创建一个定制视图并设置点击监听器，以及视图回调—>撰写通信。然后在更新设置通讯时进行撰写- >查看:

```
@Composable
fun XMLCounter() {
    var resultState by remember { mutableStateOf(0) }
    AndroidView(
        factory = { context ->
            CounterView(context).apply {
                // Example of View -> Compose communication
                resultCallback = {
                    resultState = it
                }
            }
        },
        update = { counterView ->
            // Example of Compose -> View communication
            counterView.binding.result.text = resultState.toString()
        }
    )
```

*   `**AndroidViewBinding**` **—** 在 compose world 中展开视图绑定，并访问绑定类

```
@Composable
fun XMLCounterBinding() {
    AndroidViewBinding(ComponentCounterBinding::inflate) {
        down.setOnClickListener { /*...*/ }
        up.setOnClickListener { /*...*/ }
    }
}
```

# 作曲中的片段

将`FragmentContainerView`添加到 XML 布局:

```
<androidx.fragment.app.FragmentContainerView
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/container"
    android:name="com.artf.composeinview.ui.main.MainFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

从 Compose world 访问片段

```
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContent {
        MaterialTheme {
            AndroidViewBinding(ActivityMainBinding::inflate) {
                val fragment = container.getFragment<MainFragment>()
                //...
            }
        }
    }
}
```

# 结论

没有理由犹豫从组合开始，因为互用性 API 目前处于非常好的阶段，允许组合和视图在我们的代码库中共存。并且带来了很多好处，包括启动时间、应用程序大小和构建时间的改进。

# 参考

*   [https://developer . Android . com/jetpack/compose/interop/migration-strategy](https://developer.android.com/jetpack/compose/interop/migration-strategy)
*   [https://developer . Android . com/jetpack/compose/interop/adding](https://developer.android.com/jetpack/compose/interop/adding)
*   [https://developer . Android . com/jetpack/compose/interop/interop-APIs](https://developer.android.com/jetpack/compose/interop/interop-apis)
*   [https://developer.android.com/jetpack/compose/ergonomics](https://developer.android.com/jetpack/compose/ergonomics)

# 链接

*   YouTube:[https://www.youtube.com/watch?v=f6bnuFu8e0s](https://www.youtube.com/watch?v=f6bnuFu8e0s)
*   GitHub:[https://github.com/QArtur99/ViewInCompose](https://github.com/QArtur99/ViewInCompose)
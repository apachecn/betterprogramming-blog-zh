# 为什么 Kotlin 合成纤维被弃用，有什么替代品？

> 原文：<https://betterprogramming.pub/why-are-kotlin-synthetics-deprecated-and-what-are-the-alternatives-5c2b087dda1c>

## 比较使用哪一个:数据绑定与视图绑定、Kotlin 合成与 findViewById

![](img/df200881bee02d81c08ec48bc507e9ca.png)

照片由[路易·蔡](https://unsplash.com/@louis993546?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/kotlin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

最近 Android 宣布，随着 Kotlin 1.4.20 的推出，他们的[Android Kotlin Extensions Gradle 插件](https://plugins.gradle.org/plugin/org.jetbrains.kotlin.android.extensions)将被弃用，并将不再在未来的 kot Lin 版本中发布。

Android Kotlin 扩展插件带来了两个非常酷的功能:

*   **合成**让你用`kotlinx.android.synthetic`绑定替换对`findViewById`的调用。
*   **Parcelize** 允许您移除样板文件，并通过`@Parcelize`注释轻松创建 [Parcelables](https://developer.android.com/reference/android/os/Parcelable) 。

随着这个插件的弃用，我们将不再能够使用这些功能。不要担心 Parcelize，因为它现在将作为一个独立的插件发布:kotlin-parcelize。但这绝对会是科特林合成材料的末日。

现在，全球各地的 Android 开发人员都怀着沉重的心情向 Kotlin 人工合成品说再见。但首先，让我们了解一下当初为什么要引入 Kotlin Synthetics，为什么它现在被弃用，以及除了它我们还有哪些选择。

> “需要是发明之母”

# 历史记录 findViewById

之前，为了获得视图的 id，我们依赖于`findViewById`。
这种方法看起来非常简单，但是开发人员面临着许多问题。

这个函数的工作方式是遍历视图层次结构，查找具有您指定的 ID 的小部件。一旦找到它，它就返回视图，如果不存在，它就返回`null`。让我们讨论一下这种方法的一些问题:

1.  通过看上面的工作原理，我们可以说这种方法成本很高。开发人员会在运行时用尽它，尤其是在列表视图适配器的早期实现中，这使得他们的代码性能很差。
2.  如果你的视图不在布局中，你将在运行时得到`NullPointerException` ，即，非空安全。
3.  在 Android API 26 之前，它返回了[视图](https://developer.android.com/reference/android/view/View)(父类)，你必须手动类型转换，一个错误的转换会导致`ClassCastException`，即不类型安全。
4.  很多样板代码。您必须为所有视图编写相同的方法，对它们进行类型转换，然后将它们赋给一个变量。

然后像 [Butter Knife](https://jakewharton.github.io/butterknife/) 这样的库出现了，使这种方法变得更简单。Butter Knife 部分改善了样板代码的问题，但仍然没有解决类型安全和空安全的问题。此外，它使用了注释处理，这确实稍微降低了构建时间。(注:黄油刀现已弃用。)

# 科特林合成救援

随着 2017 年发布的[Android kot Lin Extensions Gradle 插件](https://plugins.gradle.org/plugin/org.jetbrains.kotlin.android.extensions)，Kotlin Synthetics 问世。对于每个布局文件，Kotlin Synthetics 都会创建一个包含您的视图的自动生成类，就这么简单。
你只需要在你的 Gradle 文件中导入这个插件，你就可以直接引用视图引用变量了。

它只在内部调用`findViewById`一次，然后缓存它。

这是如此方便和有趣的使用，那么为什么它被否决

我们来分析一下。

## 赞成的意见

1.  没有样板代码——只需在你的 Gradle 文件中配置一次，就可以了。Kotlin 合成将自动生成一个类供您使用。
2.  类型安全-所有视图都是从其已知类型的布局文件中选取的。

## 骗局

1.  部分空安全-它通常是空安全的，因为所有视图必须存在于布局文件中才能生成。但是，如果您有多个基于配置的布局文件，其中一些视图在一些布局文件中存在，而在一些布局文件中缺失，该怎么办呢？在这里，您必须手动检查是否为空。
2.  污染名称空间——在不同的布局中可以有相同的视图 ID，并且可以意外地导入其他布局的视图，这将在运行时抛出`NullPointerException`。
3.  仅限 Kotlin——kot Lin 合成材料只能与 kot Lin 一起使用。Java 不支持它们。仍然有一些项目是用 Java 编写的，可能还没有完全迁移到 Kotlin，因此 Kotlin Synthetics 可能不是在您的项目中获取 ViewIds 的一致方式。

由于这些问题，科特林合成现在被否决。
但是用什么代替呢？我们是否应该回到以前的`findViewById`方法，或者有其他一些推荐的方法来达到同样的目的？

让我们找出一些推荐的方法。

# 数据绑定

如[文档](https://developer.android.com/topic/libraries/data-binding)中所述，“数据绑定库是一个支持库，它允许您使用声明性格式而非编程方式将布局中的 UI 组件绑定到应用程序中的数据源。”

首先，将您的应用配置为使用数据绑定，并在应用模块的`build.gradle`文件中启用`dataBinding`构建选项。

```
android {
    ...
    buildFeatures {
        dataBinding true
    }
}
```

要在布局中启用数据绑定，您必须在布局中手动添加`<layout>`标签。

Android Studio 会自动为你的布局生成带有`Binding`后缀的类。比如你的布局文件名是`activity_main.xml`，那么 Android 会生成一个名为`ActivityMainBinding.`的类

数据绑定非常强大，它支持以下内容:

*   将表达式与布局绑定
*   使用可观察的数据对象
*   绑定适配器
*   双向绑定

## 赞成的意见

1.  零安全—它是零安全的。即使我们有基于配置的多个布局，并且很少有视图在其他配置布局中出现/缺失，该视图也会被标注为`@ Nullable`。
2.  类型安全-因为所有视图都是从已知类型的布局文件中选取的。
3.  **支持 Kotlin 和 Java**

## 骗局

1.  样板代码——每个布局文件都需要您手动添加`<layout>`标签，以便它支持数据绑定。
2.  增加构建时间—数据绑定会增加构建时间，因为它必须生成带有视图的类文件，而且还要生成带有 getter、setter 和求值表达式的类文件。

# 视图绑定

简单地说，视图绑定是数据绑定的一个非常简单的版本/子集。
两者的区别在于视图绑定只针对视图引用，不针对 UI 与数据源的绑定。

首先，配置您的应用程序以使用数据绑定，并在应用程序模块的`build.gradle`文件中启用`viewBinding`构建选项。

```
android {
    ...
    buildFeatures {
        viewBinding true
    }
}
```

对于视图绑定，您不必在布局文件中声明任何内容。默认情况下，所有布局文件都会生成一个带有`Binding`后缀的类文件供您使用。

# 视图绑定的优势

*   空安全—与数据绑定相同
*   类型安全—与数据绑定相同
*   没有样板代码——与数据绑定不同，默认情况下，所有布局都可以生成没有任何标签的绑定类。
*   对构建时间没有影响—与数据绑定不同，对构建时间没有负面影响。
*   支持 Kotlin 和 Java。

# 结论

视图绑定和数据绑定应该是推荐的方法。
对于更简单的用户案例，使用视图绑定而不是 Kotlin Synthetics，如果你生活在岩石下并且仍然使用`findViewById`，那么是时候迁移了。

如果您想从数据绑定开始，并对它的好处感到惊讶，那么是时候迁移到数据绑定了。

或者，您可以同时使用视图绑定和数据绑定。
所有带有`<layout>`标签的布局文件将使用数据绑定，而其他布局将使用视图绑定。

# 参考

1.  [Android Kotlin Gradle 扩展插件被弃用](https://android-developers.googleblog.com/2020/11/the-future-of-kotlin-android-extensions.html)
2.  [了解更多关于视图绑定的信息](https://developer.android.com/topic/libraries/view-binding)
3.  [了解有关数据绑定的更多信息](https://developer.android.com/topic/libraries/data-binding)
4.  [从 Kotlin Synthetics 迁移到 View Binding](https://developer.android.com/topic/libraries/view-binding/migration)
5.  [Android Studio 预览版中的新功能](https://developer.android.com/studio/preview/features/#agp-4-0-0)

# 离别赠言

欢迎评论，让我知道你的建议。一定要提出你希望我在接下来的文章中涉及的主题。

仅此而已！谢谢大家！
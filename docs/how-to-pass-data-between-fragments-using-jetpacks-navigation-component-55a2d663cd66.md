# 如何使用 Jetpack 的导航组件在片段之间传递数据

> 原文：<https://betterprogramming.pub/how-to-pass-data-between-fragments-using-jetpacks-navigation-component-55a2d663cd66>

## 带通信的向前和向后 Android 导航

![](img/dbc4e65580e2cf9433a4070ddef8b8cd.png)

[钳工](https://unsplash.com/@benchaccounting?utm_source=medium&utm_medium=referral)对[去毛刺](https://unsplash.com?utm_source=medium&utm_medium=referral)拍照。

在本文中，您将学习如何在向前和向后导航中在`Fragments`之间传递数据。您将了解到`navArgs`的概念以及如何将`LiveData`与`Navigation`组件中的一个键一起使用。

# 先决条件

这篇文章讲述了如何使用 Jetpack `Navigation`组件在目的地之间传递数据，因此需要了解`Navigation`组件的基本知识以及如何使用它。如果您不熟悉`Navigation`组件，请查看以下链接:

*   [Android 中的 JetPack 导航组件](/jitpack-navigation-component-in-android-944165c35f54)
*   [探索 Jetpack 导航组件的实时用例](/exploring-real-time-use-cases-of-jetpack-navigation-component-e58dd8dfba5)

# 概观

屏幕之间的导航是任何 Android 应用的基本功能之一。我们通常通过`Intents`或`Fragment`交易来完成。尽管如此，传统的导航系统并没有为现代 Android 应用程序开发进行优化。

我们需要手动维护后台堆栈并处理好一切。即使在这种情况下，我们也没有像 iOS 应用程序开发中那样的花哨的故事板来代表用户的流动。这使得 Android 开发有点过时，给开发人员带来了很多负担。

# 介绍

组件是 Android 应用开发*中所有导航相关问题的一站式解决方案。* It 为应用内导航提供了一个健壮的导航框架。

Jetpack 的`Navigation`组件带来了一种新的导航模式，导航图代表了用户的导航流程(就像 iOS 中的故事板)。它还会处理`Fragment`事务、动画、后台堆栈等等。

如果您是第一次接触`Navigation`组件，我建议您浏览一下前面提到的文章。

让我们马上进入正题吧。

# 用导航系统向前导航

在组件之间发送数据和导航一样重要。在`Navigation`组件出现之前，Android 开发者通过与`Activity Intent`的附加组件和与`Fragments`的争论来实现。它是我们内部用来发送数据的包。

bundle 只不过是一个支持原始数据类型的键值对数组(类似于 hashmap)。包的优势之一是我们可以通过可打包和序列化技术发送用户定义的对象。

现在有了`Navigation`组件，我们有了安全的参数。这是一个 Gradle 插件，用于在目的地之间传递数据，使用类型安全的默认值，并在编译时执行一些运行时操作。

## 综合

在顶层 gradle 文件中的 dependency 节点下添加以下行:

```
ext.nav_version = "2.3.5"
classpath "android.arch.navigation:navigation-safe-args-gradle-plugin:$nav_version"
```

然后通过添加以下行将插件导入模块级 Gradle 文件:

```
apply plugin: 'androidx.navigation.safeargs'
```

或者，要生成只适合 Kotlin 模块的 Kotlin 代码，请添加:

```
apply plugin: "androidx.navigation.safeargs.kotlin"
```

仅此而已。我们完成了整合。

## 使用

现在让我们从定义导航图中的目的地所需的参数开始。首先，看一看:

这是一个简单明了的实现，不需要任何解释。我们可以根据需要向任何目的地添加任意数量的参数。这将生成基于导航图中给定参数的函数。

一旦我们完成了参数的声明，我们就可以在`Navigation`组件动作中使用它们来传递实际的参数。看一看:

在目的端，我们可以利用 Kotlin 的安全 Args 插件和委托特性来轻松检索数据。看一看:

# 与 SavedStateHandle 的反向通信

在`Navigation` 2.3.0-alpha02 及更高版本中，我们可以从`NavBackStackEntry`访问`SavedStateHandle`。就像 bundle 一样，`SavedStateHandle` 是一个发送和检索数据的键值对。主要优点是它可以经受住配置更改和进程死亡。

`SavedStateHandle` 的一个主要优点是，当当前数据从堆栈中弹出时，我们可以用它将数据发送到源目的地(就像我们在 Android `Activities`中使用`setResult`一样)。

## 使用

为了更好地理解它，让我们看一个简单的用例，用户从`Fragment A`导航到`Fragment B`。现在从`Fragment B`开始，我们需要向`Fragment A`发送数据。

要将数据发送回`Fragment A`，首先，我们需要在`Fragment A`中用一个唯一的键注册`SavedStateHandle`。看一看:

在片段 A 中注册监听器

在`Fragment B`中，我们可以使用`previousBackStackEntry`发送数据。看一看:

仅此而已。反向通信完成。当我们使用`LiveData`进行反向通信时，我们需要意识到发布的数据会被任何新的观察者观察到。如果您只想处理一次数据，那么您需要调用`SavedStateHandle`上的`remove()`来清除结果。

# 结论

Jetpack 的`Navigation`组件是在 Android 应用中实现导航的下一代工具。同时，在 Android 应用程序中，在组件之间传递数据以执行特定操作或者将某种标志传递回源以刷新屏幕是一项常见的任务。这两种情况在本文中称为前向和后向通信。不要通过变通方法来实现这些基本任务。您应该使用本文中提到的推荐方法。

# 奖金

我最近看到了由 [Satya Pavan Kantamani](https://medium.com/u/d255c4fc9d76?source=post_page-----4c9f44d04e68--------------------------------) 发表的这篇关于 Jetpack Compose 的优秀文章:

[](/jetpack-compose-androids-modern-toolkit-for-building-ui-82e67d384c26) [## Jetpack Compose 简介:Android 的未来 UI 工具包

### 告别 XML 布局，采用现代技术

better 编程. pub](/jetpack-compose-androids-modern-toolkit-for-building-ui-82e67d384c26) 

希望你学到了有用的东西。感谢阅读。
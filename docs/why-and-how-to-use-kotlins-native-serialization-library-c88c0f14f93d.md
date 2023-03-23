# 为什么以及如何使用 Kotlin 的原生序列化库

> 原文：<https://betterprogramming.pub/why-and-how-to-use-kotlins-native-serialization-library-c88c0f14f93d>

## 在 Android 中序列化 JSON 响应的最简单方法

![](img/49feaa131b88068a1a3104fe7661de1a.png)

图片来源:科特林

# 从文章中摘录

到本文结束时，您将了解为什么 Kotlin 的团队创建了一个新的序列化库，尽管有许多高级库，如 [Moshi](https://github.com/square/moshi) 和 [Gson](https://github.com/google/gson/blob/master/UserGuide.md) 。除此之外，您还将学习如何使用本机序列化库，如果您有足够的耐心一直读到最后，您可能还会知道本机序列化的隐藏特性。请继续阅读，了解它们。

# 为什么 Kotlin 团队创建了一个新的序列化库

我们有许多著名且有效的序列化库，比如 Square 的 Moshi 和 Google 的 Gson。但是 Kotlin 团队决定为 Kotlin 创建一个全新的本地序列化库。问题是，为什么？

像 Moshi 和 Gson 这样的序列化库都是使用反射的 Java 库，对于 Android 开发来说很好。Kotlin 不限于 Android(JVM)；它支持 JavaScript(JS)和 IOS(原生)开发。反射肯定不会与 [Kotlin.js](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.js/) 和本机模块一起工作。此外，在 Android 中使用反射也是一个缺点。

除了多平台支持和在序列化中使用反射，Java 序列化库还有一个缺点:它们不支持 Kotlin 中的默认值变量。为了清楚地理解它，让我们从一个简单的数据类开始，如下所示:

```
**data class SimpleExample**(**val** data : **String**,
                         **var** optionalData : **String** = "empty")
```

当我们试图解析只有`data` 节点的 JSON 时，那么`optionalData` 值被更改为`null` ，而不是像在数据类中声明的那样分配一个默认值`empty` 。这是一个大问题，因为当一个变量不带问号声明时，Kotlin 编译器将保证该变量永远不会成为`null`，但是普通的 Java 序列化库并不知道这一点。这种类型的功能冲突会导致应用程序出现意外行为。

因此，Kotlin 团队决定创建一个序列化库，它可以与它支持的所有平台一起工作，并且没有反射。

# 综合

要使用 Kotlin 序列化库，我们必须集成序列化插件和运行时序列化库。序列化插件生成代码来解析 JSON，而不使用任何反射。另一方面，运行时库使用这些代码来序列化数据类。

要集成序列化插件，请在应用程序级`build.gradle` 文件顶部的所有插件下添加以下代码行。

```
apply plugin: 'kotlinx-serialization'
```

您还需要在项目级`build.gradle`文件中的 dependencies 节点下添加下面一行:

```
classpath **"org.jetbrains.kotlin:kotlin-serialization:$kotlin_version"**
```

然后在应用程序级的`build.gradle`文件中的 dependencies 标签下添加下面的库实现行:

```
implementation "**org.jetbrains.kotlinx:kotlinx-serialization-runtime:0.20.0**"
```

# 如何对数据类使用 Kotlin 序列化

Kotlin 团队的这个原生库可以很快完成序列化。我们需要在预期的类上面添加`@Serializable` 注释。看一看:

```
**@Serializable
data class SimpleExample**(**val** data : **String**,
                         **var** optionalData : **String** = "empty")
```

仅此而已；您不需要像处理常规库那样用可序列化标签来注释每个变量。这很简单。现在让我们看一个更真实的例子，关于[改型 2](https://square.github.io/retrofit/) 。

# 用 kotlinx-serialization 改进 2

我们都熟悉用于 [RxJava](https://github.com/ReactiveX/RxJava/wiki) 、[协程](https://kotlinlang.org/docs/reference/coroutines-overview.html)、Moshi 和其他库的改型适配器，对吗？同样，Kotlin 序列化也有一个来自 [JakeWharton](https://github.com/JakeWharton/retrofit2-kotlinx-serialization-converter/commits?author=JakeWharton) 的适配器，通过它我们可以将改型响应与 Kotlin 序列化代码链接起来。

要将这个库集成到您的项目中，请在应用程序级`build.gradle`文件中的`dependencies` 节点下添加以下代码行:

```
implementation("**com.jakewharton.retrofit:retrofit2-kotlinx-serialization-converter:0.5.0**")
```

现在是时候将适配器与改造实例联系起来了。看看下面的代码:

假设结果数据类被标注了`@Serializable`标签，这就是我们需要做的。其余的将由 Kotlin 序列化转换器处理。

# 隐藏功能

## 编译时安全

这是在你做一些复杂的序列化时使用的，比如你在响应数据类中嵌套了类。看一看:

```
**@Serializable
data class SimpleExample**(**val** data : **String**,
                         **var** optionalData : **String** = "empty",
                         **var complexClass**: **ComplexClass** )
```

你已经用`@Serializable`注释了`SimpleExample`数据类，这很好，但是如果你忘记注释`ComplexClass`该怎么办呢？这会导致运行时崩溃吗？或者你已经自己明确地检查了每个嵌套类是否被注释了？

放松；上述灾难都不会发生，因为 Kotlin 序列化库是编译时安全的，这意味着如果您没有用`**@**Serializable` **、**注释任何嵌套类，无论树结构有多深**，它都会显示一个错误。**

## 瞬态和可选注释

**Transient:** 通过将数据类中的一个变量注释为`@Transient`，我们指示序列化程序完全忽略这个字段。

**可选:**通过将数据类中的一个变量注释为`@Optional`，我们向序列化程序表明这个变量是可选的，所以如果在响应中没有找到它，不要中断流程。此外，我们可以分配一个默认值，如下所示。

这两个注释很有意义，我不知道 Java 库是怎么漏掉的。

# 奖金

要了解更多关于 Kotlin 的信息，请阅读 Kotlin 高级编程系列的前几部分:

*   [“使用 Kotlin 进行高级编程](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb)”
*   [“用 Kotlin 进行高级编程(第二部分](https://medium.com/android-dev-hacks/advanced-android-programming-with-kotlin-part-2-aae2a15258b0))”
*   [“用 Kotlin 进行高级编程(第三部分](https://medium.com/better-programming/advanced-programming-in-kotlin-2e01fbc39134))”
*   [“kot Lin 中的高级 Android 编程(第四部分)”](https://medium.com/better-programming/advanced-android-programing-in-kotlin-part-4-187b88fea048)

要了解更多关于 Kotlin 协同例程和 Kotlin 的其他高级特性，请阅读以下文章:

*   [“科特林协程，从基础到高级](https://medium.com/better-programming/kotlin-coroutines-from-basics-to-advanced-ad3eb1421006)”
*   [“使用新 Kotlin 流程进行异步数据加载](https://medium.com/better-programming/asynchronous-data-loading-with-new-kotlin-flow-233f85ae1d8b)
*   [“探索 Kotlin 中的集合和序列”](https://medium.com/better-programming/exploring-collections-and-sequences-in-kotlin-3a324ea08fb9)

在我们结束之前，我要感谢列昂尼德·斯塔策夫。我从他在 KotlinConfig 2019 的演讲中学到了很多我在这里提到的概念。

感谢您的阅读。
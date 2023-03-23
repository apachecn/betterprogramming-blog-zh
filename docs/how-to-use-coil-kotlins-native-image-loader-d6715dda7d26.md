# 如何使用 Coil，Kotlin 第一图像加载器

> 原文：<https://betterprogramming.pub/how-to-use-coil-kotlins-native-image-loader-d6715dda7d26>

## Android 上更好的图像加载

![](img/2340d22ca17ad6f6c77c901e8ec766e5.png)

阿诺德·弗朗西斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 这篇文章的要点

在本文结束时，您将学习如何使用 Instacart 团队的新图像加载器。除了一般用法，您还可以获得关于 Coil 的转换、请求取消和图像采样特性的知识。最后但同样重要的是，有一个从格莱德和毕加索到线圈的迁移指南。

# 介绍

[Coil](https://coil-kt.github.io/coil/) 是来自 Instacart 团队的新图像加载器，使用了许多高级功能，如 coroutines、OkHttp 和`androidX.lifecycle`s。Coil 为您的 APK 添加了大约 1500 个功能，与 [Picasso](https://square.github.io/picasso/) 相当，比 [Glide](https://bumptech.github.io/glide/) 和 [Fresco](https://frescolib.org/) 要少。Coil 还包括几个高级特性，如图像采样、有效的内存使用和自动取消/暂停请求。

默认情况下，Coil 完全兼容 R8 优化技术，因此开发人员无需添加任何与 Coil 相关的 ProGuard 规则。

# 综合

Coil 需要 Java 8 字节代码，所以在您的`build.gradle`文件中添加以下几行来使它工作。

线圈有四个工件；各有各的用途。但我们主要使用以下两种:

1.  `io.coil-kt: Coil`:默认工件，有`Coil` 单例。如果您没有使用依赖注入或者在应用程序中维护任何 Coil 实例，那么最好使用这个构件。
2.  `io.coil-kt:coil-base`:基础神器，**不**包含`Coil`单体。如果您使用依赖注入来注入线圈实例，那么最好使用这个工件。

# 功能

既然我们已经成功地集成了库，是时候让 Coil 投入使用了。让我们从加载一个简单的远程映像开始。看一看:

```
imageView.**load**("https://www.example.com/image.jpg")
```

由于 Coil 是 Kotlin 开发的第一个图像加载器，它利用了许多高级特性，其中之一就是扩展。`load` 是来自 Coil 的一个`extension` 函数，这使得加载远程镜像比任何其他库都简单。

有了`load` 扩展，Coil 节省了我们传递上下文和创建`imageloader`实例的时间。看看线圈的延伸部分:

它利用了从`imageView` 获取上下文并创建线圈加载器的杠杆作用。这就是为什么本地语言库更好的原因。

## 占位符和错误图像

几乎所有像 Glide、Picasso 和 Fresco 这样的库都有这个特性——在原始图像载入视图之前显示一个虚拟图像(占位符),如果请求失败则显示一个错误图像(错误)。Coil 也有开箱即用的特性，令人兴奋的部分是语法:实现它们更多地是沉浸在语言语法中，如 Kotlin 中的`let`和`apply` 特性。看一看:

## 预加载图像

Coil 利用协程更有效地下载图像。我们可以使用`get`暂停功能下载远程图像。看一看:

```
val image = Coil.**get**(imageUrl)
```

通过`get`暂停功能，我们可以下载一个远程镜像作为`drawable`。

## 复试

在某些情况下，您需要在下载远程映像后进行回调。线圈用`targets`功能盖住它。看一看:

有三种类型的目标:`Target`、`ViewTarget`和`possibleViewTarget`。

*   `Target` 在请求没有链接到任何视图时使用。
*   当请求链接到一个`imageview`时，使用`ViewTarget` ，比如显示一个占位符，直到请求完成。
*   如果需要位图池，使用`possibleViewTarget` 。

# 转换

现在我们已经完成了来自 Coil 的不同类型的图像加载技术，是时候探索转换了。变换是任何图像加载器都应该包含的一个方便的功能。开箱即用，线圈自带四种变换:[模糊](https://coil-kt.github.io/coil/api/coil-base/coil.transform/-blur-transformation/)、[圆形裁剪](https://coil-kt.github.io/coil/api/coil-base/coil.transform/-circle-crop-transformation/)、[灰度](https://coil-kt.github.io/coil/api/coil-base/coil.transform/-grayscale-transformation/)、[圆角](https://coil-kt.github.io/coil/api/coil-base/coil.transform/-rounded-corners-transformation/)。下面的代码演示了用法:

# 取消请求

有效利用资源是应用程序性能的关键。默认情况下，当`imageview`被分离，或者上下文被破坏，或者另一个请求被视图启动时，Coil 会取消请求。默认情况下，Coil 涵盖了所有内存泄漏的情况，但是当您想要取消请求时会出现这种情况。为此，Coil 为每个加载请求返回`[RequestDisposable](https://coil-kt.github.io/coil/api/coil-base/coil.request/-request-disposable)`，通过它您可以取消请求。看一看:

```
val **disposable** = imageView.**load**("https://www.example.com/image.jpg")

// Cancel the request.
disposable.**dispose**()
```

# 图像采样

图像采样是最先进的技术，用于根据`imageview`的大小加载高质量的图像。假设磁盘上有一个大小为 500 x 500 的图像。最初，线圈加载 100 x 100，并将其用作占位符，直到加载全部质量。它作为一个[渐进 JPEG](https://www.liquidweb.com/kb/what-is-a-progressive-jpeg/) 工作。我们可以在通过`crossfade`功能加载来自 Coil 的任何`imageloader`请求时启用此功能。看一看:

```
imageView.**load**(imageUrl)**{
    crossfade**(true)
**}**
```

# 从格莱德/毕加索的迁移

线圈是一个易于使用的 Kotlin 原生图像加载器。它涵盖了所有的基本和高级用法，并通过扩展使其对开发人员来说更简单。现在，让我们比较 Glide、Picasso 和 Coil 之间的图像加载代码。

## 基本用法:

```
// Glide
**Glide**.with(context)
    .load(url)
    .into(imageView)

// Picasso
**Picasso**.get()
    .load(url)
    .into(imageView)

// Coil
imageView.**load**(url)
```

## 回拨请求

## 自定义请求

# 奖金

要了解更多关于 Kotlin 的信息，请阅读 Kotlin 高级编程系列的前几部分:

*   [“使用 Kotlin 进行高级编程](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb)
*   [“使用 Kotlin 进行高级编程—第二部分](https://medium.com/android-dev-hacks/advanced-android-programming-with-kotlin-part-2-aae2a15258b0)”
*   [“用 Kotlin 进行高级编程—第三部分](https://medium.com/better-programming/advanced-programming-in-kotlin-2e01fbc39134)”
*   [“kot Lin 中的高级 Android 编程—第四部分”](https://medium.com/better-programming/advanced-android-programing-in-kotlin-part-4-187b88fea048)

要了解更多关于 Kotlin 协同例程和 Kotlin 的其他高级特性，请阅读以下文章:

*   [“科特林协程，从基础到高级](https://medium.com/better-programming/kotlin-coroutines-from-basics-to-advanced-ad3eb1421006)”
*   [“如何使用 Kotlin 密封类进行状态管理”](https://medium.com/better-programming/how-to-use-kotlin-sealed-classes-for-state-management-c1cfb81abc6a)
*   [“使用新 Kotlin 流进行异步数据加载](https://medium.com/better-programming/asynchronous-data-loading-with-new-kotlin-flow-233f85ae1d8b)
*   [“探索 Kotlin 中的集合和序列”](https://medium.com/better-programming/exploring-collections-and-sequences-in-kotlin-3a324ea08fb9)
*   [为什么以及如何使用 Kotlin 的原生序列化库](https://medium.com/better-programming/why-and-how-to-use-kotlins-native-serialization-library-c88c0f14f93d)
*   [了解如何组合 Kotlin 流](https://medium.com/@sgkantamani/learn-how-to-combine-kotlin-flows-317849a71d3e)

感谢您的阅读。
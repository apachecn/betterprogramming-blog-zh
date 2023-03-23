# 介绍 Pixel:一个新的用于 Android 的 Kotlin 图像加载库

> 原文：<https://betterprogramming.pub/introducing-pixel-a-new-kotlin-image-loading-library-for-android-a37a7a192f73>

## 一个可靠的，现代的，轻量级的方式来加载图像到您的 Android 应用程序中

![](img/bc790584ba943e94d0ce21a64146effb.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[亚瑟·马子](https://unsplash.com/@arthurbizkit?utm_source=medium&utm_medium=referral)拍摄的照片。

我非常激动和兴奋地宣布 [Pixel](https://github.com/mmobin789/pixel) ，这是一个基于 Kotlin 的 Android 开源图像加载库。它可靠、现代、轻便。

技术栈:Kotlin 协同程序、OkHttp、Okio 和 AndroidX。

# 一个新的 Kotlin 图像加载库

使用 Kotlin 协同例程进行结构化并发和线程化的想法部分来自另一个类似的库， [Coil](https://coil-kt.github.io/coil/) ，这是第一个完全在 Kotlin 中构建的图像加载库。这是第二个。

显示图像是许多 Android 应用程序的重要组成部分，开发人员通常很难根据其大小和对应用程序的影响来选择一个库——即使不考虑其性能和优势。要考虑的常见因素有 APK 规模、样板代码或其生成，以及较慢的构建时间。这就是 Pixel 介入的地方。

以下是 Pixel 的一个示例:

Pixel 相当新，还在定期开发中，所以目前只支持从互联网上加载图像。

# 为什么是 Pixel？

有许多图像加载库，但是 Pixel 基于图像视图的大小(宽度 x 高度)以像素为单位加载图像——因此得名。它的创建是为了满足 Android 中更简单的图像加载的确切目的。这些是它的核心特征:

*   Kotlin-first: Pixel 内置于 Kotlin 中，利用其惯用法、内联、扩展和 lambda 方法来创建一个简单灵活的 API。
*   Kotlin 协同程序:Pixel 使用它们的原因和 Coil 一样。
*   可靠:Pixel 的技术堆栈是 OkHttp 和 Okio，因为它们是大多数依赖 I/O 操作的应用程序的标准。它还具有 AndroidX 功能，可以与 Android 生命周期保持同步。
*   响应式 UI: Pixel 响应主线程的状态。它确保主线程上请求的任何图像加载都服从于主线程的可用性。为此，它使用提供的 imageView，并在任何其他主线程状态可用的情况下将负载发送给它。
*   签名下载:每张图片加载都是唯一的。Pixel 通过跟踪和识别每次加载来执行优化，这样就不会下载具有相同参数(大小)的相同图像。
*   轻量级:**Pixel 背后的另一个主要目的是在不损害其性能的情况下实现极其轻量级，以避免其依赖性成为考虑的相关因素。为了确保严格遵守单一责任原则，Pixel 进行了微型优化。不会添加任何额外的功能。一些例子是在 imageView 上应用动画，修改 imageView，或者在 imageView 上应用它的属性(可以并且应该从客户端设置)。此外，没有注释处理来避免降低构建速度。**
*   **可测试:Pixel 主要使用单例工件，它可以被包装来创建模拟测试。**

# **入门指南**

**我非常高兴能与社区分享[像素](https://github.com/mmobin789/pixel)。repo 包含 quickstart，而完整的文档将很快添加。敬请期待！此外，任何相关的改进建议都是受欢迎的，可以在 GitHub 上[报告。](https://github.com/mmobin789/pixel/issues)**
# 颤振可能是也可能不是下一个大事件，但 Kotlin 多平台已经存在

> 原文：<https://betterprogramming.pub/flutter-may-or-may-not-be-the-next-big-thing-but-kotlin-multiplatform-is-here-to-stay-baf1a44a692d>

## Flutter 的崛起不应该让本地 Android 开发者担心

![](img/84dcab5743c2bb581b1fced246611ba0.png)

马克·赖歇尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

谷歌的 Flutter 框架在过去的一年里获得了巨大的动力。它已经成为人们谈论的话题，有些人把它吹捧为下一个大事件，而另一些人则把它当作又一项闪亮的新技术。

虽然 Flutter 的崛起对于跨平台应用开发来说是一个好兆头，但它让原生 Android 开发者感到困惑。许多刚开始创业的人发现自己进退两难:

*   他们应该坚持使用原生 Android 和 Kotlin 还是切换到 Flutter 和 Dart？
*   谷歌会为了 Flutter 而扼杀原生 Android 开发吗？

考虑到原生 Android 和 Flutter 框架都是来自谷歌自己的武器库的技术，在它们之间做出决定并不是一件容易的事情。

谷歌对 Flutter 的不懈支持只会让 Android 开发者更加担心和害怕 Kotlin 在生态系统中的未来。

在我谈论 Android 开发者的未来之前，我想讨论两篇令人惊叹的文章，它们对 Flutter 的未来提出了截然不同的观点。

以下是他们的简要总结:

*   [Michael Long](https://medium.com/u/ba1bf5213360?source=post_page-----baf1a44a692d--------------------------------) 声明 Flutter“[不是下一件大事](https://medium.com/better-programming/why-flutter-isnt-the-next-big-thing-e268488521f4)”，并提出了一些有效的观点。这是真的，Flutter 在 iOS 上永远是二等公民，谷歌比任何人都更快地扼杀产品和技术。
*   埃里克·范·巴伦说“事实上，T10 是下一件大事”。他以 Dart 与 Fuchsia 的兼容性(也许是 Android OS 的未来)的形式提出了一个很好的反驳。

这场辩论如此有趣，以至于我们中可能没有人能够决定 Flutter 是否真的是下一个大事件。

提及这些文章背后的想法并不是在这场扑朔迷离的辩论中火上浇油。相反，我想将重点转移回原生 Android 开发。

Flutter 受欢迎程度的迅速上升无疑给 Android 开发者的头脑带来了疑问，因为没有人在谈论 Kotlin 的未来。

我们可能知道也可能不知道 Flutter 是否是应用开发中的下一件大事。尽管如此，Kotlin 今天提供了多平台支持，这意味着人们可以运行代码而不管底层架构。这确实是革命性的，我想偷偷把它称为下一件大事。

值得庆幸的是，Kotlin 的平台独立支持意味着 Android 开发者不必太担心。尽管移动开发者社区对 Flutter 赞赏有加，但不管 Flutter 和 Fuschia 的未来如何，Kotlin 都将留在这里。

在我们深入挖掘 Kotlin 充满希望的未来之前，让我们后退一步，分析一下谷歌当初为什么要引入 Flutter(原生 Android 的竞争技术)。

# Flutter 的推出并不是为了杀死原生 Android。相反，它是来未雨绸缪的

对于外行人来说，Flutter 是谷歌的跨平台框架，用于构建运行在 Android、iOS、web 和桌面上的应用程序。它使用 Google 的 Dart 语言，并且不提供任何与 Java 虚拟机(JVM)的兼容性。

谷歌引入一个独立框架的想法并不是真的要与它自己的原生 Android 工具竞争。相反，它希望对冲 Android，因为甲骨文给了他们噩梦。

对于那些不熟悉的人来说，甲骨文和谷歌多年来一直因在 Android 设备中使用某种 Java API 和 JVM 而陷入法律纠纷。

基于 JetBrains 的 Kotlin 的引入及其随后宣布成为 Android 开发的首选语言并没有真正解除谷歌的担忧，因为 kot Lin——很像 Java——仍然需要 JVM 来运行(那时，Kotlin/Native 还处于萌芽状态)。

因此，为了获得对其软件生态系统的完全控制，谷歌引入了 Flutter 框架和 Fuschia 操作系统。Dart 是一种编译成本机代码的语言，因此不需要依赖 Oracle 的 Java 虚拟机。

与此同时，Fuschia OS 可能是谷歌远离基于 Linux 的设备并确保 Android 开发的未来与 Flutter 保持一致的努力。

但这并不意味着目前的许多 Kotlin 开发人员陷入困境。

# Kotlin 多平台在移动应用程序开发方面有着光明的前景

Kotlin for Android 为全球开发者所熟知，但这只是 JetBrains Kotlin 多平台项目的一个方面。

其他子集包括 Kotlin/JS(它产生 Kotlin 代码)、Kotlin 标准库以及任何与 JavaScript 兼容的依赖项。

但最重要的是，有 Kotlin/Native 用于将 Kotlin 代码编译成本地二进制文件，它可以在没有虚拟机的情况下运行。这实际上意味着您可以将 Kotlin 代码用于 iOS Linux、macOS、Windows 和其他嵌入式设备。

关于 Kotlin/Native，需要注意的关键一点是，它不使用公共 UI，只重用业务逻辑模块。这意味着如果你使用 Kotlin/Native 构建 iOS 应用，你只需要编写特定的平台 API 和/或 UI 代码(SwiftUI/UIKit)。

虽然坏消息是，目前这并没有真正将 Kotlin/Native 定位为 Flutter 的替代品，但它确实确保了你可以构建 100%的原生应用程序——这是任何跨平台都无法实现的。

然而，Kotlin 支持多个运行时目标的能力——很像 Dart——确保 JetBrains 的语言不会消失，并将继续成为 Android 开发者的重要齿轮。

事实上，Jetpack Compose (Android 的声明式 UI 框架)强烈表明了未来对平台无关的支持，这使得 Kotlin Multiplatforms 成为跨平台应用开发的竞争者。

最后，如果 Fuschia 操作系统真的取代了 Android，Kotlin 的开发者还会在。Android 是一个比 Flutter 大得多的生态系统，如果 JetBrains 增加了 Kotlin-to-Dart transpiler 支持，从而确保 Kotlin 瞄准未来的操作系统，人们不会感到惊讶。

# 外卖食品

尽管 Flutter 越来越受欢迎，但原生 Android 开发者不必过于担心。

Kotlin 多平台不仅解决了 Android-JVM 的困境，还确保了本地应用程序开发的存在，尽管存在大量的跨平台框架。

网飞最近展示了 Kotlin 多平台确实已经可以生产了。

所以:

*   如果你是原生 Android 开发者，坚持使用 Kotlin。
*   如果你是一个了解 Kotlin 的 Android 开发人员，并且希望涉足原生 iOS 生态系统，那么使用 Kotlin Multiplatform。
*   如果你想玩跨平台框架，使用 Flutter。

不管 Flutter 的未来如何，Kotlin 或原生 Android 开发都不会有任何进展。
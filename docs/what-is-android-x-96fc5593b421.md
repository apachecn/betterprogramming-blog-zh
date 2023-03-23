# AndroidX 是什么？

> 原文：<https://betterprogramming.pub/what-is-android-x-96fc5593b421>

## 这个新的支持库是什么，为什么 Android 世界需要它？

![](img/4ff28b160595d1d71db3fd12e7578a35.png)

由[埃里克·索尔海姆](https://unsplash.com/@eirikso?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/android-cellphone?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

无论你是一名经验丰富的 Android 开发人员，还是该平台的新手，如果你最近编写了 Android 代码，很可能你已经到处看到了 AndroidX。如果你和我一样，很可能你没有仔细研究这个术语背后的细节，或者它和你应用程序的其他导入之间的区别，比如支持库。但如果你在这里，你很可能想知道安卓系统背后的秘密。希望能澄清你对它的一些疑惑。

重要的事情先来。AndroidX 代表 *Android E X 张力库*。这个简单的外延让有经验的开发人员更清楚术语背后的含义。但是如果你不是这样，不要担心，我将很快详细介绍。

在深入研究之前，我想探索一下在 Android 文档中搜索 AndroidX 时找到的第一个定义:

> *`*androidx*`*名称空间内的工件由*[*Android Jetpack*](https://developer.android.com/jetpack)*库组成。与支持库一样，* `*androidx*` *命名空间中的库独立于 Android 平台，并提供跨 Android 版本的向后兼容性。”—* [*AndroidX 概述*](https://developer.android.com/jetpack/androidx)*

*我认为这有点难读，而且包含了大量的术语，很难概括。在这篇文章的最后，你将能够回到这个定义，知道他们想说什么。*

# *Android 版本背景*

*Android 有一套特殊的操作系统版本控制方法。目前(2020 年 1 月)Android 已经有十个主要版本。Android 的最新版本是 Android 10(简单不是吗？)，于 2019 年 9 月 3 日向公众发布。这是本该被称为 Android Q 的版本，之前所有的 Android 版本都是以字母顺序命名为一种甜点:Android 1.5 纸杯蛋糕，Android 4.0 冰淇淋三明治，Android 9.0 馅饼。但是 Android 10.0 是个例外，因为相应的甜点必须用 Q 来命名，你能想出一个带 Q 的名字吗？嗯，Android branders 显然不能或没有找到任何足够好的，所以这最后一个版本只是 Android 10。*

# *什么是支持库？*

*在他们定义的第二句话中，我们可以找到下面的文字:“像支持库一样，AndroidX 命名空间中的库……”由此，我们注意到他们假设我们都知道支持库是什么。也许你知道，但是万一你不知道，让我们回顾一下。*

*每个 Android 版本都有不同的功能和新增功能。回想一下 2008 年 Android 的起源，智能手机已经发生了很大的变化，这意味着它们添加和删除了大量的功能。一开始，它就像从物理键盘迁移到数字键盘一样简单，直到高级功能，如人工智能助手，又名“Ok Google”。在 Android 9.0 发布之前，我们使用所谓的支持库来使我们的应用程序兼容所有版本。这些库管理不同版本中可用或不可用的功能，并帮助开发人员在所有设备上创建可用的应用。*

# *为什么需要支持库？*

*如果你是 Android 用户，你可能知道当你购买你的设备时，它会得到一些更新，但最终，你不会得到新版本的 Android。这部分是由硬件限制等因素造成的。这是另一篇文章更大的主题。*

*这里重要的是，不同的 Android 版本可能对集成在操作系统中的库、方法和功能有不同的名称，而不会在你的应用 APK 中提供。因此，为了确保编译器理解您的代码，并在所有设备中执行相同或相似的功能，支持库会控制并为我们执行这项工作。你的应用附带了支持库，与 Android 版本无关。*

*如果你想更深入地了解支持库的细节，可以看看 Android 关于这个主题的文档:[https://developer . Android . com/topic/libraries/Support-Library](https://developer.android.com/topic/libraries/support-library)*

*![](img/6bead9a06532233abaca9b75e59a8b6a.png)*

*照片由[乔恩·泰森](https://unsplash.com/@jontyson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/question-mark?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄*

*到目前为止，我们已经澄清了 Android 文档的第二句话:“像支持库一样，`androidx`命名空间中的库与 Android 平台分开发布，并提供跨 Android 版本的向后兼容性。”好样的。希望这些听起来更容易理解。*

*我们可以继续探索第一句话:“名称空间中的工件包含了 [Android Jetpack](https://developer.android.com/jetpack) 库……”*

# *Android Jetpack 是什么？*

*首先提到的是 Android Jetpack。这本身就是一个大话题，但话说回来，让我们看看 Android 文档和它们的定义:*

> **“Jetpack 包含一系列 Android 库，这些库融合了最佳实践，并在您的 Android 应用中提供向后兼容性。”—*[*Android Jetpack 入门*](https://developer.android.com/jetpack/docs/getting-started)*

*![](img/97ad14f61da573fecab501c11814f114.png)*

*照片由[马修·施瓦茨](https://unsplash.com/@cadop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/skeleton?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄*

*至少这里的定义更容易阅读，但是你和我想的一样吗？这个定义听起来就像我们在描述支持库一样。如果我们是在选择题考试中，选项是 a. Jetpack 和 b. Support Library，老实说，我不知道该选哪个。(我希望会有一个 c。以上都有)。*

*那么新的问题来了:支持库和新的 Jetpack 有什么区别？基本上，Jetpack 组织得更好，并且在不同设备之间更加一致。在开发人员看来，Jetpack 是对所有支持库的重构。为什么我说全部？因为如果你看看支持库，它们有不同的版本(v4、v7、v12 等。)这让它们变得非常混乱，变得一团糟。有了这个改变，你就不用担心版本了，因为 Jetpack 知道该用什么代码。本质上是一样的，只是从外表上看，都被浓缩了，对开发者来说变少了工作。*

*从上面我们可以看到，任何以单词 AndroidX 开头的库都是 Jetpack 的一部分。*

*希望这篇文章回答了让你来到这里的问题。在我看来，有两个独立的术语，Android Jetpack 和 AndroidX，有点令人困惑。即使理解我们日常使用的东西背后的所有逻辑不是必要的，关于我们主题的知识将帮助我们成为更好的开发者。*

*下次见！*
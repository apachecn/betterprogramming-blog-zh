# Android 中文本阴影的丑陋真相

> 原文：<https://betterprogramming.pub/the-ugly-truth-about-text-shadowing-in-android-776816cd9970>

## 未记录的限制

![](img/5268c44ebfed5c36727044afca1fc3a9.png)

[张家瑜](https://unsplash.com/@danielkcheung?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

几乎所有的移动应用程序都包含文本。它占据了用户界面(UI)的很大一部分。这项研究表明[文本内容覆盖了一些最受欢迎的 iOS 应用程序 UI](https://www.intercom.com/blog/text-in-mobile-app-design/) 的 30-40%。

因此，在构思用户界面时，你应该关注文本的可读性。

我们举个例子。您可能想要在图像上显示一些文本。你有没有考虑过你的文字颜色可以和图像的背景融合在一起？如果没有，很可能你的用户不能总是阅读它。

![](img/73c7d692d7d92e917860dde57989c400.png)

标题融合了米色背景和米色。

大多数时候，你可能无法控制你正在加载的图像。然后由您提供渲染机制来突出显示文本内容。

首先，你可以考虑使用阴影属性。它会在文本下面产生一个彩色的覆盖层，与图像的背景形成对比。

![](img/4c8b28e7bc10e08db1989bdc6289f89e.png)

有阴影时可读性更好。

在 Android 中，`TextView` API 提供了现成的[隐藏属性](https://developer.android.com/reference/kotlin/android/widget/TextView#android:shadowcolor)。您可以指定:

*   一种颜色
*   半径——阴影的显著程度
*   偏移(水平和垂直)

所有这些属性看起来都很简单。然而，有一些限制，你可能不知道半径。

# 阴影半径限制

根据[文档](https://developer.android.com/reference/kotlin/android/widget/TextView#android:shadowradius)，半径应该是一个浮点数。你也可以注意到它指的是模糊。在移动应用程序中，出于性能原因，我们通常建议使用模糊处理。

那么什么才算是合理的半径呢？事实证明，这不仅取决于你的硬件，还取决于你的操作系统。

这主要取决于你的设备的渲染能力。此外，支持 API 23 及以下将崩溃你的应用程序，如果你使用的阴影半径超过 25。在不知道这一点的情况下，当你调试这个崩溃时，你将处于痛苦的世界中。事实上，您不会得到任何堆栈跟踪。这是您从错误控制台获得的唯一帮助:

```
RS CPP error: Blur radius out of 0-25 pixel bound
RS CPP error (masked by previous error): Allocation creation failed
RS CPP error (masked by previous error): Allocation creation failed
RS CPP error (masked by previous error): Blur radius out of 0-25 pixel bound
A/libc: Fatal signal 11 (SIGSEGV), code 1, fault addr 0x28 in tid 10781 (RenderThread)
```

虽然控制台声明模糊半径发生了一些错误，但你必须自己找出它来自于你的文本阴影。你不会知道它在哪里坠毁或为什么坠毁。

在 API 23 之上，我没有观察到这个问题——尽管你应该记住性能。

# 这都是关于硬件的

Android 配备了大量不同的设备。它们没有相同的规格。

当谈到添加模糊效果时，系统依赖于[硬件加速](https://developer.android.com/guide/topics/graphics/hardware-accel)来优化渲染。为了避免这个崩溃，应用一个更大的阴影半径，你可以决定[依靠软件渲染](https://stackoverflow.com/questions/37182062/how-to-create-a-large-blurred-shadow-behind-a-textview)。然而，它将极大地影响您的性能渲染，而这已经受到硬件加速的威胁。

请记住，不是所有的用户都拥有最新的旗舰产品。只要你知道 Android 中文本阴影是如何工作的，你就应该能够在可读性和性能之间找到适当的平衡。
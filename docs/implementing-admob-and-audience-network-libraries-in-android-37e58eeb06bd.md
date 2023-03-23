# 在 Android 中实现 AdMob 和观众网络库

> 原文：<https://betterprogramming.pub/implementing-admob-and-audience-network-libraries-in-android-37e58eeb06bd>

## 借助这些 SDK 提高您的 Android 应用的广告收入

![](img/664aef5f4a06e5444eeb0cdb7936c32b.png)

[努诺·安图内斯](https://unsplash.com/@onun?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 这篇文章的要点

您将学习如何在您的 Android 应用程序中实现来自广告公司的奖励广告，如 [AdMob](https://developers.google.com/admob) (来自谷歌)和脸书。本文还包含了我在五年的移动应用程序广告经验中学到的最佳实践。

本文可能会集中讨论如何在 Android 应用程序中显示广告，但是对于如何跨平台工作的概述是相似的。例如，在移动应用程序中，广告公司使用设备 id 来识别特定用户，而在 web 上，他们使用 cookies。

# 介绍

在移动应用程序中显示广告来赚钱并不是一种新的盈利策略。这是广泛使用的从任何移动应用程序或网站赚钱的方法之一。

通常，移动应用开发者更喜欢显示横幅广告和间隙广告。它们确实产生了可观的收入，但大多数奖励广告比传统广告，如插播广告，赚取更多的收入。

如果这是您第一次在应用程序中实现广告，我强烈推荐您阅读下面的文章，了解基本的广告类型和最佳实践。

[](https://medium.com/android-dev-hacks/implementing-ads-in-your-android-application-28ac676024aa) [## 在您的 Android 应用中实现广告

### Admob、脸书和 StartApp

medium.com](https://medium.com/android-dev-hacks/implementing-ads-in-your-android-application-28ac676024aa) 

使用奖励广告对每个人来说都是一种双赢的方法。用户必须观看一段时间的广告才能获得奖励，这样广告商就可以确定用户使用了这些信息。同时，用户可以了解新的应用、产品等。，详细。最后，对于开发者来说，如果用户获得了回报，那么像 AdMob 和脸书这样的广告公司就能赚到钱。这意味着开发商也赚了一大笔钱。

# AdMob 奖励广告

先说谷歌的广告平台 AdMob。您可以在 Google AdMob 页面[上创建一个 AdMob 帐户和必要的 id，以便在应用程序中使用。这个过程很简单，我就跳过了。如果你有任何疑问，请随意评论。](https://admob.google.com/home/)

## 综合

我更喜欢使用 Firebase 库，但是你也可以通过 play-services-ads 库显示 AdMob 奖励广告。要将 Firebase 广告集成到您的项目中，请在应用程序级别的`gradle`文件中添加以下代码块。

```
dependencies {
    ***implementation* ("com.google.firebase:firebase-ads:19.6.0")**
}
```

下一步是在`manifest`文件中添加必要的元数据，如下所示:

最后，是时候实施奖励广告了。首先，我们需要创建一个奖励广告实例。这可以通过调用`MobileAds`类中的`getRewardedVideoAdInstance`函数来完成。看一看:

AdMob 奖励广告实例创建

接下来，我们需要加载奖励广告。为此，我们需要 AdMob `AdRequest`实例和 reward ad `id`。在下面的代码片段中，我们使用了一个测试广告 ID，但实际上，您需要从您的 AdMob 帐户生成一个奖励广告 ID 并在这里使用。

AdMob 广告请求和加载奖励广告

`loadAd`函数是异步的；我们需要分配一个广告监听器来显示成功加载的奖励广告。现成的 Firebase Ads 提供了`RewardedVideoAdListener`来做到这一点。看看听者:

AdMob 广告监听器

`onRewardedVideoAdClosed`在广告关闭后执行；要么是用户强行关闭，要么是广告结束后关闭。

如果用户通过将应用程序移至后台或取消应用程序而离开应用程序，则执行`onRewardedVideoAdLeftApplication`。

`onRewardedVideoAdLoaded`在广告成功加载并准备显示后执行。

`onRewardedVideoAdOpened`奖励广告开始显示后立即执行。

`onRewardedVideoCompleted`在视频结束后执行。

一旦用户通过成功观看广告赢得奖励，就执行`onRewarded`。

如果加载广告时出现错误，则执行`onRewardedVideoAdFailedToLoad`。

现在我们知道了监听器回调是如何工作的，我们可以创建一个监听器并将其分配给一个奖励广告实例，如下所示:

显示 AdMob 广告

瞧啊。您刚刚在应用程序中实现了 AdMob 奖励广告，以增加您的广告收入。

# 脸书广告

下一站是脸书广告公司。我们需要在[脸书观众网络](https://www.facebook.com/audiencenetwork/solutions/overview?&utm_campaign=AN_SMB_Search_Google_Acquisition_APAC_IN_EN_Brand_Beta&utm_source=Google&utm_medium=paid_search&utm_term=%2Bfacebook%20%2Baudience%20%2Bnetwork&utm_device=c&gclsrc=aw.ds&&gclid=CjwKCAjwiMj2BRBFEiwAYfTbCt_p0Ahb2y7ViEHff_5IclDhIzE7K9HOS9aU_864i7leJPBApFDMTBoC3Z0QAvD_BwE&gclsrc=aw.ds)中创建一个帐户，然后创建脸书奖励广告 ID。在某种程度上，它类似于 AdMob ad ID 生成。不要再耽搁了，让我们开始吧。

## 综合

要包含脸书广告，我们需要将脸书观众网络库添加到您的项目中，如下所示:

```
implementation "**com.facebook.android:audience-network-sdk:5.+**"
```

一旦你完成了整合部分并生成了一个脸书奖励广告 ID，就该进入实施部分了。在创建任何广告对象之前，我们需要在应用程序类内部的`onCreate`函数上初始化受众网络 SDK。看一看:

首先，我们需要创建一个类似于 AdMob 广告实现的脸书奖励广告实例。看一看:

然后我们需要加载悬赏广告，如下图所示:

类似于 AdMob 奖励广告加载，脸书`loadAd`功能是异步的。所以我们需要一个回调来显示成功加载的奖励广告。看一看:

侦听器函数的工作方式类似于 AdMob 函数，所以我不再赘述。我们在`onAdLoaded`函数中显示广告，一旦广告成功加载，该函数就会调用。

就这样——你已经完成了脸书奖励广告！

# 最佳实践

*   **在你加载 AdMob 或脸书奖励时显示加载覆盖图**，然后在广告成功加载并准备好显示时关闭它。这样，你将避免不幸的广告点击，这是对广告政策的重大违反。
*   最好在之前**加载你想要显示的广告。这样，用户在加载广告时就不需要等待了。**
*   **不显示应用程序之外的广告。**如果你的应用程序与后台运行的应用程序或一些实用程序相关，不要在应用程序之外显示广告。这是主要的违规行为之一。解决方法之一是显示一个弹出窗口，将用户导航到应用程序，然后显示广告。

目前就这些。希望你学到了有用的东西，感谢阅读。
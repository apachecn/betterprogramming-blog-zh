# 关于安卓即时应用你应该知道的一切

> 原文：<https://betterprogramming.pub/everything-you-should-know-about-instant-apps-on-android-2ea83b18dc8e>

## 无需在手机上安装应用程序，即可提供即时应用程序体验

![](img/985e7944b9e51db02fd3300598e59b9f.png)

[自由股票](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 从文章中摘录

在本文中，您将学习如何为您的 Android 用户创建即时应用程序。除此之外，您还将了解如何提示用户安装完整版本的应用程序，测试即时应用程序的正确方法，如何使用带有 URL 的即时应用程序，等等。请继续阅读，了解它们。

# 介绍

*即时应用*是一种无需明确安装即可提供安卓应用原生体验的技术。

当您的 Android 应用程序具有即时应用程序功能时，用户将在 Google Play 商店的应用程序详细信息屏幕上看到“安装”旁边的“立即尝试”按钮。点击“立即尝试”按钮，一个虚拟应用程序就安装好了。

此时只会下载开发人员在清单文件中声明为即时应用的模块。这允许用户在安装整个应用程序之前简单体验应用程序。如果您的应用程序很大，此选项将提供应用程序的功能，而无需实际下载。

# 要记住的事情

现在我们知道了什么是即时应用，以及它如何对用户和企业主有所帮助。尽管它提供了许多好处，但也有一些限制。

1.  支持即时应用的应用捆绑包只能使用少数应用权限，如`[ACCESS_COARSE_LOCATION](https://developer.android.com/reference/android/Manifest.permission#ACCESS_COARSE_LOCATION)`、`[ACCESS_FINE_LOCATION](https://developer.android.com/reference/android/Manifest.permission#ACCESS_FINE_LOCATION)`、`[ACCESS_NETWORK_STATE](https://developer.android.com/reference/android/Manifest.permission#ACCESS_NETWORK_STATE)`、`[CAMERA](https://developer.android.com/reference/android/Manifest.permission#CAMERA)`、`[INSTANT_APP_FOREGROUND_SERVICE](https://developer.android.com/reference/android/Manifest.permission#INSTANT_APP_FOREGROUND_SERVICE)`、【仅在 Android 8.0 (API 等级 26 及以上)】、*、*、`[INTERNET](https://developer.android.com/reference/android/Manifest.permission#INTERNET)`、`[READ_PHONE_NUMBER](https://developer.android.com/reference/android/Manifest.permission#READ_PHONE_NUMBERS)`、`[RECORD_AUDIO](https://developer.android.com/reference/android/Manifest.permission#RECORD_AUDIO)`、`[VIBRATE](https://developer.android.com/reference/android/Manifest.permission#VIBRATE)`、`[WAKE_LOCK](https://developer.android.com/reference/android/Manifest.permission#WAKE_LOCK)`。
2.  默认情况下，即时应用程序无法与已安装的应用程序进行交互，除非满足以下条件之一:
    a .)已安装的应用程序中至少有一个活动在其清单文件中应具有标记为`true`的属性`android:visibleToInstantApps`。
    b .)****已安装的 app 包含一个`[CATEGORY_BROWSABLE](https://developer.android.com/reference/android/content/Intent#CATEGORY_BROWSABLE)`意图过滤器。****
3.  ****即时应用功能建立在 Android 应用捆绑包的基础上，使用 Android Lollipop 中引入的 split APKs 功能。因此，开发人员需要将他们的应用程序分发从传统的 APK 改为 Android 应用程序捆绑包，以便使用即时应用程序。****

# ****综合****

****通过在应用程序级`build.gradle`文件的依赖关系节点下添加以下代码行，将 Google Play Instant API 包含在您的应用程序中。****

```
**implementation "com.google.android.gms:**play-services-instantapps**:17.0.0"**
```

****如果你正在使用任何以前的即时应用插件，你可以删除它们。新的即时 API 不需要任何插件，有了这个新的 API，你可以对即时和完整版本的应用程序使用一个代码。****

# ****实现即时体验****

## ****版本代码维护****

****你的 app 即时体验的版本代码应该小于 app 的实际版本代码。这是因为当用户从即时应用程序转移到完整应用程序安装时，Google Play 会将其视为应用程序更新。所以版本代码的升序是有意义的。****

****即使用户已经安装了应用程序，他们也可以通过 URL 来体验即时应用程序，稍后您会看到这一点。但在此之前，你应该小心版本代码——在任何情况下，即时应用程序应该有更少的版本代码。****

****谷歌的建议是大幅增加可安装 APK 的版本号，比如 1000，以确保有足够的空间让你的即时体验版本号增加。****

## ****沙盒版本****

****正如我所说，即时应用程序不会安装在设备中。相反，它们将从沙盒存储中操作。为此，我们需要在清单文件中包含`targetSandboxVersion`属性，如下所示:****

```
**<manifest
   xmlns:android="http://schemas.android.com/apk/res/android"
  ...
   **android:targetSandboxVersion="2"** ...>**
```

## ****在模块级别实现即时体验****

****为了使模块支持即时应用程序，我们只需要在清单文件中添加下面一行，如下所示。这在基础模块和打算包含在 instant-app 体验中的模块中是强制性的。****

```
**<manifest 
 xmlns:dist="http://schemas.android.com/apk/distribution">
    <**dist:module dist:instant="true"** .../>
    ...
</manifest>**
```

## ****带有即时应用程序的动态模块****

****我们也可以在即时应用中使用动态功能模块。创建支持即时应用的动态功能模块的最简单方法是使用 Android Studio 的模块模板。****

****要创建即时动态特征模块，导航如下:****

******文件>新>新模块******

****然后，将打开一个新窗口，其中包含与不同配置相关的不同模板。在列表中选择**T5*即时动态特征模块*选项，点击下一步。之后，您可以给模块命名，也可以给包命名。******

# ****URL 映射****

****如果你想在一个 URL 加载到手机上时提供即时应用程序体验，该怎么办？这是可能的，但是您应该实现以下事情来使它工作:****

1.  ****首先，您应该通过将`autoVerify` 设置为`true`来添加第二个意图过滤器。
    `<intent-filter android:**autoVerify**=”true”>`****
2.  ****然后，在上面创建的意图过滤器下添加以下类别和操作标志。
    `<action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <category android:name="android.intent.category.DEFAULT" />`****
3.  ****最后，我们需要添加 URL 模式，如下所示:****

```
**<data android:scheme="http"/>
<data android:scheme="https"/>
<data android:host="example.com"/>
<data android:path="/instantapp/schedule"/>**
```

****我们还可以指定一个默认 URL，用于更轻松地启动即时体验，如下所示:****

```
**<meta-data
    android:name="default-url"
    android:value="https://example.com/instantapp/experience"/>**
```

****这没什么可做的，但是当你想给别人演示的时候会很有帮助。这是可选的。****

# ****要求用户安装完整版****

****既然我们已经配置了即时体验，下一步就是鼓励用户安装实际的应用程序。为此，首先我们需要确认用户在即时体验中。我们可以通过使用`[isInstantApp()](https://developers.google.com/android/reference/com/google/android/gms/instantapps/PackageManagerCompat.html#isInstantApp%28%29)`方法来确定。如果运行过程是即时体验，这个方法返回`true`。****

****下一步是向用户显示一个提示，鼓励他们安装实际的应用程序。这可以按如下所示完成:****

# ****测试****

****无论做什么，测试都是让它稳定的核心事情之一。即时应用可以通过从 Android Studio 安装或使用内部测试功能从 Play store 下载来直接测试。****

## ****在本地设备中测试****

****要直接从 Android Studio 安装，请执行以下步骤:****

1.  ****首先，如果设备中已经安装了该应用程序，请将其卸载。****
2.  ****然后，导航到安装选项上的常规选项卡。选择“部署为即时应用程序”****
3.  ****然后点击运行按钮。****

## ****内部测试轨道****

****如果你不确定从 Play store 下载的即时应用程序如何工作，那么这是一个使用内部测试轨道测试它的绝佳时机。****

1.  ****首先，像往常一样生成一个 Android 应用捆绑包。****
2.  ****上传到内部测试轨道下，而不是发布管理中的生产下。****
3.  ****将您的测试人员的邮件 id 添加到测试列表中。****
4.  ****现在用其中一封电子邮件登录。当您在 Play store 中搜索应用程序时，您会看到“立即尝试”选项。****

# ****奖金****

****要了解有关动态交付和 Android 应用捆绑包的更多信息，请阅读以下文章:****

*   ****[“Android 中的动态交付—第一部分”](https://medium.com/android-dev-hacks/dynamic-delivery-in-android-part-1-634c686d7170)****
*   ****[“Android 中的动态交付—第二部分”](https://medium.com/android-dev-hacks/dynamic-delivery-in-android-part-2-b3999513bfc0)****
*   ****[“Android 中的动态交付—第三部分”](https://medium.com/android-dev-hacks/dynamic-delivery-in-android-part-3-3e1201c4955b)****

****我希望你学到了有用的东西——感谢阅读。****
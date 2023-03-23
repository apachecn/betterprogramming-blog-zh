# 如何在 Android 中设置生物认证

> 原文：<https://betterprogramming.pub/how-to-set-up-biometric-authentication-in-android-672688afcaae>

## 使用 Android 的生物识别 API 进行安全而简单的身份验证

![](img/705f6f2501ce465a46ad82483bd1bf95.png)

照片由[昆廷·拉加奇](https://unsplash.com/@quentinlagache?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/eye?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 这篇文章的要点是

生物认证是指纹认证的延伸。与它的前身相比，生物识别 API 要高级得多，也更容易集成。在本文中，您将学习如何集成生物识别 API 以及如何使用它。请继续阅读以了解它。

# 介绍

近年来，Android 团队的重点主要是提高安全性和隐私性。模式/pin 锁在 Android 中已经存在多年，作为一种增强，指纹认证在几年前被引入。现在是时候增强指纹认证了，这就是所谓的生物认证。

生物认证更加安全且易于使用，这在许多情况下都很方便，例如支付授权、安全简单的登录、访问敏感数据时的认证等等。

除此之外，生物特征 API 还提供了一种使用设备密码或 pin 进行身份验证的方法，无需开发人员做任何额外的工作。

# 检查可用性

正如我所说的，Android 安全系统始于密码和 pin 锁，所以一些设备不具备指纹或面部解锁功能。在为用户启用这种类型的身份验证之前，最好检查设备是否与生物识别身份验证兼容。

为了首先检查可用性，我们需要获得 system `BiometricManager` 类的实例，如下所示:

```
val biometricManager = BiometricManager.from(this)
```

对于`BiometricManager` 实例，我们需要访问`canAuthenticate()`公共函数，这会产生一个整数。

`canAuthenticate()`有四种可能的结果:

*   `BIOMETRIC_SUCCESS`:该设备可以进行生物认证。
*   `BIOMETRIC_ERROR_NO_HARDWARE`:此设备上没有生物特征。
*   `BIOMETRIC_ERROR_HW_UNAVAILABLE`:生物特征目前在设备中不可用。
*   `BIOMETRIC_ERROR_NONE_ENROLLED`:用户尚未在设备中关联任何生物特征凭证。

使用 [Kotlin](https://kotlinlang.org/) mighty `when`来处理不同的用例。看一看:

检查生物识别可用性

# 综合

生物特征库集成很简单。我们需要在应用程序级别的`build.gradle`中的 dependencies 标签下添加下面一行。看一看:

```
dependencies {
    implementation 'androidx.biometric:biometric:1.0.1'
}
```

看看[最新版](https://developer.android.com/training/sign-in/biometric-auth)。

# 使用生物特征库

这是令人兴奋的部分。首先，我们需要创建一个`Executor`的实例，如下所示:

```
val executor = ContextCompat.getMainExecutor(this)
```

`getMainExecutor` 返回一个`Executor`，它在与这个上下文相关的主线程上运行排队的任务。这是用于将调用分派给应用程序组件(活动、服务等)的线程。).这个`Executor`用于在生物识别服务和您的应用程序组件之间建立一座桥梁。

接下来，我们需要创建一个`BiometricPrompt`的实例，如下所示，提示用户进行身份验证。它有三个论点。

1.  Android 组件的实例(活动/片段)
2.  我们在上面创建的一个`Executor`
3.  有三个回调的 lambda 表达式，称为`Biometric.AuthenticationCallback`

带有回调的生物识别提示

现在可以通过`PromptInfo` 构建器将必要的信息发布到提示符下，如下图所示:

将信息发布到生物特征以显示在用户界面上

剩下的就是通过将`promptInfo`作为参数传递，在`BiometricPrompt`实例上使用`authenticate`来显示生物统计对话框。

```
biometricPrompt.authenticate(promptInfo)
```

仅此而已；剩下的工作将由生物识别 API 负责，并通过回调提供适当的结果。

为了更清楚地理解它，请参见下面的代码，其中所有部分都放在一起:

# 启用密码选项

有时，您的指纹选项或面部检测可能会出现问题。例如，当光线不足以进行面部检测时，它可能不会像预期的那样工作。在这种情况下，用户应该能够使用其他身份验证模式，比如 pin/密码/模式。

为了实现这一点，生物特征库通过将`true`作为参数传递，在`BiometricPrompt.PromptInfo`实例上提供了一个函数`setDeviceCredentialAllowed` 。看一看:

# 奖金

要了解有关 Android 安全性的更多信息，请阅读以下文章:

*   "[使用证书锁定从您的 Android 客户端与服务器进行安全通信](https://medium.com/better-programming/secure-communication-with-the-server-from-your-android-client-with-certificate-pinning-5f53cea55972) **"**

感谢您的阅读。
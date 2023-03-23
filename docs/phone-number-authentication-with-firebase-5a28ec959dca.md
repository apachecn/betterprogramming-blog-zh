# 基于 Firebase 的电话号码认证

> 原文：<https://betterprogramming.pub/phone-number-authentication-with-firebase-5a28ec959dca>

## 使用 FirebaseAuth 库对用户进行身份验证

![](img/ec23c3d7c743ede0b46580f52cb43004.png)

由[克里斯托弗·罗宾·艾宾浩斯](https://unsplash.com/@cebbbinghaus?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# 介绍

自从 [Firebase](https://firebase.google.com/) 推出以来，我认为它将对移动开发产生重大影响。Firebase 的制造商已经通过身份验证、实时数据库、机器学习、分析等等证明了这一点。

在本文中，我们将学习如何使用 FirebaseAuth 库通过带有重发特性的 OTP 使用电话号码认证来认证用户。

让我们开始吧。

# 为什么要进行电话号码认证？

使用基于电话号码的身份验证有几个好处:

*   避免重复账户:当你根据一个唯一的电话号码创建一个账户时，很难创建多个账户。
*   安全性:通过电话号码验证，用户将没有任何密码，所以每次登录时，可以通过直接发送到他们手机的动态验证码来完成。
*   用户体验:电话号码验证减少了用户登录时的摩擦，因为他们不必记住任何密码。

# 为什么选择 Firebase？

实施电话号码认证包括向用户的手机发送 SMS。为此，我们需要为短信服务付费。但是通过 Firebase，我们可以免费发送短信。根据免费计划，Firebase 每月提供 10，000 次验证。

除此之外，Firebase 还在几个用例中为开发人员提供了帮助，例如实现实时数据库，通过社交媒体平台和电话号码认证用户，监控分析，发送通知等等。

前端开发人员可以在不了解服务器端代码的情况下完成所有这些工作。这是使用 Firebase 的核心优势之一。我们还可以在 Android、IOS 和 web 上无缝实现 Firebase。

# 综合

Android 开发正在迅速采用 Kotlin，因此在本文中，我们将通过 Kotlin 实现 Firebase 身份验证。要添加支持 ktx 的 FirebaseAuth 库，请在`build.gradle`文件的模块级的 Dependencies 选项卡下添加下面一行:

```
implementation 'com.google.firebase:firebase-auth-ktx:19.3.1'
```

然后，我们需要在 Firebase 控制台的 Authentication 选项卡下启用电话号码登录方法。一旦你完成了，我们就可以开始编码了。

# 发送验证码

首先，我们需要创建一个回调监听器，以获得用户验证是否成功的通知。为此，我们需要使用`PhoneAuthProvider.OnVerificationStateChangedCallbacks()`，如下图所示:

回调监听器

*   `onVerificationCompleted` : 该回调在两种情况下被调用:

1.  在少数情况下，手机无需验证码即可自动验证。
2.  在某些设备上，Google Play 服务会检测收到的短信，并在用户不采取任何行动的情况下调用验证过程。

*   `onVerificationFailed`:由于各种原因调用此回调:如果手机号码格式不正确，当应用程序超过短信限额，或当用户输入错误的验证码。
*   `onCodeSent`:当代码已经发送到用户的手机号码时，调用这个回调。

1.  `verificationId`:我们将把它作为回调函数中的一个参数，我们应该用它和验证码结合起来构建凭证。
2.  `resendtoken`:与此同时，我们还会收到`resendtoken`，用于稍后重新发送代码。

现在我们已经创建了一个回调监听器，我们需要收集用户的手机号码并发送验证码。我们可以通过调用带有必要参数的`PhoneAuthProvider`实例上的`verifyPhoneNumber`函数来发送验证码。看一看:

向用户手机发送验证短信的代码

Firebase 不会发送多个验证短信，直到前一个请求超时。因此，我们需要在活动`onSaveInstanceState`中维护一个标志，并从`onRestoreInstanceState`中检索它，以避免任何混淆。

当我们创建通用应用程序时，支持不同的语言至关重要。为了用特定的语言发送验证消息，我们可以在`auth`实例上使用`setLanguageCode`，如下所示:

```
auth.**setLanguageCode**("fr")
```

# 创建 PhoneAuthCredential

当用户输入验证码时，我们需要使用验证码和之前在`onCodeSent`回调中收到的`verificationid`创建一个`PhoneAuthCredential`。

要创建`PhoneAuthCredential`对象，调用`PhoneAuthProvider.getCredential`:

# 通过 OTP 验证

现在是验证手机号码的时候了。为此，我们需要使用`signInWithCredential`。我们可以添加一个监听器来观察状态变化，如下所示:

OTP 验证

# 重新发送 OTP

如果一切顺利，用户现在可能已经登录了。但是如果他们没有收到 OTP 呢？为了解决这个问题，开发人员通常在超时后启用重新发送代码按钮。

现在，问题是如何用 FirebaseAuth 重新发送代码。同样的请求(`verifyPhoneNumber`)，但是在这种情况下，我们需要添加在`onCodeSent`回调中得到的`resendToken`。看一看:

重新发送 OTP 代码

# 结论

要了解有关 Firebase 提示和新功能的更多信息，请阅读以下文章:

*   [“Firebase——您的应用程序](https://medium.com/@sgkantamani/firebase-a-perfect-database-for-your-app-c74ad19f98d0)的完美数据库”
*   [“基于 Firebase 的 Android 中的机器学习](https://medium.com/@sgkantamani/machine-learning-in-android-2e4cec40cc21)”

目前就这些。希望你学到了有用的东西。感谢阅读！
# 如何在 Android 中实现生物认证

> 原文：<https://betterprogramming.pub/how-to-implement-biometric-authentication-in-android-a1da36d61b1e>

## 现在，您的应用安全性更高

![](img/34741652bf10ed96a23a3419815f56fc.png)

由[川上正风](https://unsplash.com/@loli_oni?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

您设备上的许多应用程序存储了某种类型的私人数据，这些数据将受到另一个未授权用户的保护(例如，银行应用程序、金融应用程序等)。).保护这类数据的最佳方式是通过应用程序中的生物认证。有了生物识别锁，没有获得你的密码的用户在没有所有者允许的情况下无法打开应用程序。

当我第一次听说生物锁的概念时，我认为这很难实现。事实上，这很容易。

让我们来看一个简短的 Android 生物认证演示。

# 履行

1.  在 Android Studio 中创建新项目。
2.  在`app build.gradle`文件中添加一个依赖项。
3.  在主活动中添加按钮和文本。
4.  在`MainActivity.kt`文件中创建`BiometricPrompt`对象。
5.  现在我们将在`Oncreate()`中创建一个对象和一个名为`isCapable`的布尔变量，该变量将返回设备是否具有生物认证。
6.  使用`BiometricPrompt`对象，为认证错误、成功和失败添加回调。

生物认证主要有三种回调:

*   `onAuthenticationError`
*   `onAuthenticationSucceeded`
*   `onAuthenticationFailed`

身份验证错误有许多可能的原因(例如，密码不正确、生物识别锁出现问题等。).认证成功和认证失败也是如此。有很多种可能。

您可以根据应用程序要求使用唯一的代码来显示错误或任何消息。

你可以在 [Android 的文档](https://developer.android.com/reference/android/hardware/biometrics/BiometricPrompt.AuthenticationCallback#onAuthenticationError(int,%20java.lang.CharSequence))中找到所有的代码和回调类型。

7.添加生物识别`PromptInfo`对象。

*   您可以在锁定屏幕上根据您想要的信息编辑`setTitle`和`setSubtitle`。
*   `setDeviceCredentialAllowed`将允许用户使用除生物认证之外的选项来解锁应用程序(例如，密码、数字密码或模式锁)。
*   `setConfirmationRequired`将确认给定的用户凭证，但仅当面部锁定激活时。

8.最后一步是按钮点击事件，应用程序锁的对话框打开。

就是这样！生物认证已经实现。

这是演示的一部分:

![](img/7c034c03d6878a06c6500a17b5c3a5d6.png)

# 参考

[](https://github.com/pg598595/Bio_Metric_Auth_Android) [## pg 598595/Bio _ metrix _ Auth _ Android

### 解散 GitHub 是超过 5000 万开发者的家园，他们一起工作来托管和审查代码，管理项目，以及…

github.com](https://github.com/pg598595/Bio_Metric_Auth_Android) [](https://developer.android.com/reference/android/hardware/biometrics/BiometricManager) [## 生物计量管理器| Android 开发人员

### AccessibilityServiceMagnificationController . OnMagnificationChangedListener

developer.android.com](https://developer.android.com/reference/android/hardware/biometrics/BiometricManager)
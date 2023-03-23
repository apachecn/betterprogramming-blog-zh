# 探索 Firebase 认证

> 原文：<https://betterprogramming.pub/exploring-firebase-authentication-2d9e5c501633>

## 使用 FirebaseAuth 库对用户进行身份验证

![](img/ee679b4bd7a8ba7dd12a0f1f6be1fde5.png)

由 [Fabian Grohs](https://unsplash.com/@grohsfabian?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Android 开发正在迅速采用 Kotlin，因此在本文中，我们将通过 Kotlin 实现 Firebase 认证。要添加支持 ktx 的 Firebase Auth 库，请在`build.gradle`文件的模块级的 dependencies 标签下添加以下行:

```
implementation 'com.google.firebase:firebase-auth-ktx:19.3.1'
```

# 注册并使用电子邮件和密码登录

当您想到身份验证时，电子邮件/密码是广泛使用的方法之一。Firebase 支持这种方法。首先，让我们看看如何注册一个新用户。然后，我们将看到如何为现有用户实现登录。

我们需要在 Firebase 控制台的 Authentication 选项卡下启用电子邮件/密码登录方法。一旦你完成了，我们就可以开始编码了。

## 注册

注册就像在 Firebase 控制台中为用户创建一个新帐户一样简单。为此，我们需要创建一个`FirebaseAuth`的实例，如下所示:

正在创建 FirebaseAuth 实例

使用`FirebaseAuth`实例，我们可以通过调用`createUserWithEmailAndPassword`函数为用户创建一个新帐户。该函数将电子邮件和密码作为参数。

开箱即用，该功能将避免重复帐户、密码/电子邮件验证，并保持标准的密码强度。我们可以添加一个回调侦听器，它返回一个任务对象，其中包含关于请求的详细信息。如果请求成功，我们可以通过调用`FirebaseAuth`实例上的`currentUser`来访问当前用户信息，如下所示:

使用电子邮件/密码创建新帐户

## 登录

现在我们知道了如何使用电子邮件/密码方法在 Firebase 中创建新用户，让我们看看如何实现现有用户的登录。

像创建新用户的`createUserWithEmailAndPassword`函数一样，我们用`signInWithEmailAndPassword`来实现现有用户的登录。我们可以添加回调，并在注册期间实现的任务对象中接收详细信息。看看登录实现:

# 当前用户信息

既然我们已经知道了如何在 Firebase 中创建一个帐户并登录到一个现有用户，那么是时候检索用户信息了。有了 Firebase，我们不再需要在首选项中维护用户详细信息。相反，我们可以从`FirebaseAuth`实例中直接访问它们。

从 FirebaseAuth 检索当前用户的详细信息

一旦您使用`FirebaseAuth`登录/注册，它将存储用户的详细信息。因此，即使应用程序重新启动，我们也可以访问它们。我们可以通过调用`FirebaseAuth`实例上的`signOut`函数来清除用户，如下所示:

```
Firebase.auth.signOut()
```

# 通过 Firebase 使用社交网站

使用谷歌或脸书等社交网站来验证用户身份，将减少与机上用户的摩擦。在这里，我们将学习如何使用谷歌和脸书登录 Firebase。

## 用谷歌登录

要用 Firebase 实现 Google 登录，我们需要添加一个`play-services-auth library`。在`build.gradle`文件的模块级的 dependencies 标签下添加下面一行:

```
implementation 'com.google.android.gms:play-services-auth:18.0.0'
```

下一步是在 Firebase 控制台中启用 Google 登录选项，就像我们对电子邮件/密码方法所做的那样。

要配置 Google Sign-In 来请求用户 id 和基本的个人资料信息，创建一个带有`DEFAULT_SIGN_IN`参数的`[GoogleSignInOptions](https://developers.google.com/android/reference/com/google/android/gms/auth/api/signin/GoogleSignInOptions.Builder#GoogleSignInOptions.Builder%28%29)`对象。要请求用户的电子邮件地址，使用`requestEmail`选项创建`GoogleSignInOptions`对象。

然后我们需要配置 Google Sign-In 来请求用户 id 和基本的个人资料数据。首先，我们需要创建一个带有`DEFAULT_SIGN_IN`参数的`[GoogleSignInOptions](https://developers.google.com/android/reference/com/google/android/gms/auth/api/signin/GoogleSignInOptions.Builder#GoogleSignInOptions.Builder%28%29)`对象。要访问用户的电子邮件地址，将`requestEmail`选项添加到`GoogleSignInOptions`对象中。看一看:

创建 Google 登录构建器

当您使用 Google 登录与后端服务器通信时，您需要调用`requestIdToken`并传递按照本指南[中的步骤](https://developers.google.com/identity/sign-in/android/sign-in)获得的令牌。

通过调用`[GoogleSignInOptions](https://developers.google.com/android/reference/com/google/android/gms/auth/api/signin/GoogleSignInOptions.Builder#GoogleSignInOptions.Builder%28%29)`上的登录意图导航到 Google 登录页面，如下所示:

登录意图

如果在`onActivityResult`中请求成功，我们就可以访问可以在 Firebase 中使用的令牌，如下图所示:

Google 登录响应处理

一旦我们从已登录的帐户获得令牌，我们就可以通过传递令牌调用`GoogleAuthProvider`上的`getCredentialon`来创建凭证。然后我们可以使用凭证作为参数调用`FirebaseAuth`实例上的`signInWithCredential`。看一看:

## 使用脸书登录

要使用脸书登录，我们需要在 Firebase 控制台中启用这个选项，就像我们对 Google 和 email/password 方法所做的那样。

首先，我们需要请求脸书访问用户的基本信息，如电子邮件和个人资料。为此，我们使用脸书的登录按钮和`CallbackManager`，如下所示:

脸书登录

如果请求成功，那么我们可以访问来自`loginResult`的令牌，该令牌用于创建来自`FacebookAuthProvider`的凭证。看一看:

使用脸书登录的 Firebase

目前就这些。希望你学到了有用的东西。感谢阅读！
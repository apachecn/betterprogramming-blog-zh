# 如何在 Android 中获得 FCM 设备令牌(2021 年更新)

> 原文：<https://betterprogramming.pub/how-to-get-an-fcm-device-token-in-android-updated-for-2021-54fcf81746f1>

## 不推荐使用用于检索令牌的旧类。让我们看看更新的那个

![](img/58456d0f43e776dfed685a63686452a5.png)

乔纳森·肯珀在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我真的不太喜欢有截止日期的任务，到处都是旧代码，所有代码都被弃用，导致网上没有具体的答案。

那是你为社区工作，而不是为任何工作。

开门见山:谷歌弃用了 [Firebase](https://firebase.google.com/docs) 的`FirebaseInstanceIdService`类，我直到昨晚才知道，因为我上一次写 FCM 服务是在一年半以前。为了获取 FCM 设备令牌，我必须修改应用程序代码库，该应用程序代码库包含不推荐使用的类，我认为它是正确的。实际上，我得到了两个代码库，它们都有相同的非工作服务类。

我在网上寻找答案，但没有运气。然后我在 Stack Overflow 上看到一个关于`FirebaseInstallations`的问题的评论，这才好玩的开始。

我知道它的功能，但不知道如何到达那里:`FirebaseInstallations`是一条路。我尝试了不同的方法，并得到了整个工作。

# Java 代码片段

我不是在这里填鸭式地喂你。我假设你知道如何在 Firebase 中创建一个项目，如何同步 SDK，包括哪些依赖项，关于`google-services.json`文件，以及这个主类工作之前需要的所有东西。

还有，别忘了把`FirebaseApp.initializeApp(this)`包括在`BaseActivity`类的`onCreate()`里。

# 科特林(带匕首)代码片段

首先，创建扩展`FirebaseMessagingService`的`DaggerFirebaseService`。然后用`MessagingServices`延长`DaggerFirebaseService`。或者你可以简单的移除[匕首](https://dagger.dev/) `Firebase`类如果你不使用匕首的话。

你可以看到我是如何在`BaseApplication`类的`onCreate()`中初始化`FirebaseApp`的。

我会让你完全理解它，因为正如我之前所说的，如果你之前有过这方面的挣扎，你知道你在看什么。

注意:不要只是复制粘贴上面的代码，而是要理解它。

感谢阅读！
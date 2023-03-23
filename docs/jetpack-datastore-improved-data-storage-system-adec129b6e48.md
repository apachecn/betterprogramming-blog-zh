# 喷气背包数据存储:改进的数据存储系统

> 原文：<https://betterprogramming.pub/jetpack-datastore-improved-data-storage-system-adec129b6e48>

## 用 Jetpack 数据存储替换共享首选项

![](img/388919666c7bdfe5e1f6ea47c972a631.png)

由[马克·汀布莱克](https://unsplash.com/@mtimber71?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是数据存储？

多年来，Android 开发者已经习惯于通过共享偏好来存储小块的敏感用户数据。这种方法有以下缺点:

*   共享偏好设置中的敏感数据很容易暴露
*   在 UI 线程上调用共享偏好操作看起来是安全的，但实际上并不安全(因为在 UI 线程上调用同步 API 看起来是安全的，没有发出错误信号的机制，缺少事务 API，等等)

[DataStore](https://developer.android.com/reference/kotlin/androidx/datastore/package-summary?hl=el) 是 Jetpack 家族的一个库，它提供了一个新的数据存储解决方案，可能会取代共享首选项。它目前处于 alpha 阶段。

# 为什么选择数据存储？

它是使用 Kotlin 协同例程和 Flow API 构建的，这使得它比共享首选项更加安全可靠。它提供了两种不同的方法来保存数据:

*   **Preferences DataStore:** 它与`SharedPreferences`相似，因为它无法定义一个模式或确保使用正确的类型访问键
*   **Proto DataStore:** 允许您使用[协议缓冲区](https://developers.google.com/protocol-buffers)创建模式。使用 protobufs 允许强类型数据的持久化。它们比 XML 和其他类似的数据格式更快、更小、更简单、更明确。

除此之外，当您存储键-值对时，只有键会被公开，而内容不会。

# 数据库与数据存储

构建数据存储是为了存储小型数据集；如果您的需求涉及部分更新、参照完整性或对大型/复杂数据集的支持，您应该考虑使用 [Room](https://developer.android.com/topic/libraries/architecture/room) 而不是 DataStore。

在本文的最后，您将找到学习如何使用房间数据库的链接，从它的基础到更高级的选项。

# 综合

要使用 Jetpack 数据存储库，请在应用程序级`build.gradle`文件中的 dependencies 节点下添加以下行。

```
implementation "androidx.datastore:datastore-preferences:1.0.0-alpha01"
```

# 让我们开始编码吧

为了更好地理解如何使用数据存储，我举了一个简单的实时例子，其中我们存储了一个指示用户登录状态的整数。这里，我们有一个包含所有可能阶段的`enum`类。看一看:

## 创建数据存储

下一步是创建数据存储。为此，我们需要创建一个 Kotlin 类，在其中我们将使用`context.createDataStore`扩展函数来创建 DatasSore。看一看:

## 创建密钥

我们将通过定义结果类型来创建`preferencesKey`内联函数。看一看:

```
companion object {
    val USER_STATUS = ***preferencesKey***<Int>("user_status")
}
```

# 保存和检索数据

现在我们已经创建了数据存储和密钥，是时候保存用户的状态了。我们将保存一个值为 1、2、3 和 4 的整数，分别代表`STARTER`、`ONBOARDING_LEVEL_1`、`ONBOARDING_LEVEL_2`和`VERIFIED`。

## 救援

为了在数据存储文件中保存键值对，我们必须使用`edit`函数来更新值。这里，我们使用`suspend`函数来保存值。看一看:

在我们的数据存储中保存值

## 恢复

为了从数据存储中检索值，我们使用流 API。使用 Flow 的一个主要优点是每次数据存储中有新值更新时，我们都会得到通知。所以我们不再需要检查更新的值。

从数据存储中检索数据

我们可以使用像`map`这样的函数将数据转换成适当的数据类型，而不是在调用点将整数交给用户状态用例。
现在，让我们把这些碎片拼在一起，让它们更有意义。看一看:

首选项管理器实现

# 从 Android 组件访问

现在我们已经完成了首选项管理器，让我们看看如何从 Android 活动中调用`setUserStatus`。看一看:

来自活动的“setUserstatus”

由于`setUserStatus`是一个挂起函数，我们使用了一个`lifecycleScope`来启动一个协程。

检索数据中心的数据是类似的；这里，我们通过用一个`lifecycleScope`扭曲它来使用来自流 API 的`collectLatest`。看一看:

# 结论

目前就这些。希望你学到了有用的东西。感谢阅读！

## Jetpack 库资源

*   [“工作管理器基础知识，如何将工作管理器与 Rxjava2 一起使用& Kotlin 协程”](https://medium.com/swlh/workmanager-basics-how-to-use-workmanager-with-rxjava2-kotlin-coroutines-c2a317197038)
*   [《安卓房间暂留库科特林协同程序](https://medium.com/@sgkantamani/android-room-persistence-library-kotlin-coroutines-26d53a09d384)
*   **[“App 启动—新架构组件”](https://medium.com/@sgkantamani/app-startup-new-architecture-component-d115b062a701)**

## **房间图书馆资源**

*   **[“Android 房间持久性库，具有 Rxjava2 和房间数据库的鲜为人知的特性](https://medium.com/mindorks/android-room-persistence-library-with-rxjava2-and-lesser-known-features-of-room-database-8b968261f9f0)”**
*   **[《安卓房间暂留库 kot Lin Coroutines](https://medium.com/@sgkantamani/android-room-persistence-library-kotlin-coroutines-26d53a09d384)**
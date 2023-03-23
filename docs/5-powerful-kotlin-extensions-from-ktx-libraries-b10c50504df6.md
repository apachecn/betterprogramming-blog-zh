# 来自 KTX 库的 5 个强大的 Kotlin 扩展

> 原文：<https://betterprogramming.pub/5-powerful-kotlin-extensions-from-ktx-libraries-b10c50504df6>

## 您应该知道的扩展

![](img/9386d73afb0b19ec3b749a8898088f61.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

[Android KTX](https://developer.android.com/kotlin/ktx) 是一组带有 Kotlin 扩展的 Android 库，用于在使用 Jetpack 和其他 Android 库时简化编码。KTX 扩展利用 Kotlin 的特性，为开发人员提供了一种使用 Jetpack 和其他 API 的简洁方法。

每个库或 API 都有一个单独的 Kotlin KTX 库，其中包含与该段相关的 Kotlin 扩展。在本文中，我们将重点关注五个扩展，它们将使 Android 开发在使用 MVVM、LiveData、Firebase 和房间数据库时更加容易。

如果您有兴趣编写自己的 Kotlin 扩展并寻找灵感，我强烈推荐您阅读以下文章:

*   [5 个 Kotlin 扩展让你的 Android 代码更有表现力](/5-kotlin-extensions-to-make-your-android-code-more-expressive-4c9243cb9466)
*   [面向 Android 开发者的 5 款 Kotlin 扩展](/5-more-kotlin-extensions-for-android-developers-3857b1f16407)

话虽如此，让我们开始扩展。

# 1.生命周期范围的视图模型

当您使用`ViewModels`和 Kotlin 协同程序时，保持范围并在范围内启动长期运行的后台任务是至关重要的，这样我们就可以在必要时取消那些任务。如果您想将任务限制在组件的生命周期内，这种方法也很方便。

假设用户在一个小狗列表屏幕上，您必须从服务器获取小狗列表。如果用户在执行请求之前就离开了屏幕，让他们保持活动状态就是浪费资源。在这种情况下，根据组件的生命周期来确定任务的范围是很方便的。

## 视图模型范围

`viewModelScope`函数是 ViewModel KTX 库的一部分。它将为项目中的每个模型创建范围，使启动协程变得容易。当`onCleared`函数被触发时，在这个作用域下启动的所有协程将在视图模型上自动取消。

要集成 ViewModel KTX 库，请将以下内容添加到您的应用程序级`build.gradle`文件中:

看一下用法:

```
**viewModelScope.launch**  {
    sampleApi.fetchPuppies() // **suspend fucntion**
    ...
}
```

## 生命周期范围

与`viewModelScope`类似，`lifecycleScope`也为 Android 中的每个生命周期对象提供了一个作用域。它是`lifecycle-runtime-ktx`图书馆的一部分。您可以通过`lifecycle.coroutineScope`或`lifecycleOwner.lifecycleScope`进入示波器。

要集成`lifecycle-runtime-ktx`库，请将以下内容添加到您的应用程序级`gradle`文件中:

看一下用法:

```
**viewLifecycleOwner.lifecycleScope**.launch {
     startOtpTimer()
}
```

# 2.视图模型创建

作为现代 Android 开发的一部分，我们正迅速从活动转向片段。在一年左右的时间里，我们将从片段转移到 Jetpack 可组合函数。

无论是 Fragment 还是 Jetpack composable 表示一个屏幕，我们都必须创建或使用关联的`ViewModel`的现有实例。

片段 KTX 库提供了一种简单的方法，使用 Kotlin delegates 特性在片段中创建`ViewModel`实例。我们可以在片段中创建一个新的`ViewModel`实例，或者从它的父 activity 中获得`ViewModel`实例。

要集成片段 KTX 库，请将以下内容添加到您的应用程序级`gradle`文件中:

看一下用法:

```
// Get a reference to the ViewModel scoped to this Fragment
**val viewModel by viewModels<MyViewModel>()**

// Get a reference to the ViewModel scoped to its Activity
**val viewModel by activityViewModels<MyViewModel>()**
```

# 3.LiveData Lambdas

简单来说，`LiveData`是一个可观察的数据持有者类，这意味着`LiveData`可以保存一组数据，这些数据可以从其他 Android 组件(如活动、片段和服务)中观察到。

`LiveData`的常见用例是从`ViewModel`到视图的通信。通常，在`ViewModel`中执行的任务是后台任务，如网络请求、数据库操作等。这些事情应该在后台线程中完成，所以我们要么需要一个`suspend`函数，要么启动一个协程作用域。

对于像从数据库或首选项中读取数据这样的简单任务，创建一个单独的函数太麻烦了。这就是 LiveData KTX 派上用场的时候。LiveData KTX 提供了一个`liveData`构建器函数，它调用一个`suspend`函数并将结果作为一个`LiveData`对象。

要集成 LiveData KTX 库，请将以下内容添加到您的应用程序级`gradle`文件中:

看一下用法:

```
val user: LiveData<User> = **liveData {** val serverData = sampleApi.getPuppies() //suspend function
    database.savedData(serverData) //suspend function
    emit(serverData)**}**
```

# 4.KTX 房间

Room 是 SQLite 上的一个抽象层，用于提供简化的编码。Room KTX 提供了一种通过 Kotlin 协程处理房间数据库的方法。使用 Room KTX，我们现在可以使用 Room Dao 中的`suspend`函数和 Flow API 来观察数据库中每次更新的数据。

作为一个 Jetpack 库，Room 已经带来了一系列好处。向其中添加协程使得实现数据库操作变得简单明了。像 Flow 这样的特性用最少的代码提供了丰富而昂贵的功能。

要集成 Room KTX 库，请将以下内容添加到您的应用程序级`gradle`文件中:

```
dependencies {
    implementation **"androidx.room:room-ktx:2.2.6"**
}
```

看一下用法:

```
@Query("SELECT * FROM Puppies")
**suspend** fun getPuppies(): List<Puppie>

@Query("SELECT * FROM Puppies")
fun getPuppies(): **Flow<List<**Puppie**>>**
```

# 5.带 await 的 Firebase 任务

Firebase 自带了自己的监听器，比如`addOnSuccessListener`、`addOnCanceledListener`、`addOnCompleteListener`等等。我是一个喜欢 Kotlin 协程而讨厌回调模式的 Android 开发者。

协程提供了顺序执行异步操作的能力，这使得代码更加简洁。说到 Firebase，回调模式令人疲惫不堪，所以我开始寻找一种用协程实现 Firebase 任务的方法。

这时我发现了为`Task`提供了一个`await`扩展的`kotlinx-coroutines-play-services`库。大多数 Firebase 操作都是基于`Task`的。这个库提供了一个名为`await`的扩展函数，它在内部执行`suspend`函数中的任务。这意味着我们不再需要那些恼人的 Firebase 回调。

它并不完全来自 KTX 家族，但它是那些从事 Firebase 工作的人的救命稻草。要集成`kotlinx-coroutines-play-services`库，请将以下内容添加到您的应用程序级`gradle`文件中:

看一下用法:

# 奖金

要了解更多关于 Kotlin 协同例程和 Kotlin 的其他高级特性，请阅读以下文章:

*   [“使用 Kotlin 进行高级编程](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb)
*   [“科特林协程，从基础到高级](https://medium.com/better-programming/kotlin-coroutines-from-basics-to-advanced-ad3eb1421006)
*   [“如何使用 Kotlin 密封类进行状态管理”](https://medium.com/better-programming/how-to-use-kotlin-sealed-classes-for-state-management-c1cfb81abc6a)
*   [“使用新 Kotlin 流进行异步数据加载](https://medium.com/better-programming/asynchronous-data-loading-with-new-kotlin-flow-233f85ae1d8b)”

你可以在 [Android 开发者](https://developer.android.com/kotlin/ktx)上找到更多关于 KTX 库的信息。

目前就这些。希望你学到了有用的东西。感谢阅读。
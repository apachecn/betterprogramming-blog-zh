# Android 中协程的使用

> 原文：<https://betterprogramming.pub/coroutines-in-android-a-thought-8044995fa0e3>

## 知道如何使用协程处理长时间运行的任务和主线程安全

![](img/4a5f851689be9a76932e600cbf42d89a.png)

马克·赖歇尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

协同程序是在 Kotlin 1.3 中引入的。这些在 Kotlin 中可能是新的，但是协程的概念并不新颖。它于 1967 年首次在 Simula 推出。现在它已经流行起来，被很多语言采用，比如 Python，C#，Ruby，Go 等等。

协同程序引入了一种新的并发方式，有助于简化异步编程。

正如我们所知，在 android 编程中，我们面临两个主要问题:

1.  长期运行任务的处理
2.  主要安全

# 长时间运行任务的处理

长时间运行的任务需要花费太多时间来执行，我们不得不等待它们，这些任务可以是我们从网络服务器获取数据的任何网络请求，也可以是读取设备中的任何文件。

根据设备的配置，这些任务的性能因设备而异。具有高配置的设备执行这些请求将花费较少的时间，而具有低配置的设备将花费较多的时间，并且可能导致无响应。

在 Android 中，每个应用程序都有一个主线程，负责处理和绘制该应用程序的 UI。如果任何任务花费太多时间来执行，那么它将导致该应用程序的不适当行为，因为它阻塞了主线程。为了克服这个问题，协程可以帮助我们。

在协程之前，这些操作是通过使用回调和其他库如 RxJava、RxAndroid 来处理的。

```
class NotesViewModel:ViewModel(){
    fun fetchNotes(){
        get(“someurl”)**{** result**->{** showNotes(result)
        **}
        }** }
}
```

这种类型的请求方法将从主线程调用，请求将来自另一个工作线程，结果将在主线程上提供，以便我们可以根据需要访问该结果。

这种类型的功能可以由第三方库提供，如翻新，使网络请求远离应用程序的主线程。

## 在长时间运行的任务中使用协程

这个操作也可以在协程的帮助下完成。但是这里的主要区别是，它不会阻塞主线程，而是基于挂起和恢复的概念工作。

使用协程，上述请求如下所示:

```
class NotesViewModel : ViewModel() {
    suspend fun fetchNotes() {
        var result = get(“ someurl ”)**{** showNotes(result)
        **}** }

    suspend fun get(url: String) {
        */**/* }
}
```

上面的例子不会阻塞主线程，但是它会挂起当前的协程，并等待方法的结果，当它得到结果时，它将从挂起前一次执行的地方开始执行。

在这个过程中，它不会阻塞主线程，主线程可以自由地完成它的任务，无论它在做什么。

它看起来像我们写的顺序代码，但它不会阻塞主线程。

# **主安全**

在 Kotlin 中，从主线程调用这些挂起函数是安全的，因为它们不会阻塞主线程。除了这些任务之外，android 中还有几个我们不能在主线程上执行的功能，因为它们的行为很慢，比如 JSON 解析、从数据库读取和写入数据，所以所有这些任务都可以使用 suspend 函数来完成。

使用 suspend 并不表示这个操作将在后台线程上执行，它将告诉我们这个操作不会妨碍主线程。因此，无论何时你做任何缓慢的任务，你都可以使用协程。

在 Android 中，所有的协程都运行在一个调度程序上，它们可以挂起自己，调度程序知道如何恢复协程。

我们可以使用 Kotlin 提供的调度程序来指定`suspend`函数将在哪里运行

同一件事的三个调度员:

1.  调度员。主要的
2.  调度员。超正析象管(Image Orthicon)
3.  调度员。默认

## **调度员。主**

顾名思义，它运行在主线程上，用于更新应用程序的 UI。可以从这里调用挂起函数。

## **调度员。IO**

它在后台线程中运行，主要用于与网络相关的繁重任务。

## **调度员。默认**

它在后台线程中工作，主要用于 CPU 密集型工作，如解析 JSON 或访问任何列表。

继续上面的例子，如果我们在 Dispatchers 的上下文中讨论，那么上面的代码看起来像这样

```
class NotesViewModel : ViewModel() {
    suspend fun fetchNotes() {
        *//Dispatchers.Main* var result = get(“ someurl ”)**{** *//Dispatchers.Main* showNotes(result)
        **}** }

    *//Dispatchers.IO* suspend fun get(url: String) {
        */**/* }
}
```

我们可以看到，`get()`方法正在发出一个网络请求，它正在使用运行在主线程上的`Dispatchers.IO`及其结果，我们正在主线程上使用`Dispatchers.Main`上下文更新主线程上的 UI。

让每个挂起函数都是主安全的，这是一个非常好的实践。如果它做任何使用太多 CPU 的事情，使用`withContext`使它从主线程调用变得安全。这是基于协程的库(如 Retrofit 和 Room)遵循的模式。

如果你使用这种风格，那么你的代码会简单得多。当一致遵循时，协程可以在主线程上自由启动，用简单的代码发出网络或数据库请求，同时保证用户不会面临任何问题。

## withContext 性能

`withContext`与回调或 RxJava 一样快，可提供主要安全性。在某些情况下，甚至可以优化`withContext`调用，而不仅仅是回调。

如果一个函数将对一个数据库进行 10 次调用，你可以告诉 Kotlin 在所有 10 次调用的外围`withContext`切换一次。然后，即使数据库库会反复调用`withContext`，它也会停留在同一个调度程序上，并遵循一条快速路径。

此外，优化了`Dispatchers.Default`和`Dispatchers.IO`之间的切换，以尽可能避免线程切换。

# 最后的想法

正如我们已经看到的，我们可以通过协程解决什么问题。协程是一个老概念，最近变得流行起来，因为它们能够使与网络交互的代码更加简单。

我们可以在 Android 中使用它，原因如下:

1.  简化长时间运行操作的代码，如下载图像、获取数据或读取文件。
2.  执行主安全意味着您的代码不会阻塞主线程，并且保持简单。

# 参考

[Android 上的 Kotlin 协同程序](https://developer.android.com/kotlin/coroutines?gclid=Cj0KCQiAjJOQBhCkARIsAEKMtO0tFOa9ntBdbEfu3DjPIMfOzz9xdKWg5-38sdFXtQOGnFX8q1cyCrkaAqvrEALw_wcB&gclsrc=aw.ds)
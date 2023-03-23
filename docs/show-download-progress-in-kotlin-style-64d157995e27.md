# 如何以科特林风格显示下载进度

> 原文：<https://betterprogramming.pub/show-download-progress-in-kotlin-style-64d157995e27>

## Ktor、密封类、扩展和流

![](img/b6e89f404be3748b074d67a0b349b114.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Uri Paz](https://unsplash.com/@uripaz?utm_source=medium&utm_medium=referral) 拍摄的照片。

这篇文章的目的很简单:我们需要从互联网上下载一个文件，并在下载过程中显示进度。这可以用很多方法来实现，但是我们将用纯 Kotlin 来实现。当我说*纯科特林*时，甚至我们将使用的库都是科特林本地的。

# 介绍

在开始这个过程之前，让我告诉你我们将使用什么。如果你对这些东西不熟悉，我会在它们下面留一个链接来详细解释。我强烈建议在进一步学习之前，对这些概念有一个基本的了解。

## Ktor

我们使用 Ktor Android 客户端进行联网。Ktor 是一个开源框架，使用强大的 Kotlin 编程语言在连接的系统中构建异步服务器和客户机。它运行在协程上，由 [JetBrains](https://www.jetbrains.com/) 制作。

[](https://medium.com/better-programming/how-to-use-ktor-in-your-android-app-a99f50cc9444) [## 如何在你的安卓应用中使用 Ktor

### 了解关于这个 Kotlin 本地网络库的所有信息

medium.com](https://medium.com/better-programming/how-to-use-ktor-in-your-android-app-a99f50cc9444) 

## 密封类

我们使用密封类作为有效的状态管理工具。根据[男性研究](https://androidstudy.com/2017/06/29/kotlin-sealed-classes/)，

> “密封类允许您表示受约束的层次结构，其中对象只能是给定类型之一。”

> 这意味着我们有一个包含特定数量子类的类。我们最后得到的非常类似于枚举。不同之处在于，在 enum 中，每个类只有一个对象，而在密封类中，我们可以有同一个类的几个对象。这种差异将允许密封类中的对象保持状态。—[AndroidStudy.com](https://androidstudy.com/2017/06/29/kotlin-sealed-classes/)

[](https://medium.com/better-programming/how-to-use-kotlin-sealed-classes-for-state-management-c1cfb81abc6a) [## 如何使用 Kotlin 密封类进行状态管理

### 使用密封类简化复杂的数据流

medium.com](https://medium.com/better-programming/how-to-use-kotlin-sealed-classes-for-state-management-c1cfb81abc6a) 

## 科特林扩展

> Kotlin 提供了用新功能扩展一个类的能力，而无需从该类继承或使用 Decorator 之类的设计模式。这可以通过名为*扩展的特殊声明来完成。*例如，我们可以在第三方库中为一个不能修改的类编写新的功能，并将它们作为该类的实际功能。这些功能称为*扩展功能。–*[*科特林文档*](https://kotlinlang.org/docs/reference/extensions.html)

[](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb) [## 使用 Kotlin 进行高级 Android 编程

### kot Lin-您应该使用的独有功能

medium.com](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb) 

## 科特林流

我们使用 Kotlin Flow 作为网络和 UI 之间的中介。

Kotlin 中的 Flow API 是异步处理顺序执行的数据流的更好方式。有了 Flow，协程就成了 Kotlin 项目中 RxJava 的替代品。

[](https://medium.com/android-dev-hacks/exploring-livedata-and-kotlin-flow-7c8d8e706324) [## 探索 LiveData 和 Kotlin 流

### 何时何地使用 LiveData 和 Kotlin Flow

medium.com](https://medium.com/android-dev-hacks/exploring-livedata-and-kotlin-flow-7c8d8e706324) 

# 为状态管理创建一个密封类

我们需要三种状态，如成功、失败和进度百分比。所以我们需要创建一个包含两个数据类和一个 Kotlin 对象的密封类。当我们需要传递和检索任何数据时，我们使用数据类。否则，Kotlin 对象将是一个很好的选择。看一看:

# 建立工作关系网

这里，我们将在 Ktor `HttpClient`类(即`downloadFile`)上创建一个扩展，并在其中包含所有必要的功能。这将是一个`suspend`函数，这样我们可以直接调用网络调用:

```
**suspend** fun HttpClient**.downloadFile(){****}**
```

我们将从函数的参数开始功能。我们需要两个参数:文件和 URL。该文件用于保存下载的数据和调用请求的 URL:

```
suspend fun HttpClient.downloadFile(**file: File, url: String**){}
```

下一步是返回类型为`DownloadStatus`的 Kotlin 流，这可以在另一侧观察到。我们可以稍后使用`emit`扩展功能来发布更新:

```
suspend fun HttpClient.downloadFile(file: File, url: String) **: Flow<DownloadStatus>** {}
```

现在我们已经完成了输入和输出，是时候实现功能了。看一看:

这看起来像复杂的代码，但实际上，它很简单。首先，我们将响应赋值给一个变量。然后我们创建一个长度为`response.contentLength()`的`ByteArray`并保存文件的每一个块。在`while`循环中，我们有每个读取块的进度，并使用`emit`函数将其发布到 UI。

# 收集结果

在你的 UI 层，我们可以使用 Kotlin Flow 的`collect`函数来消费来自流源的每一个`emit`。然后在`collect`函数内部，我们需要在更新 UI 之前将调度程序切换到主线程:

然后在`Dispatchers.Main`中，我们可以告诉 Kotlin 何时区分状态并更新 UI:

# 奖金

要了解更多关于 Kotlin 的信息，请阅读 Kotlin 高级编程系列的前几部分:

*   [“使用 Kotlin 进行高级编程—第二部分](https://medium.com/android-dev-hacks/advanced-android-programming-with-kotlin-part-2-aae2a15258b0)”

要了解更多关于 Kotlin 协同例程和 Kotlin 的其他高级特性，请阅读以下文章:

*   [“服务器端开发中的 Ktor:基础知识”](https://medium.com/@sgkantamani)

这篇文章的灵感来自于下面这篇文章:

[](https://blog.kotlin-academy.com/show-download-progress-in-a-recyclerview-with-ktor-client-and-flow-asynchronous-data-stream-9debab3d2cb6) [## 使用 Ktor 客户端和流异步数据流在 RecyclerView 中显示下载进度

### 我很确定你们中的许多人都想知道“嘿，但是显示一个确定的进度条怎么样？”。所以在这个…

blog.kotlin-academy.com](https://blog.kotlin-academy.com/show-download-progress-in-a-recyclerview-with-ktor-client-and-flow-asynchronous-data-stream-9debab3d2cb6) 

# 结论

目前就这些。希望你学到了有用的东西。感谢阅读。
# 在 Android 库中实现手动依赖注入

> 原文：<https://betterprogramming.pub/implementing-manual-dependency-injection-in-android-libraries-6149bb7c477d>

## 创建您自己的 DI 框架

![](img/89da1cbee3a914a0f3244586c22b799c.png)

[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

有许多资源可供开发人员在 Android 应用程序中实现 DI。但是当谈到 Android 库中的 DI 时，我只发现了几个——其中只有几个是真正有用的。

所以我决定写一篇关于为什么以及如何在一个 Android 库中实现手工依赖注入的文章。我在图书馆与 DI 合作的经历可以追溯到 2018 年，当时我工作的公司与一家行业领导者达成了协议。他们现在想要我们应用程序的一部分，将他们的应用程序作为一个库包含进来。

当谈到库时，开发人员最初没有太多考虑的是，与实际的 Android 应用程序开发相比，我们在开发中受到了多大的限制。如果您对某个应用程序类有一些依赖，并且已经在库中创建了自己的应用程序类，那么实际的应用程序级应用程序类应该扩展库应用程序类。否则，您会得到一个编译时错误。

说到 Android 库中的 DI，它非常依赖于应用程序类。如果你把 dagger 或者 hilt 作为你的 DI 框架，你需要从 application 类触发代码生成。库的这种应用程序级依赖性在集成时会产生摩擦。

# 为什么我在 Android 库中选择手动 DI

1.  以减少整合时的摩擦。
2.  主机应用可能会也可能不会使用库使用的 DI 框架，所以在 SDK 中使用 dagger 这样的大型库会增加主机应用的大小。
3.  让事情尽在我们的掌控之中。Dagger2 可以在库中使用，而不必在主机应用程序中使用，但这需要大量的努力，不值得。

# 手动 DI 框架的蓝图

我喜欢把事情简单化。我的手动 DI 框架所依赖的主要特性是 Kotlin 的`object`和`lazy`。所以在继续之前，让我们唤起你对它们的记忆。

## 科特林物体

一个 Kotlin `object`就像一个不能被实例化的类，所以它必须被名字调用。我们可以把它看作一个具有应用程序会话生命周期的单例类。在内部，Kotlin 基于所有最佳实践(如线程安全等)维护这个单例类。看一看:

```
**object** NetworkModule {}
```

上面这段代码相当于下面的 Java 类:

## 懒惰的

Kotlin 中的`lazy`关键字是许多 Android 开发者的救命稻草。假设您的类中有一个变量，它的值是通过执行一项昂贵的工作获得的，但该变量只有在用户访问屏幕上的特定功能时才有用。处理这个问题的最简单的方法是初始设置变量`null`，并在用户访问该特性时初始化该值。但是我们必须在每次访问变量时使用`null`检查。

我们可以做得更好，我们可以通过两种方式做到:

*   将该变量命名为`lateinit var` 并在需要时初始化。
*   惰性初始化。

```
val gson : Gson by *lazy* **{** provideGson() **}**
```

现在你已经知道了我的 DI 配方的两个主要组成部分，接下来的部分就是设计了。对于每个目的，我们将创建一个科特林`object`。所以对于网络相关的依赖，我们创建一个`NetworkModulr` Kotlin `object`。对于与数据库相关的依赖项，我们创建`DatabaseModule`。在这些 Kotlin `objects`中，我们使用`lazy`来创建每个依赖项。仅此而已。就这么简单。

# 设计一个简单的 DI 框架

这里，我们有三种类型的依赖——网络、数据库和特定于 Android 的，比如应用程序类实例。为此，我们需要创建三个 Kotlin `objects`如下所示:

```
object NetworkModule {}object DatabaseModule {}object LibraryModule {}
```

## 库模块

为了简单起见，让我们从`LibraryModule`开始。现在，我们只有一个单一的需求。这是应用程序类的实例。这不能通过`lazy`操作符创建。相反，我们使用`lateinit var`和`@Volatile`注释使其线程安全。看一看:

库模块

基本上，在库初始化的时候，我们需要用一个应用实例调用`initializeDI`函数。这是我们 DI 框架的入口点(就像初始化 dagger 组件一样)。

```
***activity***?.*application*?.*let* **{** LibraryHelpers.**initializeDI**(**it**) **}**
```

## 网络模块

既然我们已经完成了库模块并有了一个应用程序实例，那么是时候创建与网络相关的依赖项了，比如具有应用程序级生命周期的`retrofit`。为了创建一个`retrofit`实例，我们需要一个序列化器、OkHttp 客户端、一些添加头的头拦截器等等。看一看:

# 数据库模块

最后，我们谈到了与数据库相关的依赖性。这里，我们需要通过`lazy`操作符创建房间数据库实例。看一看:

类似地，您可以为每个目的创建所需数量的对象，并在整个生命周期中维护`lazy`实例。(例如，对于存储库、用例等。).

我还没有考虑作用域，因为我构建的库最多只有 4-5 个屏幕，我不喜欢为这样的事情实现作用域。如果您对基于范围的依赖关系感兴趣，那么您可以将依赖关系设为`null`类型，并清除该范围破坏的对象。我的建议是不要做这件事。

# 奖金

我最近看到了[萨提亚·帕万·坎塔马尼](https://medium.com/u/d255c4fc9d76?source=post_page-----4c9f44d04e68--------------------------------)发表的这篇关于安卓系统中`BroadcastReceiver`的优秀文章:

[](https://pavan-careers5208.medium.com/broadcastreceiver-in-android-adcfe2801a6b) [## Android 中的广播接收器

### 处理系统范围和自定义广播

pavan-careers5208.medium.com](https://pavan-careers5208.medium.com/broadcastreceiver-in-android-adcfe2801a6b) 

目前就这些。希望你学到了有用的东西。感谢阅读。
# 如何使用 Kotlin 和 LiveData 监控 Android 中的互联网连接

> 原文：<https://betterprogramming.pub/how-to-monitor-internet-connection-in-android-using-kotlin-and-livedata-135de9447796>

## 增强应用程序的离线功能

![](img/c7936cde7ea6b61ff9545141e6cf9090.png)

照片由[谢尔盖·佐尔金](https://unsplash.com/@szolkin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 介绍

如今，任何移动应用程序的基本要求之一就是互联网连接。如果你曾经使用过任何移动应用程序，你可能会看到一个红色或绿色背景的小横幅，根据应用程序的用户界面，在屏幕的顶部或底部指示网络连接状态。

实现这个特性有两个好处。首先，用户知道他们是离线的，所以他们可以采取适当的行动。第二，如果你正确地实现了它，你的用户界面会很美观。

# 先决条件

这是一个看似容易处理的特性，但需要对`ConnectivityManager`中的工作原理有更深的理解。这里我们使用`ConnectivityManager`来获取网络连接的信息，然后我们使用`LiveData`将结果以密封的类格式传递给视图。我们还将使用 Kotlin 协同例程来解决 Android APIs 的实时问题。

在继续之前，如果您不知道上面讨论的任何概念，请通读以下文章:

*   "[探索 LiveData 和 Kotlin 流](https://medium.com/android-dev-hacks/exploring-livedata-and-kotlin-flow-7c8d8e706324)"
*   "[如何使用 Kotlin 密封类进行状态管理](/how-to-use-kotlin-sealed-classes-for-state-management-c1cfb81abc6a)"
*   [科特林协程，从基础到高级](/kotlin-coroutines-from-basics-to-advanced-ad3eb1421006)

# 密封类

[根据 Kotlin 文档](https://kotlinlang.org/docs/sealed-classes.html)，密封的类和接口代表了受限的类层次结构，提供了对继承的更多控制。

首先创建一个简单的密封类，它有两种状态，网络可用和不可用。密封类非常适合这个目的，它们使得在类站点处理状态变得很容易。在我们的例子中，是风景。看看一个简单的密封类:

```
sealed class NetworkStatus {
    object Available : NetworkStatus()
    object Unavailable : NetworkStatus()
}
```

如果需要，可以将消息作为参数传递给每个状态。

# 分析网络监视器类

我们将要创建的网络监视器类应该满足以下几点:

*   我们希望它能对视图和`Lifecycle`感知做出响应。
*   它应该很容易跨组件重用。
*   它应该总是有更新的网络状态信息。

这三点使我们的网络监视器类易于采用，并提供了强大的功能。

# 利用 LiveData

为了涵盖第一点和第二点，我们可以利用`LiveData`。正如我们所知，`LiveData`具有生命周期意识，并对视图`Lifecycle`做出响应。我们可以用类型为`NetworkStatus`的`LiveData`来扩展我们的类。看一看:

```
class NetworkStatusHelper() : LiveData<NetworkStatus>() { }
```

这将允许我们覆盖两个方便的函数，`onActive`和`onInactive`。我们可以使用这两个函数从系统中恢复和停止监视网络状态，这将在下一节中看到。这将解决我们的第一个问题。看看原始代码:

```
class NetworkStatusHelper() : LiveData<NetworkStatus>() {

    override fun onActive() {
        super.onActive()
    }

    override fun onInactive() {
        super.onInactive()
    }

}
```

使用`LiveData`作为事实的来源使得跨 Android 组件重用变得更加容易，这解决了我们的第二点。

# 监控网络连接

下一步是建立到系统的连接，通过该连接我们可以观察网络连接状态。这可以通过`ConnectivityManager`完成。为了访问`ConnectivityManager`，我们需要上下文，所以传递上下文最简单的方法是通过构造函数参数。看一看:

正在创建`ConnectivityManager instance`

## 网络连接监听器

现在我们已经创建了实例`ConnectivityManager`，下一步是创建一个回调监听器来观察系统级网络连接的变化。这里我们用`ConnectivityManager.NetworkCallback`。看一看:

来自`ConnectivityManager class`的简单网络监听器

因为覆盖函数的名称是不言自明的，所以我跳过了解释部分。下一部分是使用覆盖函数来为我们服务。先说`onAvailable`功能。看一下代码:

第一步，我们从`ConnectivityManager`实例中获取网络能力，然后检查它是否具有互联网能力。然后我们将维护一个有效网络的列表，如果列表不为空，我们显示`network available`；else，`not available`。

然后是`onLost`函数，我们从列表中删除网络并更新`LiveData`。看一看:

```
override fun onLost(network: Network) {
    super.onLost(network)
    **valideNetworkConnections.remove(network)
    announceStatus()**
}
```

最后，每当网络的任何能力被更新时，就调用`onCapabilitiesChanged`功能。在这里，我们将重新检查网络的互联网能力，并采取适当的行动。看一看:

`onCapabilitiesChanged function`

最后，为了看到更大的图片，把所有的碎片放在一起:

完整的网络回拨设计

# 注册/注销 connectivityManager 回调

最佳实践之一是通过在不需要时不使用系统资源来节省系统资源。为了在我们的网络监视器类中实现这一点，我们使用了 LiveData 的`onActive`和`onInactive`函数。看一看:

网络监视器类的完整实现

# 观察 Android 组件的状态

最后，是时候使用我们的网络监视器从 Android 活动中观察网络连接的状态，并向用户显示适当的消息。看一看:

从任何 Android 组件观察连接状态

您可以将这段代码添加到活动的`onCreate`函数中。

# Android APIs 的问题

Android APIs 中有一个缺陷。使用`onAvailable`功能，它们发出网络连接状态，但不是该网络的实际能力，例如，如果它有实时互联网接入。

# 解决办法

要检查网络是否有实时互联网连接，我们需要实际进行网络调用，并根据输出确定网络互联网能力。

为此，我创建了一个具有单一函数的`InernetAvailablity` Kotlin 对象。如果有互联网接入，函数的返回类型为`Boolean`:`true`；else，`false`。看一看:

```
object InernetAvailablity {

    fun check() : Boolean {
        return try {
            val socket = Socket()
            **socket.connect(InetSocketAddress("8.8.8.8",53))**
            socket.close()
            **true**
        } catch ( e: Exception){
            e.printStackTrace()
            **false**
        }
    }

}
```

如果套接字连接建立，没有任何异常，我们确定网络有一个互联网连接；否则，它不会。

现在我们需要在具有协程作用域的`onAvailable`和`onCapabilitiesChanged`函数中执行这个操作，并且只有当它具有互联网功能时，我们才需要将网络添加到列表中。看一看:

检查网络的互联网功能

最后，看一下代码:

网络监控实施

# 奖金

我最近看到了 Satya Pavan Kantamani 发表的这篇关于 Kotlin 协程的优秀文章，强烈推荐:

[](/asynchronous-programming-with-kotlin-coroutines-5b3417f53ac6) [## 用 Kotlin 协程进行异步编程

### 用协程程序编写阻塞和非阻塞代码

better 编程. pub](/asynchronous-programming-with-kotlin-coroutines-5b3417f53ac6) 

目前就这些。希望你学到了有用的东西。感谢阅读！
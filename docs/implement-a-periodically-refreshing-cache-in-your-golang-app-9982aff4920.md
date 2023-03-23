# 在你的 Golang 应用中实现定期刷新缓存

> 原文：<https://betterprogramming.pub/implement-a-periodically-refreshing-cache-in-your-golang-app-9982aff4920>

## 让我们提高 web 应用程序的性能

![](img/131ae7a3995ed05aa5d42e38c17c9833.png)

Jess Bailey 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在后台定期刷新的缓存是几种用例中使用的常见模式，但是我们如何在 Go 中实现这一点呢？在本文中，我将向您介绍实现背后的概念，帮助您理解它是如何工作的。

![](img/2dccb6eeb7dfecf6fb532500af8abba2.png)

# TL；速度三角形定位法(dead reckoning)

没有文字，更多代码。如果你想看完整的实现，直接去[这个要点](https://gist.github.com/thomaspoignant/f2c901911e81f3109fe64ad840b5d393)。

# 定期在后台做一些事情

在这个例子中，假设我们的缓存是一个简单的`map`:

```
var **cache** map[string]interface{}
```

我们想要的是拥有一个每 *X* 秒*更新缓存的线程*。为了在 Go 中做到这一点，我们将使用一个`goroutine`来创建一个后台线程，它的作用是定期更新缓存。

这里我们有两个概念:创建一个新线程和定期处理一个动作。

## Goroutine 和 channel

如你所见，为了执行`startUpdaterDaemon` 功能，我们给它加上了前缀`go`。这个意味着我们在一个新线程中调用这个函数。

您还可以看到我们正在参数中发送一个通道。正如在 [Code Farm](https://codefarm.me/2017/06/11/goroutines-and-channels-in-go-lanugage/) 中所解释的，“通道是一种通信机制，它使一个 goroutine 能够向另一个 goroutine 发送值。”在这里，如果需要的话，我们可以用它来从外面阻止`goroutine`。

要停止`goroutine`，只需关闭通道:

```
defer **close**(updaterChan)
```

因此，您将转到`select`的第二部分并从函数返回，从而停止无限循环:

```
case <-**updaterChan**:
  // stop the daemon
  **return**
```

更多关于`goroutine`:

[](https://pkg.go.dev/github.com/dc0d/goroutines) [## 戈鲁廷斯

### 这个包提供了在 goroutine 上执行常见任务的实用程序——等待 go routine 启动、超时、do…

pkg.go.dev](https://pkg.go.dev/github.com/dc0d/goroutines) 

## 心脏

第二个关键部分是`time.Ticker`。这个概念允许你管理什么时候做一个动作。当你在创建你的`time.Ticker`时，你给出一个`duration`，之后，你可以使用`ticker.C`来知道什么时候该做动作了。

当你完成时，不要忘记用`Stop()`功能关闭你的`time.Ticker`。

关于`time.Ticker`的更多信息:

[](https://pkg.go.dev/time#Ticker) [## 时间

### 包装时间提供测量和显示时间的功能。日历计算总是假设…

pkg.go.dev](https://pkg.go.dev/time#Ticker) 

# 管理缓存上的读/写

实现一个好的缓存的另一个关键是考虑当你的包的其他部分同时在读缓存时，你如何写/修改你的缓存。

为了做到这一点，我们将使用互斥体。Go 有一个我们需要的完美对象:`sync.RWMutex`。

> RWMutex 是一个读取器/写入器互斥锁。锁可以由任意数量的读取器或单个写入器持有。RWMutex 的零值是一个未锁定的互斥体。— [Go 的文档](https://golang.org/pkg/sync/)

这意味着您可以同时有多个读者，但只能有一个作者。

要更新缓存，您应该在更新之前使用互斥体的`Lock()`。然后，在使用`Unlock()`功能更新后，释放您的锁定。

```
*// update the cache* **mutex.Lock()** *// lock the cache before writing into it* cache["key3"] = time.Now()
**mutex.Unlock()** *// unlock the cache before writing into it*
```

第二个考虑是当你读取数据时。我们在这里所做的也是使用一个锁，但是这一次，我们使用`RLock()`和`RUnlock()`，因为我们想要有多个读锁。

```
**mutex.RLock()** *// add a read lock before reading the cache* fmt.Println(cache["key3"])
**mutex.RUnlock()** *// release the read lock when reading is done*
```

关于`RWMutex`的更多信息:

[](https://golang.org/pkg/sync/#RWMutex) [## sync-Go 编程语言

### Cond 实现了一个条件变量，一个等待或宣布事件发生的集合点

golang.org](https://golang.org/pkg/sync/#RWMutex) 

# 完全解

完整的解决方案将如下所示。正如你所看到的，我们正在使用我们已经谈论过的所有上下文:`RWMutex`、`time.Ticker`、`goroutine`和`channel`。

正如您在下面的输出中看到的，缓存每三秒钟改变一次:

```
2021-01-20 20:48:54.415613 +0100 CET m=+0.000100959
2021-01-20 20:48:54.415613 +0100 CET m=+0.000100959
2021-01-20 20:48:57.418863 +0100 CET m=+3.003280863
2021-01-20 20:48:57.418863 +0100 CET m=+3.003280863
2021-01-20 20:48:57.418863 +0100 CET m=+3.003280863
2021-01-20 20:49:00.415974 +0100 CET m=+6.000323481
2021-01-20 20:49:00.415974 +0100 CET m=+6.000323481
2021-01-20 20:49:00.415974 +0100 CET m=+6.000323481
2021-01-20 20:49:03.420429 +0100 CET m=+9.004709000
2021-01-20 20:49:03.420429 +0100 CET m=+9.004709000
2021-01-20 20:49:03.420429 +0100 CET m=+9.004709000
2021-01-20 20:49:06.419332 +0100 CET m=+12.003543113
...
```
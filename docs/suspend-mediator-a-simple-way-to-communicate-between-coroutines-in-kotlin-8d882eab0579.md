# suspend Mediator——kot Lin 中协同程序之间通信的一种简单方式

> 原文：<https://betterprogramming.pub/suspend-mediator-a-simple-way-to-communicate-between-coroutines-in-kotlin-8d882eab0579>

## 了解如何使用挂起功能创建简单的 WebSocket 数据源

![](img/2ed4a9f47eacb13c25df4fe83df8f472.png)

扎克·卢塞罗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在这个故事中，我们将学习如何与不同的协程通信，并使用它们来实现一个具有看起来像本地函数的远程数据源。这些函数是一个请求，使用 WebSocket 返回响应。

# 动机

当我编写内部使用 WebSocket 连接的数据源时，我注意到发送/接收套接字模式增加了软件的复杂性和难以维护性。发送一个请求，在代码的另一部分接收响应，迫使程序员声明在响应代码中使用的临时变量。

想象一下，如果可以发送请求并暂停它，直到收到响应，然后恢复它。像下面的代码一样，数据源提供了一个很好的暂停登录函数，它返回登录的结果(最后是完整的代码):

## SuspendMediator 解决了这个问题

嗯，有了`SuspendMediator`，有可能！我的实现只有两个方法:暂停和恢复。

请注意，这两个函数接收一个密钥来专门标识请求和响应。假设请求有一个在响应中发回的消息标识符。该标识符可以是该消息的关键。键越“唯一”，并行请求就越多，因为重复的键需要在请求队列中等待。这个限制对于`SuspendMediator`知道谁将发送的请求与接收的响应唯一匹配是必要的。

## 履行

`SuspendMediator`可以有几种实现，有无请求队列、请求/队列超时等等。

请求队列用于缓存具有相同关键字的请求或避免并行请求:

*   无队列:不同键的执行可以并行进行，但是当`SuspendMediator`检测到一个重复的键时，会在 suspend 方法上抛出一个错误。
*   相同键入队:不同键的执行可以并行，重复键入队。
*   确保顺序:避免并行执行。它一个接一个地发送它们，按顺序等待响应。

我们可以有两种类型的超时:

*   队列超时:当我们有重复的键，并且有必要在队列中等待请求时，使用超时。
*   请求超时:用于避免等待永远不会收到的响应的超时。

废话说够了。给我看看代码:

# 使用 SuspendMediator 的示例

`SuspendMediator`的主要目的是在协程之间进行通信。假设三个协同程序的乒乓操作将一个数乘以 2 的倍数。

输出:

```
Coroutine 1 - result1 Success(5)
Coroutine 2 - result2 Success(10)
Coroutine 3 - result3 Success(20)
Coroutine 1 - result4 Success(40)
```

协程 1 用键 1 挂起，直到协程 2 用值 5 恢复这个键。协程 1 继续接收该值，并用密钥 2 发送双倍的该值。关键字 3 的值在协程 2 中产生，并发送给协程 3，依此类推。注意，`SuspendMediator`不是生产者/消费者，而是协程之间双向通信的一种方式。

更有趣的例子是使用挂起函数的动作回调。当调用该操作并返回 true 时，可以保证该键已准备好供另一个协程调用使用该键继续。

回到故事开始时的登录示例，想象一下这个简单的登录字符串协议:

```
Request: 1|username|password
Response: 1|loginResult
```

请注意，第一个字段表示消息的类型(Login ),在本例中，它的值为 1，我们可以将它用作消息的键。该协议可以使用如下密封类来实现:

让我们看看使用`SuspendMediator`的`DataSource`示例代码:

当登录函数被调用时，该函数挂起，直到`queuedSuspendMediator`在队列中没有值为 1 的键，因为`queueMode`等于`ENQUEUE_SAME_KEY`。在简单的情况下，队列是空的或者没有键 1，所以不需要挂起。

之后用`LoginRequest`的值调用 WebSocket `sendMessage`。当服务器响应时，监听器解析消息并用`LoginResult`消息调用恢复函数。

# 结论

使用`SuspendMediator`，我们可以为请求和响应消息编写更简单的代码，降低临时变量的复杂性。WebSocket DataSource 是`SuspendMediator`的众多用例之一。

感谢阅读。敬请关注更多内容。
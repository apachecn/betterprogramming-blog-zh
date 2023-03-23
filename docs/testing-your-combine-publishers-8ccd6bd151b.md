# 如何测试您的联合收割机出版商

> 原文：<https://betterprogramming.pub/testing-your-combine-publishers-8ccd6bd151b>

## 发布者测试变得简单

![](img/938792881f059ceee2c0941014e13c91.png)

在用了 [ReactiveCocoa](https://github.com/ReactiveCocoa) 、 [RxSwift](https://github.com/ReactiveX/RxSwift) 一段时间后，我得到了转用苹果 [Combine](https://developer.apple.com/documentation/combine) 框架的机会。当然，现在我需要对所有这些出版商进行单元测试。

使用流有一些很大的好处，比如更干净的代码，更少的副作用，更小的类。但是它也有一个缺点，测试有点困难。

我所做的是，我试图让测试变得简单一点。

# 问题

假设我们有一个返回设备当前状态的协议`Device`。

```
**protocol** IDevice {
 **func** deviceState() -> String
}
```

此外，我们有一个`UseCase`,如果设备根据它的状态是开还是关，我们希望得到一个 bool。如果没有合并，我们代码将如下所示:

在我们的单元测试中，我们需要有一个模拟`deviceState()`的`IDevice-Mock`，这样我们就可以测试每个案例。

# 公开方法

现在，使用 Combine，我们将更改函数，使其更具“反应性”。所以，我们用发布者替换了返回值。

测试变得很棘手。我们需要订阅`isOn()`，嘲笑`deviceState()`。所以，我们开始吧。

现在我们创建了一个`expectedValues`数组，其中列出了我们期望从`Publisher<Bool>` *，*获得的值，我们订阅了这些值，保存在接收器中发送，然后使用`deviceStateSub`发送设备状态。

当然，如果发布者的输出是不等价的，这就行不通了。

所以，我写了一些帮助函数，让我的生活更简单。

# 解决办法

首先，一个助手函数是`expectCompletion`，我们用它来检查一个可观察对象是否完成。

助手函数将返回预期值。然后我们只需要发送来自模拟的完成，并等待期望。

我们现在将帮助函数`expectValue`添加到我们的扩展中，它将检查等价的值:

在`expectValue` 函数的`equals`参数中，我们给出了我们期望从`.isOn()`发布者处得到的值。之后，我们从我们的主题`deviceStateSub`发送值。

所以，我们期望当设备状态为`Off`时，`isOn()`返回`false`，如果为`On`，它应该返回`true`。

最后，我们有一个类似的函数来处理更复杂的检查。想象一下我们的`IsOnUseCase`也有另一个函数，它返回一个带有文本的不等价的自定义对象。

我们的测试函数，`expectValue`、*、*也将接受闭包列表*。*

现在我们需要使用闭包来检查`isOn()`发布者的结果。

希望你觉得以上功能有用。

快乐的单元测试。
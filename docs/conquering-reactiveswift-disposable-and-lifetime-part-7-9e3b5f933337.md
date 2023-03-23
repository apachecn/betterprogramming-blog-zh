# 征服反应 Swift:一次性和终生(第七部分)

> 原文：<https://betterprogramming.pub/conquering-reactiveswift-disposable-and-lifetime-part-7-9e3b5f933337>

## 如何使用作用域原语控制交互

![](img/f6e951c9ccc053327e6fdae5cc3a269a.png)

照片由[狂鱼数码](https://unsplash.com/@madfishdigital?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/throw-your-laptop-away?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

欢迎来到我的“征服[反应 Swift](https://github.com/ReactiveCocoa/ReactiveSwift) ”系列的第 7 部分。[第 6 部分](https://medium.com/fueled-engineering/https-medium-com-hsusmita-conquering-reactiveswift-action-tutorial-6-58dae11300b5)详细处理了*动作* 。在本文中，我们将在类别 s *cope* 下讨论原语。范围原语有助于指定*源*和*消费者*原语之间的交互的生存期。在这个品类中，我们有*一次性*和*终身*。

# 可任意处理的

ReactiveSwift 的作者对“一次性”的定义如下:

> 表示可以“释放”的东西，通常与释放资源或取消工作相关联。

为了理解它的用例，让我们回顾一下上一篇文章中的问题:每隔五秒钟打印一条经过时间的消息。

为了做到这一点，我们创建了一个信号，在接下来的 50 秒内每 5 秒钟发出一个整数值。然后，我们创建了一个观察器，它观察信号并打印信号发出的整数。当它附着在一个信号上时，它就被那个信号所保留。假设我们只想观察前五次出现的信号。为了实现这一点，我们需要保存由`signal.observe(signalObserver)`返回的一次性数据，并在五次发生后调用`disposable.dispose`。

简单一次性的使用案例

# 一次性的变体

让我们更深入地了解一次性用品。`Disposable`是一种协议，其定义如下:

```
public protocol Disposable: class { var isDisposed: Bool { get } func dispose()}
```

一次性用品有三种类型:系列型、范围型和复合型。

## 系列一次性用品

`SerialDisposable`处理包装好的一次性用品，并允许更换。这在您希望前一个观察在新观察开始时结束的情况下非常有用。

## 范围内的一次性用品

`ScopedDisposable`在废弃时处理包装好的一次性用品。

## **复合一次性用品**

保留一系列其他一次性用品。当它的`dispose`方法被调用时，所有可处置的集合被处置。当你想停止监听所有的信号和当一个类被释放时的`signalProducers`时，这个可支配是有用的。

为了理解每种类型的用例，让我们看一个例子。

# 示例:类计时器

让我们用一个名为`start(interval: Int)`的方法构建一个类`Timer`，打印给定时间间隔内经过的时间。

为此我们可以利用我们在[第六部分](https://medium.com/fueled-engineering/https-medium-com-hsusmita-conquering-reactiveswift-action-tutorial-6-58dae11300b5)中写的`signalProducerGenerator`。这将一个整数作为参数，并返回一个可以在给定的时间间隔内发出值的`signalProducer`。这是我们的`Timer`类的样子。

让我们运行下面的代码片段。

## 片段 1

```
let timer = Timer()timer.start(interval: 2)DispatchQueue.main.asyncAfter(deadline: .now() + 4.0) { timer.start(interval: 1)}
```

这将为我们提供以下输出:

```
timeElapsed = 0 : interval = 2timeElapsed = 2 : interval = 2timeElapsed = 4 : interval = 2timeElapsed = 0 : interval = 1timeElapsed = 1 : interval = 1timeElapsed = 6 : interval = 2timeElapsed = 2 : interval = 1timeElapsed = 3 : interval = 1timeElapsed = 8 : interval = 2timeElapsed = 4 : interval = 1timeElapsed = 5 : interval = 1timeElapsed = 10 : interval = 2timeElapsed = 6 : interval = 1timeElapsed = 7 : interval = 1timeElapsed = 12 : interval = 2timeElapsed = 8 : interval = 1timeElapsed = 9 : interval = 1timeElapsed = 14 : interval = 2timeElapsed = 16 : interval = 2timeElapsed = 18 : interval = 2
```

请注意，两个观察都是活动的。然而，现在我们希望当我们再次调用 start 方法时，前面的观察停止。对此，我们可以利用`SerialDisposable`。`SerialDisposable`有一个名为`inner`的属性，类型为`Disposable`。当我们给这个`inner`属性分配任意观察的一次性物品时，它将处理旧的内部一次性物品。

我们可以通过以下方式实现这一目标:

现在，当我们运行代码片段 1 **，**时，我们得到以下输出。注意对`interval = 2`的观察已经停止。

```
timeElapsed = 0 : interval = 2timeElapsed = 2 : interval = 2timeElapsed = 4 : interval = 2timeElapsed = 0 : interval = 1timeElapsed = 1 : interval = 1timeElapsed = 2 : interval = 1timeElapsed = 3 : interval = 1timeElapsed = 4 : interval = 1timeElapsed = 5 : interval = 1timeElapsed = 6 : interval = 1timeElapsed = 7 : interval = 1timeElapsed = 8 : interval = 1timeElapsed = 9 : interval = 1
```

然而，我们还有另一个问题。如果对象被释放，当前的观察不会结束。让我们运行下面的代码片段。

## **片段二**

```
var timer: Timer? = Timer()timer?.start(interval: 2)DispatchQueue.main.asyncAfter(deadline: .now() + 4.0) { timer?.start(interval: 1)}DispatchQueue.main.asyncAfter(deadline: .now() + 6.0) { timer = nil}
```

我们得到以下输出:

```
timeElapsed = 0 : interval = 2timeElapsed = 2 : interval = 2timeElapsed = 4 : interval = 2timeElapsed = 0 : interval = 1timeElapsed = 1 : interval = 1timeElapsed = 2 : interval = 1timeElapsed = 3 : interval = 1timeElapsed = 4 : interval = 1timeElapsed = 5 : interval = 1timeElapsed = 6 : interval = 1timeElapsed = 7 : interval = 1timeElapsed = 8 : interval = 1timeElapsed = 9 : interval = 1
```

为了解决这个问题，让我们使用`ScopedDisposable`。它有另一个名为`inner` disposable 的属性，当`ScopedDisposable`释放时，这个属性被释放。所以我们需要把`SerialDisposable`包装成`ScopedDisposable` **。**这是它的样子:

当我们现在运行代码片段 2 时，我们得到以下输出。请注意，当`timer`变为零时，所有观察在 6 秒钟后停止。

```
timeElapsed = 0 : interval = 2timeElapsed = 2 : interval = 2timeElapsed = 4 : interval = 2timeElapsed = 0 : interval = 1timeElapsed = 1 : interval = 1
```

现在让我们考虑另一个场景。假设我们不希望旧的观察在新的观察开始时结束。但是我们希望所有这些都在对象变为零时结束，并且只有在对象变为零时。这里我们需要的是`compositeDisposable`而不是`serialDisposable`。为此，我们将修改`start(interval: Int)`,继续将所有一次性用品添加到`CompositeDisposable`,如下所示:

```
**self**.compositeDisposable += 
**self**.timerSignalProducer(interval: interval)
.startWithValues { value **in** print("timeElapsed = \(value) : interval = \(interval)")
}
```

我们必须将这个`compositeDisposable`包装在`scopedDisposable`中，这样当`timer`变为零时，它就会被处理掉。

以下是完整的代码:

当我们现在运行代码片段 2 时，我们得到以下输出。注意，两个观察器都是活动的，直到`timer`变为零。

```
timeElapsed = 0 : interval = 2timeElapsed = 2 : interval = 2timeElapsed = 4 : interval = 2timeElapsed = 0 : interval = 1timeElapsed = 1 : interval = 1timeElapsed = 6 : interval = 2
```

# 一生

文档对*寿命*的描述如下。

> 表示对象的生存期，并提供一个钩子来观察对象何时被初始化。

生命有一些有用的属性，可以帮助我们做决定。

```
public var ended: Signal<Never, Never>**public var** hasEnded: Bool
```

例如，当观察`Signal`或`SignalProducer`时，如果不再有任何东西观察它们，那么继续发射值就没有意义。这就是为什么在`func timerSignalProducer(interval: Int) -> SignalProducer<Int, Never>`中，我们只在`lifetime.hasEnded`为假时才发送值。

# 结论

这都是一次性和终身的。在下一篇文章中，我们将讨论 ReactiveSwift 中可用的操作符。

以下是您可能想要关注的文章的完整列表:

1.  [反应 Swift 简介(第一部分)](https://medium.com/@hsusmita4/reactive-programming-introduction-to-reactiveswift-c3fcef5e896)
2.  [征服反动派 Swift:原语(下)](https://medium.com/@hsusmita4/conquering-reactiveswift-primitives-part-2-3f39d47d7bc)
3.  [征服反应 Swift:信号与观测器(第三部分)](https://medium.com/@hsusmita4/conquering-reactiveswift-signal-and-observer-part-3-8b7da35fe33a)
4.  征服反动派:信号生产者(第四部分)
5.  [征服反应 Swift:属性(第五部分)](https://medium.com/@hsusmita/https-medium-com-hsusmita-conquering-reactiveswift-property-tutorial-5-c4e3dd38e94d)
6.  [征服反动 Swift:行动(第六部分)](https://medium.com/@hsusmita/https-medium-com-hsusmita-conquering-reactiveswift-action-tutorial-6-58dae11300b5)
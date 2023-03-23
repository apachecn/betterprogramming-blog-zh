# JavaScript 中的承诺与观察

> 原文：<https://betterprogramming.pub/promises-vs-observables-in-javascript-208ec84f701b>

## 直观的比较

![](img/c041031637fff136adbacbc785a0f59b.png)

[毛绒设计工作室](https://unsplash.com/@plushdesignstudio?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

根据来自 [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 的官方定义，`Promise`是一个“代表异步事件的最终完成或失败”的对象。这意味着一旦一个`Promise`被创建，它就被异步执行，当它解析或拒绝时，结果被传递给适当的处理函数，`Promise`完成。就这么简单！以下示例演示了这一简单的功能:

```
Output:# After 5 seconds, both results are printed immediatelyMessage: 12
```

我们可以很容易地使用`Promise`来执行 HTTP 调用，并将多个调用按顺序链接起来或者并行运行——并以一种直接的方式处理错误。

但是，看看这段代码和输出，我们可以看到`Promise`甚至都不是与反应式数据流交互的理想方法。

首先，`Promise` s 在声明的瞬间被执行。在上面的例子中，两个`Promise`一旦被声明就开始执行。这就是为什么第二个`Promise`的输出会在第一个`Promise`解析后立即打印:第一个`Promise`在五秒后解析，而第二个在两秒后解析，在第一个解析之前。

其次，没有取消`Promise`的机制——它们是不可变的。这意味着在处理需要很长时间才能解决的 HTTP 调用时会出现严重的问题。在这种情况下，我们需要依靠一些第三方库来执行额外的逻辑。

最后，但最重要的是，`Promise` s 处理产生单个值的数据源。也就是说，我们不能将`Promise`用于异步数据序列，比如鼠标/键盘事件。

为了克服这些缺点，RxJS 库提供了以相同方式处理每个异步数据源的方法，无论是鼠标/键盘还是 HTTP 事件。在 RxJS 中，我们必须从流的角度来考虑，流可以定义为一段时间内发出的一系列事件。这些流由几个组件组成(生产者、消费者和数据管道)。现在，我们只需要关注生产者，它是由随时间异步生成事件并发送给所有消费者的东西创建的。在 RxJS 中，生产者被称为*可观测量*。

下面的例子演示了如何通过可观测量来克服`Promise` s 的上述缺点。

我正在从鼠标事件`mousedown`、`mouseup`和`mousemove`中创建可观察对象。我也链接这些可观察的事物，最后，订阅这个链接。

首先，异步事件流和链接流保持休眠状态，直到订阅者开始监听。这样，`Promise`的一个主要缺点就被可观测量克服了。

另一方面，如果你仔细观察链接的可观察对象，我正在定义一个条件来取消它:当一个`mouseup` 事件被发出时，取消可观察对象并停止监听。我们也正在克服`Promise` s 的另一个主要缺点。

最后，我们可以说，随着时间的推移，观察对象可以与异步值一起工作，而不仅仅是像`Promise` s 的情况那样的单个值。在这个示例中，我有三个从鼠标事件创建的观察对象，以及一个链接的观察对象，当鼠标被单击和拖动时开始发出值，当鼠标键被释放时停止。

在您离开之前，一定要看看这个关于从异步事件中创建可观测量的精彩动画[。](https://reactive.how/fromevent)

保重。

# 资源

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) [## 承诺

### Promise 对象表示异步操作的最终完成(或失败),及其结果…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
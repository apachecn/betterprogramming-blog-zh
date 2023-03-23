# Swift 并发编程一瞥

> 原文：<https://betterprogramming.pub/a-look-at-concurrent-programming-in-swift-ce886bd49c31>

## 如何使用 concurrentPerform 轻松编写并行代码

![](img/c7bbec173c50c854cce06fbceec224ab.png)

照片由[Nathana rebou as](https://unsplash.com/@nathanareboucas?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

如果你关注我的博客，你会知道我喜欢并发编程。我对同时运行不同的任务、收集结果并节省大量时间的想法非常着迷。

本周，当我在我团队的当前项目中工作时，我发现了一个我并不知道的新 API:`[DispatchQueue.concurrentPerform](https://developer.apple.com/documentation/dispatch/dispatchqueue/2016088-concurrentperform)`。这个小巧精致的 API 让我们可以一起运行批量工作，并为我们处理所有的并发性。

让我们一起来探索一下。

# 理论上的并发执行

当使用一个新的 API 时，首先要做的是阅读它的文档:

> 该方法实现了一个**高效并行** for 循环。分派队列执行提交的块指定的次数，并且**在返回之前等待所有迭代完成**。如果目标队列是并发队列，那么这些块并行运行，因此必须是可重入安全的。— [苹果开发者](https://developer.apple.com/documentation/dispatch/dispatchqueue/2016088-concurrentperform)

我在这里强调了两条主要信息:

1.  什么是 T1:一个有效的并行 T2 循环。
2.  它的行为:它等待所有的计算完成。

第二点特别有趣:我们不必为了等待所有生成的线程完成而弄乱锁、信号量、障碍和类似的工件。API 为我们做了这些。然而，我们可能不得不使用这些控制机制来访问共享资源。

## 探索签名

这个函数的特征是:

```
concurrentPerform(iterations: Int, execute work: (Int) -> Void)
```

`iterations`参数告诉`DispatchQueue`模块`execute`必须执行多少次。`execute`模块将`Int`作为参数。这是当前迭代的索引。

这个参数对于决定我们想要操作集合中的哪个元素或部分很有用。例如，让我们分析这段代码:

这段代码为`Array`类型添加了一个非常简单的特性:并发运行`map`操作的可能性。让我们来看一下突出显示的步骤:

1.  检查我们是否需要运行一些操作。如果数组为空，`mapping`将返回一个空数组。
2.  准备要返回的数组。通过一个`map`操作，我们知道返回的数组必须与输入的数组大小相同。我们通过将所有值预设为第一个元素的映射值来准备它。我们可以安全地这样做，因为我们知道数组不是空的。
3.  使用`concurrentPerform`对所有元件运行`map`功能。迭代次数为`self.count`。注意这个块的`index`参数是如何被用来访问`self`变量和索引目标数组的。

*注意:要返回的数组是在每次迭代中从不同的线程中访问的。这不是线程安全的。为此我们不得不引入* `*os_unfair_lock*` *来防止应用崩溃。*

这个新功能的用法与标准的`map`用法没有什么不同。我们使用`concurrentMap`代替标准`map.`

# 表演

现在真正的问题是使用这种方法是否值得麻烦。

正如在许多工程环境中一样，这取决于您的问题。让我们来看一些基准测试，在这些测试中，我们运行以下代码:

这里，我们只取数组`10`、`100`、`1_000`、`10_000`、`100_000`和`1_000_000`并计算它们的平方——首先用一个`concurrentMap`然后用一个连续的`map`。结果时间为:

```
// 10 elements: **Concurrent Map time: 0.3299713134765625 ms
Default Map time: 0.03802776336669922 ms**// 100 elements:
**Concurrent Map time: 0.09799003601074219 ms
Default Map time: 0.04303455352783203 ms**// 1000 elements:
**Concurrent Map time: 0.3190040588378906 ms
Default Map time: 0.46896934509277344 ms**// 10000 elements:
**Concurrent Map time: 3.3109188079833984 ms
Default Map time: 2.4089813232421875 ms**// 100000 elements:
**Concurrent Map time: 30.785083770751953 ms
Default Map time: 23.782014846801758 ms**// 1000000 elements:
**Concurrent Map time: 311.1530542373657 ms
Default Map time: 246.08397483825684 ms**
```

这有点令人失望，不是吗？似乎我们并没有从使用并行方法中获得什么。另一方面，我们为线程跳跃支付了大量的开销。

主要原因是我们使用锁来避免崩溃。锁创建了一个单点同步，基本上将并发计算移回串行计算。另一点需要考虑的是，我们在`execute`区块执行的工作速度极快。这只是一个简单的乘法，并且该操作不需要大量的 CPU 时间。

## 繁重的作业

让我们试着稍微修改一下我们的代码，将`square`操作转换成计算量更大的操作——也许添加一个`0.01 sec`的`sleep`。

基准测试代码现在看起来像这样:

您可能会注意到，我们刚刚在第 8 行和第 15 行添加了几个`Thread.sleep`调用。

结果如下:

```
// 10 elements:
**Concurrent Map time: 36.27192974090576 ms
Default Map time: 114.11595344543457 ms**// 100 elements:
**Concurrent Map time: 159.67702865600586 ms
Default Map time: 1161.31591796875 ms**// 1000 elements:
**Concurrent Map time: 1427.2420406341553 ms
Default Map time: 11396.499037742615 ms**// 10000 elements:
**Concurrent Map time: 13940.824031829834 ms
Default Map time: 113169.63696479797 ms**
```

*注意:使用* `*100_000*` *和* `*1_000_000*` *元素执行顺序代码需要很长时间。因此，我阻止了它。我们已经可以看到* `*concurrentPerform*` *即使在这么小的样本上也有多好了。*

现在我们正在使用一个繁重的操作，结果是显而易见的:`concurrentMap`比标准的`map`操作好一个数量级——即使只有几个元素！

## 定量

还有一个用例是`concurrentPerform`优于标准的顺序方法。

通过分析锁和并发性的问题，我们可以看到:

1.  做一个手术。
2.  拥有结果数组上的锁。
3.  设置结果。
4.  打开锁。

我们基本上是在为每一个操作支付锁定和解锁的成本。

利用并发能力的另一种方法是将多个操作成批处理，并且每批只获取一次锁，即使是少量操作也是如此。

让我们看一下这个例子，在这个例子中，我们对`10_000_000`整数的平均值进行批处理计算:

在这个代码片段中，我们创建了一个包含 1000 万个整数的数组，然后首先使用标准的顺序方法，然后使用并发方法来计算平均值。

有趣的部分在第 17 行和第 33 行之间。在这里，我们定义批处理大小并准备一个名为`temp`的目标数组。

在该模块中，我们根据`iteration`指数计算指定批次的平均值。例如，您可以看到第一次迭代计算了从`0`到`99_999`的元素的平均值。

然后，我们将结果存储在`temp`数组中，只为`10_000`操作获取一次锁。基准的输出是:

```
The sequential average is: **0.0027276\.** It tooks **2.6642** seconds
The concurrent average is: **0.0027276**. It tooks **1.4912** seconds
```

您可以看到并发平均时间大约是顺序平均时间的一半。

# 结论

在今天的文章中，我们探索了`DispatchQueue.concurrentPerform` API。我在本周之前不知道它，我认为它是一项宝贵的知识，因为它可以加快我们的算法。

我们研究了几个使用这种方法非常有效的用例:当处理繁重的操作时，以及当我们可以一起批处理快速操作时。
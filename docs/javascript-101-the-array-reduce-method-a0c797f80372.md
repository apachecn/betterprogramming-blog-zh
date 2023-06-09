# JavaScript 101:数组 Reduce 方法

> 原文：<https://betterprogramming.pub/javascript-101-the-array-reduce-method-a0c797f80372>

## 你曾经想知道的关于这种方法的一切，以及为什么你应该更经常地使用它

![](img/29b22f7df084b301fe11278939f432df.png)

JavaScript 数组方法非常流行和有用。然而，由于某种原因，`reduce`方法被落在了宣传列车的后面。人们不仅忽视它，而且还主动回避它。

不管原因是什么，我已经把向您展示它有多棒以及最重要的是它有多容易使用作为我的使命。

所以让我们言归正传。

# Reduce 方法的目的是什么？

该方法的目的是将回调函数应用于每个元素，但与`map`或`forEach`方法不同，它还跟踪该回调的最后返回值。最终，返回最后一个返回值。

这是一种复杂的说法，这个方法以某种方式帮助你减少一个数组的元素。通常，这种方法用于将元素列表缩小到单个值，但这不一定是它的唯一用途。

关于`reduce`有趣的一点是，你可以把它用作`map`、`filter`或任何你喜欢的方法。那为什么讨厌呢？大家来了解一下吧！

# Reducer 方法的语法

从外面看，这是一个很简单的方法。它只需要两个参数:回调和初始值。

```
array.reduce(callback, initialValue)
```

我认为真正复杂的部分是回调函数的签名。由于它有几个可选的参数，恐惧就产生了。

第一个参数回调需要以下属性:

*   `previousValue`:这是上一次回调执行的返回值，或者如果是第一个，则是传递给`reduce`方法的第二个参数的内容(`initialValue`)。
*   这是你正在处理的数组的当前元素。
*   `currentIndex`(可选):这个告诉你你在数组中的位置。如果你的逻辑在某种程度上依赖于元素的位置，这是很有用的。否则可以忽略。
*   `array` (可选):这是您正在处理的完整数组。除非你真的因为什么原因需要它，否则你可以忽略它。

`reduce`的第二个参数`initialValue`，是第一次调用回调函数时分配给`previousValue`的值。

仅此而已。看到了吗？一点都不恐怖！

# 现在，我们可以用 Reduce 方法做什么呢？

有趣的事情开始了。用这种方法我们能做什么？

答案是:“一切！”

正如我之前说过的，你可以把`reduce`看作是你真正喜欢的所有其他方法的构建模块。

例如，考虑下面显示的`map`方法:

每一次迭代，我们都会得到一个新的数组，在那里我们会添加每个元素的 double 值。而初始值(`reduce`的第二个参数)是空的新数组。

那`filter`的方法呢？

你现在开始明白规律了，对吧？回调函数的主体允许您做任何您想做的事情，因此您可以模仿任何其他方法。

在这种情况下，我只添加字符串，忽略数字。之后，我将返回当前的字符串列表，这样下一次迭代也可以这样做。

![](img/c213d3d1d6c511cff44fb79260df44b2.png)

但这不是你来这里的原因。这很有趣，但是如果你想过滤一个列表，你可以使用`filter`，如果你想转换每个元素，你可以使用`map`。那么`reduce`还能做什么呢？

## `reduce`更具体的用途

`reduce`方法的另一个常见用例是将一组元素简化为一个元素。例如，在我的这个项目中，我使用了一种[遗传算法来寻找最佳主题](https://deleteman.hashnode.dev/using-a-genetic-algorithm-to-find-your-users-ideal-theme)，我必须通过将一系列距离相加来计算主题的总得分。我用这段代码解决了这个问题:

忽略实际问题；在某种程度上真实的场景中，这是对`reduce`的一种非常有效的使用。我将复杂对象的可变长度列表简化为一个数字，即`score`。为此，我从设置为 0 的`score`开始，然后遍历列表，并将每个主题的当前值(该主题的距离)添加到累积分数中。

最终结果是所有距离相加。

当然，我可以在这里简单地使用`forEach`,因为我在迭代完整的元素列表，但是`reduce`为这些用例提供了非常简单的语法。

我发现的另一个有趣的用例是运行函数管道，每个函数都使用前一个函数的输出作为其输入，如下面的代码所示:

对于这个例子，我试图将函数管道引入一个更加实际和真实的场景。如果您做过后端开发，您可能会遇到类似的情况，您必须对您的“请求”运行一组验证和转换

该逻辑可以被隔离到单独的函数(通常是单独的库)中，然后添加到一个列表中，它们可以在这样的管道中运行。

每个函数要么转换要么对前一个函数返回的对象执行某种验证。由于这是一个更真实的场景，所以当出现问题时，我们会抛出错误来打破循环。

或者，我们可以找到一种方法来收集所有错误，然后最终决定是否继续请求。

但是这里的要点是,`reduce`方法让我们在一行代码中运行整个管道。那是强大的。

我不会一直向你扔用例。现在，你可能明白没有真正的理由害怕这种方法，因为它背后没有黑魔法。

事实上，如果你像我一样，你可能会有点失望。很像一个孩子，当他们被教导如何手指分裂的“把戏”工作。这里没有魔法，但是你的工具箱里增加了一个新的强大的工具，所以开心点吧。

你对这个方法还有疑问吗？这些例子中有哪个对你来说没有意义吗？留下评论，我会尽力帮你看懂的！
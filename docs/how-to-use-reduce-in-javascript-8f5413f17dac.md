# 如何使用。JavaScript 中的 reduce()

> 原文：<https://betterprogramming.pub/how-to-use-reduce-in-javascript-8f5413f17dac>

## 使用一些本地 JavaScript 数组功能的概述和快速指南

![](img/79177a8f1c074cda22b6784764c3e578.png)

照片由 [Hafidh Satyanto](https://unsplash.com/@hafidhsatyanto?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当我第一次试图弄清楚`.reduce()`函数是做什么的时候，它困扰了我很长一段时间。出于某种原因，我就是拿不出来。现在我终于解开了这个谜，我希望我可以帮助其他人获得他们的灵光一现。让我们开始吧。

# 这是如何？reduce()函数工作？

基本上，`.reduce()`接受我们提供的一个 reducer 函数，它将针对给定数组中的每一项执行。结果将作为`accumulator`变量传递给下一次迭代。该函数仅返回`accumulator`变量，并将在最后一次迭代后返回。正如人们所料，该函数从左向右迭代——稍后您会明白我为什么要提到这一点。

让我们举下面的例子。我们有一群游客，我们想知道旅游团的平均年龄。我们可以使用传统的`for`循环来确定这一点，如下例所示。

但是有一种更干净的方法，它有一个额外的但经常被忽视的好处。让我们在这里使用`.reduce()`函数来组合年龄，看看它可能是什么样子。

这里我们可以看到我们不再需要使用传统的`for`循环。我们可以使用 JavaScript 本身提供的原生功能，使代码更加易读和简洁，额外的好处是我们新的`combinedAges`变量已经被不变地创建了。

既然我们已经把旅游团的年龄减少到一个总数，我们就可以很简单地确定这个旅游团的平均年龄。唷！那很容易。

另一个简单的例子可能是我们需要将一组对象简化为一个对象的场景。我们可能不知道返回的对象的数量，或者我们可能正在构建一些配置对象，不管是哪种情况。让我们来看看这个。

在这种情况下，结果将是一个组合了键的平面对象，更值得注意的是，下一次迭代的`currentValue`覆盖了`accumulator`的键。如果我们在数组中有未知数量的对象，我们就不能使用 spread 语法，这使得它成为一种可能派上用场的替代方法。

# 如果我们需要索引呢？

在一些用例中，我们可能需要知道迭代的当前索引。令人欣慰的是，我们创建并传递给`.reduce()`函数的 reducer 不仅仅接受两个参数；它需要四个人。

```
const fooReducer = (acc, value, index, array) => (...)const bar = [...]
const value = bar.reduce(fooReducer)
```

第一个参数始终是`accumulator`值，后跟`currentValue`；但是，如果需要的话，我们可以传递`index`，以及调用该函数的`array`。

这为我们创建和提供的 reducer 的逻辑提供了很大的灵活性。

# 数组不是从 1 开始的

实际的`.reduce()`函数本身并不只是把我们传递给它的 reducer 作为参数；它也可以取初始值。

上面，我们可以看到在第一次缩减中，我们没有提供任何初始的`accumulator`值。因此，在第一次迭代中，`accumulator`被设置为调用它的数组中的第一个值。

在第二种情况下，我们提供一个 0 的初始`accumulator`值，在第一次迭代中，我们可以看到`accumulator`实际上被设置为 0。

这可能是需要记住的事情，当然这取决于您的函数用例。

# 从左到右再回到左

如果您不熟悉使用`.reduce`函数，那么您可能不知道实际上还有一个`.reduceRight`函数。然而，这个函数的工作方式略有不同，并且在某些值的累积顺序可能很重要的用例中非常有用。

那么`.reduceRight`和`.reduce`功能有什么不同呢？很简单，`.reduceRight`函数从右向左迭代，而不是从左向右迭代。它向后遍历给定的数组。

然而，在所有其他方面，这两个函数的表现完全相同。

![](img/7d9d0b78507795e5346a40b1120ae835.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 最后一个结论！

总之，使用 JavaScript 的原生数组功能不仅可以使我们的代码更加简洁易读，而且还可以帮助引入对象不变性，，这一点经常被忽略。

感谢您的阅读。我希望你喜欢并学到了一些东西。如果你碰巧有任何反馈、批评或贡献，请随意写在下面的评论区。

再见了。

# 参考

这里有一些参考资料，如果您想了解更多信息或者想在自己的代码库中实现这个原生函数(如果您还没有这样做的话),可能会引起您的兴趣。

[MDN。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)
减少() [MDN。reduceRight()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight)
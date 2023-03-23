# 如何使用 Kotlin 的内置函数来测量代码执行时间

> 原文：<https://betterprogramming.pub/how-to-use-kotlins-built-in-functions-to-measure-code-execution-time-1759b2ac90aa>

## 调查性能问题或找出最佳实现

![](img/e2126a4c5c26e1ab57588db977fab23a.png)

马克·赖歇尔在 [Unsplash](https://unsplash.com/s/photos/kotlin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Kotlin 提供了测量执行时间的简单方法。这在很多地方都会派上用场。你可以用它来比较算法或者检查你的代码哪个部分慢。

让我们首先看看测量执行时间的经典(Java 风格)方法。只有这样，我们才能体会到科特林的好处。

作为示例，我们比较了`ArrayList`和`LinkedList`的性能。我们测量 5000 万个整数相加，然后求和所需的时间。查看 [GitHub](https://gist.github.com/ablx/586d701a839b821253244c5d81cb36e4) 上的代码。

# 经典的 Java 风格的方式

如果您使用 Java 已经有一段时间了，您可能会看到类似这样的内容(显然，这是在 Java 中，而不是在 Kotlin 中)。

这种方法工作得很好，但是也有缺点。

首先，我们必须将结果从纳秒转换为秒。其次，我们需要引入一两个变量和一长串代码来得到我们的结果。

如果您需要测量同一方法的不同部分，这可能会变得混乱。很容易发生的情况是，你用错误的变量计算差异。

很容易看出有一个简单的解决方案:提取一个测量时间的方法。但是我们为什么要重新发明轮子呢？

# 科特林测量时间的方法

## measureTimeNanos 和 measureTimeMillis

Kotlin 中最简单的方法是使用来自`kotlin.time`的`measureTimeNanos`和`measureTimeMillis` 。

如果你看看引擎盖下面，这些功能与你在上面看到的一样:

这些方法返回执行时间，所以我们不需要手动计算它们。但是，我们仍然需要手动转换单位。有一个很大的警告！你不能得到被测函数的返回值。

你可以使用的另一个方法是`[measureTime](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.time/measure-time.html).` 返回一个`[Duration](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.time/-duration/)` [对象](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.time/-duration/)。`[Duration](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.time/-duration/)`为您提供将持续时间转换成您想要的时间单位的方法。但是，返回值仍然会丢失:

当然，还有更好的解决方案。

# 两全其美:测量时间价值

到目前为止，我可以得到简洁的语法，但没有返回值或返回值和臃肿的语法。幸运的是，有一个两全其美的方法:`[measureTimedValue](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.time/measure-timed-value.html)`。

你可以进一步使用另一个很酷的 Kotlin 特性，即析构:

这使您能够在代码中加入时间度量。用`measureTimedValue`把它包起来，用 Kotlin 的[析构](https://kotlinlang.org/docs/destructuring-declarations.html)就可以一气呵成的得到时长和代码结果。

如果你看看 GitHub 上的代码，你可能会注意到注释`[@OptIn(ExperimentalTime::class)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.time/-experimental-time/)`。我们需要这个，因为 JetBrains 将更先进的时间测量方法标记为实验性的。这些方法将来可能会改变它们的行为或语法。你需要记住这一点。无论如何，我不建议在产品代码中长期使用这些方法。

# 结论

在 Kotlin 中测量执行时间非常容易。您可以根据需要插入它，而不会因为不明确的变量而污染您的范围。

以下是一些有用的链接:

*   [kotlin.time API 文档](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.time/)
*   [科特林的破坏](https://kotlinlang.org/docs/destructuring-declarations.html)
*   在 [GitHub](https://gist.github.com/ablx/586d701a839b821253244c5d81cb36e4) 上完成代码。

感谢您的宝贵时间！

```
**Want to Connect?**Subscribe to my newsletter so you never miss a new post:
[https://verbosemode.dev/subscribe](https://verbosemode.dev/subscribe)
```
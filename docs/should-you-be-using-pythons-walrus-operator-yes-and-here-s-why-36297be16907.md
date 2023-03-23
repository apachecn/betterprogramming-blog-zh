# 你应该使用 Python 的 Walrus 操作符吗？(是的。原因如下)

> 原文：<https://betterprogramming.pub/should-you-be-using-pythons-walrus-operator-yes-and-here-s-why-36297be16907>

## Python 有争议的赋值表达式——被称为 walrus 运算符——可以改进您的代码，现在是您开始使用它的时候了！

![](img/aca76b3f9b100ae7ead6e240be3a0988.png)

乔纳森·库珀在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

赋值操作符——或者我们都知道的 walrus 操作符——是 Python 中的一个特性(从 3.8 开始),但它仍然有一些争议，许多人对它有毫无根据的厌恶。

在本文中，我将试图说服您，walrus 操作符确实是语言的一个很好的补充，如果您正确使用它，那么它可以帮助您使您的代码更加简洁和可读。

# 基础/必需品

如果您还不熟悉`:=`，让我们首先回顾一些基本的用例，这些用例可能会说服您尝试一下这个 Python 特性。

我想展示的第一个例子是如何使用 walrus 操作符来减少函数调用的次数。让我们想象一个名为`func()`的函数，它执行一些非常昂贵的计算。计算结果需要很长时间，所以我们不想多次调用它。你可以这样做:

在上面声明的第一个列表中，`func(x)`被调用了三次，每次都返回相同的结果，这浪费了时间和计算资源。当使用 walrus 操作符重写时，`func()`只被调用一次，将其结果赋给`y`，并在剩余的两个列表值中重用它。

你可能会说，*“我在列表声明前加* `*y = func(x)*` *就可以了，不需要海象！”*是的，你可以，但是那是一个额外的、不必要的代码行，乍一看——不知道`func(x)`超级慢——可能不清楚为什么额外的`y`变量需要存在。

如果你不相信上面的话，我还有一个。用同样昂贵的`func()`考虑下面的列表理解:

在第一行中，`func(x)`在每个循环中被调用两次。相反——使用 walrus 操作符——我们在`if`语句中计算一次，然后重用它。代码长度是一样的，两行的可读性都是一样的，但是第二行的效率是第一行的两倍。您可以避免使用 walrus 操作符，同时通过将它改为一个完整的`for`循环来保持性能，但这需要五行代码。

walrus 操作符最常见的用例之一是减少嵌套条件，比如在使用正则表达式匹配时。代码如下:

通过使用 walrus，我们将匹配代码从七行减少到四行，同时通过移除嵌套的`if`使其更具可读性。

名单上的下一个是所谓的“一圈半”习语。它看起来是这样的:

通常的解决方案是使用一个虚拟的无限`while`循环，将控制流委托给`break`语句。相反，我们可以使用 walrus 操作符来重新分配`command`的值，然后在同一行的`while`循环的条件中使用它，这将使代码更加简洁。

类似的简化也可以应用于其他`while`循环，例如，当逐行读取文件或从套接字接收数据时。

# 就地积累数据

继续讨论一些更高级的 walrus 操作符的用例。这使得就地积累数据成为可能。代码如下:

前两行展示了如何利用 walrus 操作符来计算运行总数。对于这样一个简单的例子，来自`itertools`的函数，比如`accumulate`是一个更好的选择，如下面两行所示。然而，对于更复杂的场景，使用`itertools`会很快变得不可读。而且，在我看来，有`:=`的版本比有`lambda`的版本好看多了。

如果你仍然不相信，检查一下文档中的`accumulate` [示例(例如，累计利息或逻辑图示例)，这些示例不可读。尝试重写它们以使用赋值表达式。它们看起来会更好。](https://docs.python.org/3/library/itertools.html#itertools.accumulate)

# 命名 f 字符串中的值

这个例子展示了`:=`的可能性和局限性，而不是最佳实践。

如果你愿意，你可以在 f 弦中使用海象操作符。这里有一个例子:

在上面的第一个打印语句中，我们使用`:=`来定义变量`today`，然后在同一行中重用该变量，这样我们就不用重复调用`datetime.today()`。

类似地，在第二个例子中，我们声明了`theta`变量，然后重用它来计算`sin(theta)`和`cos(theta)`。在这种情况下，我们还将它与看起来像“reverse walrus”的操作符结合使用。这只是`=`，它强制表达式沿着它的值打印，加上用于格式化表达式的`:`。

还要注意，walrus 表达式必须用括号括起来，这样 f 字符串才能正确解释它。

# 任何和所有

您可以使用 Python 的`any()`和`all()`函数来验证某个 iterable 中的任意或所有值是否满足某些条件。但是，如果您还想捕获导致`any()`返回`True`的值(所谓的“见证”)或导致`all()`失败的值(所谓的“反例”)，该怎么办呢？

`any()`和`all()`都使用短路来计算表达式。这意味着他们一找到第一个“见证”或“反例”，就停止评估。所以，用这一招，walrus 操作符创建的变量总会给我们第一个“见证”/“反例”。

# 陷阱和限制

虽然我在前面几节中试图鼓励您使用 walrus 操作符，但我认为提醒您它的一些缺点和局限性也很重要。以下是您在使用 walrus 运算符时可能遇到的问题:

在前面的例子中，您看到了短路对于捕获`any()` / `all()`中的值可能是有用的，但是在某些情况下，它可能会产生意想不到的结果。这里有一个例子:

在上面的代码片段中，我们创建了一个条件，其中两个赋值由`and`连接。它们根据是否满足第一个、第二个或两个条件来检查一个数是否能被 2、3 或 6 整除。乍一看，这似乎是一个不错的技巧，但是由于短路，如果表达式`(two := i % 2 == 0)`失败，第二部分将被跳过，因此`three`将是未定义的或者将具有来自前一个循环的旧值。

不过，短路也可能是有益的/有意的。我们可以将它与正则表达式一起使用，在一个字符串中搜索多个模式，如下所示:

我们已经在第一部分中看到了这个片段的一个版本，其中我们将`if` / `elif`与 walrus operator 结合使用。在这里，我们通过将条件简化为单个`if`来进一步简化它。

如果您熟悉 walrus 操作符，您可能会注意到它会导致变量作用域在理解中表现不同。代码如下:

使用正常的 list/dict/set 理解，循环变量不会泄漏到周围的范围内，因此，任何同名的现有变量都不会被修改。

然而，使用 walrus 操作符，在 comprehension 返回之后，来自 comprehension 的变量(上面代码中的`total`)仍然是可访问的，从 comprehension 内部取值。

当您对在代码中使用 walrus 变得更加熟悉时，您可以尝试在更多的情况下使用它。有一个地方你绝对不应该使用它，那就是带有`with`语句的地方。下面是这个陷阱的代码:

当使用普通语法`with ContextManager() as context: ...`时，`context`被绑定到`context.__enter__()`的返回值，而如果使用带有`:=`的版本，那么它被绑定到`ContextManager()`本身的结果。这通常无关紧要，因为`context.__enter__()`通常会返回`self`，但如果不返回，就会产生难以调试的问题。

对于一个更实际的例子，看看当您将 walrus 操作符与下面的`closing`上下文管理器一起使用时会发生什么:

您可能遇到的另一个问题是`:=`的相对优先级，它低于逻辑运算符的优先级。代码如下:

这里我们看到我们需要用圆括号将赋值括起来，以确保将`re.match(...)`的结果赋值给变量。如果我们不这样做，那么将首先计算`and`表达式，然后分配一个布尔结果。

最后，这真的不是一个陷阱，而是一个轻微的限制。您目前不能对 walrus 运算符使用内联类型提示。因此，如果你想指定变量的类型，那么你需要把它分成两行，如下所示:

# 结束语

像其他语法特性一样，walrus 操作符可能会被滥用，降低清晰度和可读性。你不需要尽可能把它塞进你的代码里。把它当作一种工具——意识到它的优点和缺点，并在适当的时候使用它。

如果你想看更多关于 walrus 操作符的实用的、好的用法，看看它是如何被引入到 CPython 的标准库中的——所有这些变化都可以在 [this PR](https://github.com/python/cpython/pull/8122/files) 中找到。

除此之外，我还推荐通读 [PEP 572](https://peps.python.org/pep-0572/) ，其中有更多的例子和介绍操作者的基本原理。

[](https://towardsdatascience.com/python-f-strings-are-more-powerful-than-you-might-think-8271d3efbd7d) [## Python f-strings 比您想象的更强大

### 了解 Python 的 f 字符串的未知特性——格式化的字符串文字——以及您的文本格式化…

towardsdatascience.com](https://towardsdatascience.com/python-f-strings-are-more-powerful-than-you-might-think-8271d3efbd7d) [](/automate-all-the-boring-kubernetes-operations-with-python-7a31bbf7a387) [## 用 Python 自动化所有枯燥的 Kubernetes 操作

### 了解如何使用 Python 的 Kubernetes 客户端库来自动化所有枯燥的 Kubernetes 任务和操作

better 编程. pub](/automate-all-the-boring-kubernetes-operations-with-python-7a31bbf7a387) 

```
**Want to Connect?**This article was originally posted at [martinheinz.dev](http://martinheinz.dev/blog/79)
```
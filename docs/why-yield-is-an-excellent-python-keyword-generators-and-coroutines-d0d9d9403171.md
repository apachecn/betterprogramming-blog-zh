# 为什么“Yield”是一个优秀的 Python 关键字—生成器和协程

> 原文：<https://betterprogramming.pub/why-yield-is-an-excellent-python-keyword-generators-and-coroutines-d0d9d9403171>

## 屈服还是不屈服

![](img/fc272fb8c732315b29791fcbf0418fa2.png)

Kurt Cotoaga 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Python 的[没有其他几种主流编程语言的](https://github.com/leighmcculloch/keywords) ( *n=35* )那么多关键词，比如 Swift ( *n=89* )、Java ( *n=51* )、JavaScript ( *n=49* )。更短的关键字列表并没有阻止 Python 成为机器学习、web 开发和许多其他领域的流行语言。在 Python 的关键字中，`yield`是最有趣的一个——也是我个人非常喜欢的一个。

在英语中,“yield”这个词作为动词有两个基本意思:

1.  生产或提供。
2.  让路。

巧合的是(或者可能根本不是)，Python 中的关键字`yield`有与这两种含义相关的使用上下文。在本文中，我想讨论利用`yield`的特定 Python 特性。

# 在生成器函数中生成值

您第一次在 Python 中看到使用`yield`可能是在一个生成器函数中。大概是这样的:

一个简单的生成器函数

这个函数的调用是为了创建一个生成器。生成器是 Python 中一种特殊的迭代器。它们具有惰性评估的优势，即在迭代需求出现之前生成适用的值。下面的代码片段展示了一个简单的例子，它在一个`for`循环中利用这个生成器函数来执行迭代工作:

使用发电机的 For 循环

为了更好地说明`yield`的作用，让我们在原来的生成器函数中添加一些`print`函数调用:

您可能知道，当我们有一个生成器时，我们可以使用`next()`函数来检索生成器的下一项，这允许我们仔细检查`yield`的用法:

发电机的逐步运行

*   当我们通过调用`generator`函数获得生成器时，函数本身并不立即执行。相反，它只是创建了`generator`对象，而没有运行代码。这是发电机的初始状态。
*   当我们得到生成器的第一项时，函数开始执行。当执行遇到`yield`语句时，它产生该项(即字符串值)。
*   重要的是，`generator`函数在产生值后停止执行。当我们再次调用`next()`函数时，执行从停止的地方继续。在我们的例子中，您会注意到`After yielding…`被打印出来。
*   当生成器产生所有的项目(三个项目)并且我们第四次调用`next()`函数时，尽管`generator`函数继续从它停止的地方运行，它将引发`StopIteration`异常。值得注意的是，当我们在`for`循环中使用生成器时，我们看不到这一点，因为 Python 为我们处理了这一点。

# 在协程中暂停和恢复执行

*注意:重要的是要知道 Python 正在逐步淘汰使用生成器作为协程的基础。查看* [*官方文档*](https://docs.python.org/3/library/asyncio-task.html#generator-based-coroutines) *。但是，如果我们知道* `*yield*` *有“让路”的意思，那么协程的概念就很容易理解了。*

在这个用法中，它适用于`yield`的第二个意思:让路。这很像现实生活中的交通“让行”标志。你暂时停下你的车，让其他有先行权的车辆先行。将这种模拟应用于编程，这是[协程](https://en.wikipedia.org/wiki/Coroutine)的基本思想。

正如您所看到的，协程的关键是执行被暂时挂起，以后在适用的时候可以继续执行。你不觉得这听起来很熟悉吗？`generator`函数运行并产生元素(暂停执行)，当请求下一个元素时，它从暂停的地方继续运行(恢复执行)。

实际上，我们可以使用`yield`关键字来实现一个协程。让我们看看下面的代码片段，这是一个简单的例子:

*   我们首先使用关键字`yield`定义一个协程函数。可以看到，整体结构类似于`generator`函数。然而，你会注意到`yield`关键字出现在赋值操作符(即等号)之后。
*   这种分配是可能的，因为除了与生成器一起工作的典型的`next()`功能之外，生成器还支持`send()`功能。
*   在示例中，我们将一个字符串数据(即`“Here’s the data that your requested.”`)发送回协程。具体来说，我们发送的数据被设置为变量`x`，我们能够按预期打印出`x`的值。
*   `StopIteration`异常是意料之中的，因为`generator`函数已经到达末尾，这与生成器的行为一致(如上一节所示)。

除了`send()`函数，还有几个与 Python 生成器相关的显著特性。请查看[这篇关于发电机高级特性的文章](https://medium.com/better-programming/python-7-advanced-features-that-you-may-not-know-about-generators-574a65fd6e45)。

# 在你走之前

在本文中，我们回顾了在 Python 中使用`yield`关键字的两个重要含义——生成器和协程。这里有一个快速回顾:

*   生成器是延迟求值的迭代器。使用`yield`关键字主要意味着`generator`函数可以根据需要产生元素。
*   协程是一种特殊的编程技术，在异步编程(如 HTTP 请求)中特别有用。所示的例子只是为了让您了解协程在概念上是如何工作的。本质上，关键词`yield`主要意思是“让路”。执行被暂时挂起，这允许其他任务运行(例如 HTTP 请求)。当我们取回数据时，执行可以继续，数据被发送回协程(例如，来自请求的数据)。
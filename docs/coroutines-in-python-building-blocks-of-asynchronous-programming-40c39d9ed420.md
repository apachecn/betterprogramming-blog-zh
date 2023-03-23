# Python 中的协同程序

> 原文：<https://betterprogramming.pub/coroutines-in-python-building-blocks-of-asynchronous-programming-40c39d9ed420>

## 异步编程的构造块

![](img/06cef4479dd81f8ad8ff1b42d101bba4.png)

照片由[瓦列里·费多托夫](https://unsplash.com/@imlst?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

*先决条件:你应该了解迭代器，以及 Python 中的 for 循环是如何在幕后工作的。你不需要知道任何关于生成器、协程或者* `*yield*` *关键字的知识。*

只是快速概述一下 Python 中 for 循环的工作原理:

```
OUTPUT :1
2
3
4
5
```

上面的代码等效于下面给出的代码:

```
OUTPUT :1
2
3
4
5
```

# 什么是协程？

协程基本上是一些函数，它们的执行可以在某个特定的点暂停/中止，然后我们可以随时从那个点继续执行。

我们需要一种机制——或者更准确地说，一个关键字——通过它我们可以插入一个检查点，并告诉程序我们希望在这里暂停函数的执行，并将控制返回到调用它的地方。我们随时都可以恢复死刑。

在 Python 中，我们可以使用`yield`关键字暂停函数的执行。

事情变得有趣起来:

*   我们可以把协程看作一个函数，它有一个或多个检查点，在这些检查点上，执行将被暂停，控制权将返回到调用它的地方。
*   本质上，协程是一个分成许多部分的函数，当我们使用`next`函数执行 for 循环的每次迭代时，我们可以执行协程的每个部分。

这里有一个基本的例子:

```
OUTPUT :<class 'generator'>
Function Starts
Function Ends
```

从输出中，我们注意到一些事情:

*   首先，我们需要调用协程/函数，它将为我们提供一个生成器对象。
*   生成器对象的行为类似于迭代器，但是在迭代器的情况下，我们遍历的是可迭代对象。有了生成器，我们正在执行协程的一部分。
*   正如在 for 循环的幕后抛出并捕获一个`StopIteration`异常一样，在这种情况下，当执行协程的最后一部分时，也会发生同样的情况。

现在，函数在中间的暂停非常有趣，并带来了一些可能性:

*   当函数暂停时，我们什么也不做，这就是我们刚才看到的情况。
*   假设一个变量在一个函数中被修改了几次，我们想要这个特定变量在某个检查点的值。然后当我们在那个特定的检查点暂停那个函数时，它返回那个变量的值。

让我们看一个例子:

```
OUTPUT :Function Part 1
5
Function part 2
12
Function part 3
```

这里，`x`的值由`yield`在不同的检查点返回，因为函数执行已经暂停。

每当我们执行函数的最后一部分，并且函数中没有剩余产出时，在执行完最后一部分后，将引发一个`StopIteration`异常。

就像迭代器试图执行下一个函数，但是 iterable 中没有元素了，它也会引发`StopIteration`异常。

*   假设我们想在某个检查点(即函数的某个状态)发送一个值(可以是常量也可以是变量)。我们也可以使用`yield`关键字来实现。当我们想要发送一个值时，我们将使用`send`函数而不是`next`。

让我们看一个例子:

```
OUTPUT :Function part 1
6
Function part 2
12
Function part 3
```

我们在使用`send`之前使用`next`的原因是我们只能在`yield`检查点使用`send`，而`yield`在表达式的右边。所以为了到达第一个`yield`，我们必须使用`next`函数。

现在这里有一个有趣的协程应用。假设我们想在两个函数之间来回切换，就像我们在多线程中做的那样。在多线程中，直到操作系统遇到一个`interrupt`，它将继续执行。在这种情况下，我们可以随时切换。

让我们看一个例子:

```
OUTPUT :Function 1 part 1
Function 2 part 1
Function 1 part 2
Function 1 part 3
Function 2 part 2
Function 2 part 3
Function 2 part 4
Function 1 part 4
Function 1 part 5
```

在这个例子中，我们可以看到，只要我们愿意，我们可以在协程之间来回切换。

因此，如果我们编写自己的定制调度程序来处理多个协程之间的切换，我们就可以用单线程实现我们用多线程实现的功能。

协程有许多应用，例如并发，也可以实现其他编程模式，如网络编程中的生产者-消费者或发送者-接收者。我将在接下来的文章中探讨这些问题。

协程也是 asyncio、twisted、aiohttp 等许多框架的构建模块。也可以把它们链在一起做管道，解决问题。
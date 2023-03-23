# JavaScript 中关于 Async/Await 的一个常见误解

> 原文：<https://betterprogramming.pub/a-common-misconception-about-async-await-in-javascript-33de224bd5f>

## 我们应得的英雄

![](img/9370cb278f963fc34379ab2a37a28754.png)

加布里埃尔·古斯芒在 [Unsplash](https://unsplash.com/s/photos/sync?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

使用 promises，我们编写模拟同步代码的异步代码，但是使用`async` / `await`，我们编写的异步代码*看起来像*同步代码。因此，这往往会导致误解。

我决定写这篇文章，因为自从引入了 [async/await，](https://hackernoon.com/6-reasons-why-javascripts-async-await-blows-promises-away-tutorial-c7ec10518dd9)以来，我注意到许多开发人员在理解什么是顺序执行时遇到了更多的困难(因为它总是异步执行的)。这些困难已经存在，但是`async` / `await`通过接近同步编程风格强调了这一点。

让我们看一个简单的例子，在异步操作之前和之后写一条消息。我们将使用`async` / `await`和`promise`来实现它:

一些开发人员倾向于认为用`async` / `await`编写的函数将总是被同步执行，因为如果我们在继续执行之前同步等待延迟的操作，代码看起来就像。

所以让我们用这段代码运行它，并与`promise`版本进行比较:

这是输出:

```
(1) with await
(1) before await
(1) with promise
(1) before promise
(1) after all
(1) after await
(1) after promise
```

正如你所看到的，函数代码是连续运行的，直到第一次遇到`await`。与承诺一样，由调用者决定代码是否按顺序执行。

让我们看看更同步的版本:

这是输出结果:

```
(2) with await
(2) before await
(2) after await
(2) with promise
(2) before promise
(2) after promise
(2) after all
```

如果调用者不使用`await`(或`then` with promises)，那么你的代码就不会按顺序运行。这就是为什么`async`/`await`/[在使用](https://blog.lavrton.com/javascript-loops-how-to-handle-async-await-6252dd3c795) `forEach`这样的结构时不能像预期的那样工作的原因。
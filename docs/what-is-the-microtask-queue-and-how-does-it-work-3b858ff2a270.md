# 什么是微任务队列，它是如何工作的？

> 原文：<https://betterprogramming.pub/what-is-the-microtask-queue-and-how-does-it-work-3b858ff2a270>

## 用一个 JavaScript 用例例子解释

![](img/65276d2dd7122d05a33d31a044c73591.png)

Adobe Stock 照片

几周前，我的一个同事向我寻求帮助。他们试图给异步函数增加一个人工等待时间。

代码看起来像这样。

预期的行为是:`task1`运行，然后 5 秒后`task2`，最后`task3`。

他们看到的实际行为是`task1`跑，然后是`task3`，最后是`task2`。我的同事想知道为什么函数没有等待`setTimeout`结束就继续。

`task2`在`task3`之后运行的原因是微任务队列。

JavaScript 引擎有一个等待任务的事件循环。当任务进来时，它们按照到达的顺序执行。在事件循环内部，有一个称为微任务队列的子队列。

微任务队列阻止事件循环的其余部分运行，直到所有微任务都已完成。承诺进入微任务队列。这就是为什么应用程序在进入下一个任务之前“等待”承诺的解决。

在我同事的例子中，当函数运行时，有三个任务被添加到事件循环中。`Task1`、`setTimeout`和`task3`。`setTimout`不返回承诺，因此它被添加到常规任务队列中。

事件循环示例:

```
Tasks     | setTimeout
Microtasks| task1, task3
```

Task1 将首先运行，然后 task3 将作为微任务队列中的下一个运行。一旦微任务队列为空，常规任务将运行。

因此`setTimeout`功能将在 5 秒内完成。一旦`setTimeout`完成，`task2`将被添加到事件循环中。`Task2`被添加到 Micortasks 队列中，因为它返回一个`Promise`。这就解释了为什么我的同事经历了`task1`然后`task3`然后`task2`。

为了实现预期的等待行为，`setTimeout`函数需要包装在`Promise`中。一旦它在一个承诺中，它将加入微任务队列中的`task1`和`task3`。

现在，`task3`将在新的`Promise`解决后运行。

新的`Promise`将在`task2`完成且解析功能运行后解析。
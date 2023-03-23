# 曹兰 Asyncjs 与 Async/Await:node . js 中的异步操作使用什么？

> 原文：<https://betterprogramming.pub/caolan-asyncjs-vs-async-await-what-to-use-for-async-operations-in-node-js-90307c73f12b>

## 异步/等待足够了吗？

![](img/7400c0b7ae9d9f9138dbe80aaea34063.png)

照片由[费伦茨·阿尔马西](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=medium)在 [Unsplash](https://unsplash.com/s/photos/async-%2F-await?utm_source=medium&utm_medium=medium) 拍摄

在使用 JavaScript 的过程中，我们都曾在 web 开发旅程的某个时刻遇到过异步操作。在 javaScript/nodeJS 中有多种方法可以处理异步操作，可以使用回调、承诺或 async/await。这为开发人员在代码方面提供了极大的灵活性，这也是为什么今天您仍然可以在现实世界的项目中找到不同的方法。

如果处理不好，异步操作可能会以最微妙的方式被证明是有害的。我们都知道回调地狱，对不对？

在本文中，我们将看看曹兰的`asyncjs`库，它如何提供一种易于阅读的方式来处理 nodeJS 中的异步操作，以及通常的控制流是否仍然需要它。

# javascript 中的异步操作

nodeJS 中的异步操作是不能立即返回结果的操作。例如，它可以是网络调用或数据库操作。

因为停止执行等待异步操作完成是没有意义的，所以回调和承诺来解决这个问题。

使用回调/承诺，我们告诉事件循环当异步操作的结果到达时做什么。

回调/承诺被推到事件循环中，并在下一次迭代中被重新访问。如果异步操作在事件循环的下一次迭代前没有解决，则重复此过程。

下面是一个使用异步操作的基于回调的方法示例:

是的，代码看起来不干净，功劳归于回调。如果你想了解更多关于回调和回调地狱，有一个完整的网站致力于此。点击查看[。](http://callbackhell.com/)

随着`asyncjs`库的出现，这种情况得到了极大的改善。让我们看看`asyncjs`库是如何提高可读性的。

# 用 asyncjs 处理异步流

该库提供了一种简单的方法来处理 NodeJS 中的异步函数。除了一个很好的数组和对象的函数集合之外，这个库还提供了各种控制流，让开发人员的工作变得简单。

`Asyncjs`库也提供了对承诺和异步/等待的支持，但我将展示使用回调的例子。

## 异步系列

这个流允许您放置任意多的处理程序，它们将一个接一个地连续运行。一个的输出不依赖于前一个处理程序(不像`[async.waterfall](https://caolan.github.io/async/v3/docs.html#waterfall)`)。

在上面的例子中，两个异步函数串行运行，最后的回调包含一个数组，该数组包含这些函数的返回值。

如果任何函数中有错误，将不会执行进一步的处理程序，控件将直接跳转到最后一个抛出错误的回调函数。

> **注意:**输出数组将按照函数编写的顺序包含结果，而不考虑它们的执行时间。

## 异步并行

当处理程序完全不相互依赖时，这个控制流就很方便了。你可以同时触发它们。

并行执行意味着启动 I/O 任务(如果有的话),如果您的函数不执行任何 I/O，那么这些函数将以同步方式串行运行。

Javascript 将*仍然*连续处理同步代码块。

同样，任何处理程序中的错误都会导致跳过所有剩余处理程序的执行。

## 异步竞赛

这与`Promise.race`完全相似，最终回调的结果将来自首先调用回调的函数。

# 使用异步/等待

我们在上一节中看到的控制流可以使用 async/await 复制，而不需要 asyncjs 库。让我们使用 async/await 重新创建这些示例:

## 异步系列

假设上面的代码块在一个异步函数中，我们很容易在这里复制了`async.series`功能。

1.  我们确保在`asyncFnThatReturnsTwo`运行之前`asyncFnThatReturnsOne`首先解析并返回结果。
2.  最终结果数组与之前完全相同，即['一'，'二']。`asyncFnThatReturnsOne`是否比`asyncFnThatReturnsTwo`花费更长时间并不重要。
3.  我们使用 try-catch 块来捕捉错误。

## 异步并行

我们并行启动两个异步函数，并将它们包装在 [Promise.all](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) 中。我们正在等待，瞧，我们得到了同样的结果！

## 异步竞赛

类似地，我们可以使用承诺来重新创建一个竞赛场景，而不需要`asyncjs`库:

然而，图书馆提供了一些好处，使它值得。有一点要记住，你可以自己定制解决方案，从头开始重新创建一切。但是，当已经有了一个完全符合你需要的库时，重新发明轮子通常不是一个好主意。

# 您可能仍然需要 asyncjs

我们已经看到了几个安装 asyncjs 库没有多大意义的场景。但是在其他用例中，asyncjs 可以证明是有价值的，并且可以让您不用编写自己的定制解决方案。

## 异步队列

这个队列实用程序帮助您编写一个工人函数，并提供一组由工人函数处理的任务。任务并行运行的最大限制称为并发限制。一旦运行的并发工作线程少于并发限制，任务就会被拾取。

随意调整`concurrent_workers`的数字，看看它是如何影响正在处理的异步操作的。操场链接在[这里](https://stackblitz.com/edit/caolan-async-queue-demo?devtoolsheight=33&file=index.js)可用。

这对于确保您不会试图并行运行超过 CPU/磁盘能力的任务非常有用。请记住，并行方面仅适用于 I/O 和定时器。如果您的所有任务都有 I/O，并且您正在并行运行无限数量的任务，那么您的服务器将会因为高磁盘 I/O 使用率和资源匮乏而崩溃。

`async.queue`提供了一个很好的节流应用用例，因为它能够设置并行执行数量的最大上限。

> 看看`[async.priorityQueue](https://caolan.github.io/async/v3/docs.html#priorityQueue)`，它类似于`async.queue`，但是提供了优先级机制来确保高优先级任务不会因为低优先级任务而被饿死。

## 异步重试

有时，请求失败可能不是因为我们的应用程序的错误(例如，网络连接问题)。您可以使用`async.retry`发出相同的请求 X 次，直到收到成功的响应。例如，尝试相同的请求 3 次，但失败了，这给了我们对服务行为的判断的确定性。

在上面的例子中，我们以 100 毫秒的间隔触发了`someAPIMethod` 5 次。如果任何方法成功，回调将立即被成功的`result`调用。如果没有方法成功，回调将被错误调用。

在`asyncjs`中还有其他的控制流可以派上用场，你可以在这里查看它们[。](https://caolan.github.io/async/v3/docs.html#controlflow)

# 结论

这是对`asyncjs`库、它提供的一些控制流以及我们如何使用 async/await 复制相同的流的一个简短概述。我们还看了几个案例，在这些案例中使用`asyncjs`确实很有帮助，可以让你避免重复劳动。

我希望它能让您对这个库的好处有所了解，并让您知道在使用第三方解决方案之前，我们应该如何理解我们的具体用例([有时一次提交就足够了](https://github.com/Marak/colors.js/commit/074a0f8ed0c31c35d13d28632bd8a049ff136fb6#diff-92bbac9a308cd5fcf9db165841f2d90ce981baddcb2b1e26cfff170929af3bd1R18))。

这篇文章最初发表在[rrawat.com](https://rrawat.com/blog/asyncjs-in-nodejs)上。

# 接下来呢？

asyncjs 的[文档非常简单易懂。由于我们在本文中只看到了几个用例，我建议浏览一下`asyncjs`文档，并查看一下库的其他可能性。你也可以尝试使用`async/await`来复制同样的东西，以巩固你对这个库仍然有意义的地方的理解。](https://caolan.github.io/async/)

```
**Want more such articles?**You can join my newsletter [here](https://buttondown.email/rishabh570).I write about my learnings and experiences related to web development technologies biweekly.
```
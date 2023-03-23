# XCTestCase 中的异步安装和拆卸

> 原文：<https://betterprogramming.pub/async-setup-and-teardown-in-xctestcase-dc7a2cdb9fb>

## Swift 中 iOS 开发如何在 XCTestCase 中使 setUpWithError()和 tearDownWithError()异步

![](img/a9b8f20e6c3a31e802ee1ad7f56cc584.png)

[谢苗·博里索夫](https://unsplash.com/@devsnice?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/wait?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

如果我们需要异步的话，`XCTestCase`允许我们简单地用`async`标记我们的测试函数。不幸的是，`setUpWithError`和`tearDownWithError`却不是这样。标记这些异步将导致以下构建错误:

```
Method does not override any method from its superclass.
```

谢天谢地，这个问题有一个相当简单的解决方案。只需将下面的代码复制到一个新文件中，并子类化`XCAsyncTestCase`而不是`XCTestCase`。

此外，确保覆盖`asyncSetUpWithError`和`asyncTearDownWithError`，而不是标准的`setUpWithError`和`tearDownWithError`。关于这是如何工作的解释，请继续阅读下面的内容。

这个解决方案的关键是`wait`函数，它接受一个异步代码块。这个异步块在一个新任务中执行，并使用一个[信号量](https://en.wikipedia.org/wiki/Semaphore_(programming))来阻止继续执行，直到异步块完成。该函数将使用`Task.init`触发一个新的异步任务，并在任务完成时向信号量发出信号。

同时，执行将简单地停止在线`semaphore.wait()`上，直到信号量被发出信号。然后，我们在典型的 setup 和 teardown 函数中使用这个`wait`函数，将调用传递给我们的异步版本，并等待它们完成。
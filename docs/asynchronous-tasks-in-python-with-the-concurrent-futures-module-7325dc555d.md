# Python 中的异步任务。期货模块

> 原文：<https://betterprogramming.pub/asynchronous-tasks-in-python-with-the-concurrent-futures-module-7325dc555d>

## Python 中的多线程和并行编程比您想象的要简单

![](img/b1422a6af5462c7183587b8bff2a85f6.png)

伊恩·贝克利在[像素](https://www.pexels.com/)上拍摄的照片。

使用`[concurrent.futures](https://docs.python.org/3/library/concurrent.futures.html)`模块，用 Python 进行多线程和并行编程很容易。

在试图解决 AWS lambda 函数在一些长时间的 I/O 操作完成之前超时的问题时，我使用一个`ThreadPoolExecutor`重写了 Python 代码。它立即修复了问题。

让我们看看如何使用`concurrent.futures`模块来启动并行任务。

# Python 中的进程与线程

`concurrent.futures`模块为您提供了使用进程或线程的不同实现。

*   多进程:使用`ProcessPoolExecutor`的任务产生多个进程(每个进程都有自己的 Python 解释器)，通过这样做，它们绕过了 Python 的全局解释器锁。最适合 CPU 密集型任务。
*   多线程:使用`ThreadPoolExecutor`的任务在同一个进程中使用多个线程。线程共享相同的解释器和内存空间。最适合 I/O 繁重的任务。

# **多线程示例**

想象一个 I/O 密集型任务，比如从 rest API 获取数据。下面的例子正好说明了这一点。让我们使用法国政府的 [geo API](https://geo.api.gouv.fr/adresse) 获取法国一组地址的详细信息:

这个函数将负责获取数据。对于如何获得多个地址，我们有不同的方法。

## **选项 1:顺序获取地址(剧透:这真的效率很低)**

这段愚蠢的代码运行了 0.267 秒。它获取了所有的地址数据，但我们可以做得更好。

## **选项 2:使用多线程并行处理请求**

为了做到这一点，我们将使用 Python 的`concurrent.futures`模块中的`ThreadPoolExecutor`,这个模块从 Python 3.2 开始就可用了。它提供了异步执行任务的高级接口。

当在我们的执行器中调用`map`函数时，我们正在为可迭代的`request_data`中的所有对象执行`get_french_addresses`任务，并等待所有线程的结果。

我们在 0.177 秒内获得所有 API 调用的响应，这比使用顺序版本要好。这是如何使用多线程的一个例子。

# **多重处理示例**

现在是多重处理。这利用了不同的 CPU 内核来启动不同的 Python 解释器并并行化任务。这就是为什么它们应该被用来加速 CPU 繁重的任务，如数字运算。

我们以这个函数为例，它取最小值和最大值，并对这些值之间的所有质数求和:

我们需要为一组五对鞋运行这个函数。让我们看看我们如何能做它。

## **方案一:依次计算每个数字(剧透:这样真的效率很低)**

在这里，我们称`find_prime_numbers_sum`为五对。它按顺序处理这些数字，总共花费了 25.73 秒。让我们看看是否可以通过并行化来改进它。

## **选项 2:使用多处理并行化**

在第二个版本中，我们创建了一个最多有四个工作线程的进程池。`run_heavy_function`是一个助手，允许我们用多个参数调用`find_prime_numbers_sum`。运行这个版本后，计算所有素数需要 15.529s。与以前的方法相比，这是一个很好的改进。

# 摘要

*   当您想要并行处理 CPU 密集型任务时，请使用多进程，因为这可以利用多个 CPU 和内核。
*   当您需要并行处理 I/O 绑定的任务或创建响应性 ui 时，请使用多线程。请记住，线程不像进程那样是可中断/可终止的。
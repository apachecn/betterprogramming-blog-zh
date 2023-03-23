# 并发与串行调度队列:Swift 中的并发性解释

> 原文：<https://betterprogramming.pub/concurrent-vs-serial-dispatch-queue-concurrency-in-swift-explained-14c4fa6f2478>

## 了解 Swift 中线程和并发的更多信息

并发和串行队列有助于我们管理执行任务的方式，并有助于使我们的应用程序运行得更快、更有效，并提高响应能力。我们可以使用`DispatchQueue`类轻松地创建队列，该类构建在 Grand Central Dispatch (GCD)队列之上。

分派队列的好处是，与较低级别的 GCD 线程代码相比，它们更容易理解，并且在不同线程上执行任务时更高效。尽管如此，还有很多东西需要学习，所以让我们深入了解并发队列和串行队列之间的区别。

# 什么是调度队列？

`DispatchQueue`是 GCD 队列之上的一个抽象层，它允许您在应用程序中异步和并发地执行任务。任务总是按照它们被添加到队列的顺序执行。

# 什么是串行队列？

串行调度队列一次只执行一项任务。串行队列通常用于同步对特定值或资源的访问，以防止发生数据竞争。

## 创建串行调度队列

一个`DispatchQueue`默认为一个串行队列，可以初始化如下:

```
let serialQueue = DispatchQueue(label: "swiftlee.serial.queue")

serialQueue.async {
    print("Task 1 started")
    // Do some work..
    print("Task 1 finished")
}
serialQueue.async {
    print("Task 2 started")
    // Do some work..
    print("Task 2 finished")
}

/*
Serial Queue prints:
Task 1 started
Task 1 finished
Task 2 started
Task 2 finished
*/
```

如您所见，第二个任务仅在第一个任务完成后开始。

# 什么是并发队列？

并发队列允许我们同时执行多个任务。任务总是按照它们被添加的顺序开始，但是它们可以按照不同的顺序完成，因为它们可以并行执行。任务将在由调度队列管理的不同线程上运行。同时运行的任务数量是可变的，取决于系统条件。

## 创建并发调度队列

并发调度队列可以通过将一个属性作为参数传递给`DispatchQueue`初始化器来创建:

```
let concurrentQueue = DispatchQueue(label: "swiftlee.concurrent.queue", attributes: .concurrent)

concurrentQueue.async {
    print("Task 1 started")
    // Do some work..
    print("Task 1 finished")
}
concurrentQueue.async {
    print("Task 2 started")
    // Do some work..
    print("Task 2 finished")
}

/*
Concurrent Queue prints:
Task 1 started
Task 2 started
Task 1 finished
Task 2 finished
*/
```

如您所见，在第一个任务完成之前，第二个任务已经开始了。这意味着两个任务并行运行。

# 两全其美

在某些情况下，利用并发队列来同时执行多个任务，同时防止数据竞争是很有价值的。这可以通过利用所谓的屏障来实现。在我们开始之前，最好了解一下数据竞争到底是什么。

## 什么是数据竞赛？

当多个线程不同步地访问同一个内存，并且至少有一次访问是写操作时，就会发生数据争用。您可能正在从主线程读取数组中的值，而后台线程正在向同一个数组中添加新值。

数据竞争可能是古怪的测试和奇怪的崩溃背后的根本原因。因此，定期花时间使用[螺纹消毒剂](https://twitter.com/twannl/status/1192781427978493952?s=20)是个好习惯。

## 在并发队列上使用屏障来同步写入

屏障标志可用于使对某个资源或值的访问是线程安全的。我们同步写入访问，同时保持并发读取的优势。

下面的代码演示了一个可以同时从多个线程访问的 messenger 类。向阵列添加新消息是使用屏障标志和块以及新读取来完成的，直到写入完成。

```
final class Messenger {

    private var messages: [String] = []

    private var queue = DispatchQueue(label: "messages.queue", attributes: .concurrent)

    var lastMessage: String? {
        return queue.sync {
            messages.last
        }
    }

    func postMessage(_ newMessage: String) {
        queue.sync(flags: .barrier) {
            messages.append(newMessage)
        }
    }
}

let messenger = Messenger()
// Executed on Thread #1
messenger.postMessage("Hello SwiftLee!")
// Executed on Thread #2
print(messenger.lastMessage) // Prints: Hello SwiftLee!
```

您可以将一个障碍看作是一个妨碍并行任务的任务，并且在一瞬间，使一个并发队列成为一个串行队列。带有障碍的任务被延迟执行，直到所有先前提交的任务执行完毕。最后一个任务完成后，队列执行屏障块，然后恢复正常的执行行为。

## 异步与同步任务

一个`DispatchQueue`任务可以同步或异步运行。主要区别发生在创建任务时。

*   同步启动任务将阻塞调用线程，直到任务完成
*   异步启动任务将直接在调用线程上返回，而不会阻塞

假设您将从主线程向队列添加一个任务。您希望防止自己对长时间运行的任务使用同步方法。这将阻塞主线程，使你的用户界面没有反应。

## 主线怎么样？

主调度队列是一个全局可用的串行队列，在应用程序的主线程上执行任务。因为主线程用于 UI 更新，所以在这个队列上执行任务时要注意。因此，使用前面描述的 dispatch APIs 在不同的线程上执行任务是有价值的。

您可以开始在后台队列上执行繁重的工作，并在完成后将其分派回主队列。

```
let concurrentQueue = DispatchQueue(label: "swiftlee.concurrent.queue", attributes: .concurrent)

concurrentQueue.async {
    // Perform the data request and JSON decoding on the background queue.
    fetchData()

    DispatchQueue.main.async {
        /// Access and reload the UI back on the main queue.
        tableView.reloadData()
    }
}
```

# 避免过多的线程创建

读完这篇文章后，你可能会想创建许多队列来提高应用程序的性能。不幸的是，创建线程是有代价的，因此您应该避免创建过多的线程。

在两种常见的情况下，会创建过多的线程:

*   并发队列中添加了太多的阻塞任务，迫使系统创建额外的线程，直到系统用完应用程序的线程。
*   存在太多私有并发调度队列，它们都消耗线程资源。

## 如何防止过多的线程创建？

最佳实践是利用全局并发调度队列。这可以防止您创建太多的私有并发队列。除此之外，您还应该注意执行长阻塞任务。

您可以使用全局并发队列，如下所示:

```
DispatchQueue.global().async {
    /// Concurrently execute a task using the global concurrent queue. Also known as the background queue.
}
```

这个全局并发队列也被称为后台队列，用在`DispatchQueue.main`的旁边。

# 结论

就是这样，深入了解 Swift 中的调度队列。还有更多的内容需要介绍，但这里只介绍了基本的内容。请务必查看 Thread Sanitizer，看看您的应用程序在哪些地方可以针对数据竞争进行改进。

谢谢！
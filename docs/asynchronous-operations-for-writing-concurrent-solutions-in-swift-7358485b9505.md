# 在 Swift 中编写并发解决方案的异步操作

> 原文：<https://betterprogramming.pub/asynchronous-operations-for-writing-concurrent-solutions-in-swift-7358485b9505>

## 在隔离的类中编写长期运行的任务，以分离关注点和易于测试的代码

![](img/e7ff955a5c90fe8e44f234315635926c.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/solution?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

异步操作允许执行长时间运行的任务，而不必在执行完成之前阻塞调用线程。这是创建关注点分离的一个很好的方法，特别是结合在操作之间创建依赖关系。

如果您是运营新手，我建议您首先阅读我的博客文章[*Swift*](https://www.avanderlee.com/swift/operations/)中的运营和运营队列入门。这篇文章将带你开始并解释基础知识。

让我们通过首先查看异步操作和它们的同步对立面之间的差异来深入了解异步操作。

# 异步与同步操作

看似很小的差别，其实只是个 A，实际差别要大得多。

同步操作更容易设置和使用，但是在不阻塞调用线程的情况下，不能像异步操作那样长时间运行。

异步操作可以充分利用 Swift 中的操作。由于可以运行异步的、长时间运行的任务，因此可以将它们用于任何类型的任务。

这为更多的关注点分离打开了大门，或者甚至为使用操作作为应用基础背后的核心逻辑打开了大门。

总而言之，异步操作允许您:

*   运行长期运行的任务。
*   从操作中分派到另一个队列。
*   手动启动操作，没有风险。

在下一段中，我会对最后一点做更多的解释。

## 手动启动操作

同步和异步操作都可以手动启动。手动启动基本上就是手动调用`start()`方法，而不是使用`OperationQueue`来管理执行。

同步操作总是阻塞调用线程，直到操作完成。

因此，它们不太适合手动启动操作。当使用异步任务时，阻塞调用线程的风险不像它可能被分派到另一个线程那样大。

## 不鼓励手动启动

尽管现在手动启动异步任务可能很诱人，但不建议这样做。

通过使用`OperationQueue`，您不必考虑多个操作的执行顺序，并且您可以受益于更多的特性，比如对任务进行优先级排序。

因此，建议总是通过将操作添加到`OperationQueue`来开始操作。

# 创建异步操作

创建异步操作都是从创建自己的子类并覆盖`isAsynchronous`属性开始的。

```
class AsyncOperation: Operation {
    override var isAsynchronous: Bool {
        return true
    } override func main() {
        /// Use a dispatch after to mimic the scenario of a long-running task.
        DispatchQueue.global().asyncAfter(deadline: DispatchTime.now() + DispatchTimeInterval.seconds(1), execute: {
            print("Executing")
        })
    }
}
```

这还不足以使任务异步，因为在执行 print 语句之后，任务仍然直接进入完成状态。这可以通过执行以下代码来演示:

```
let operation = AsyncOperation()
queue.addOperations([operation], waitUntilFinished: true)
print("Operations finished")// Prints:
// Operations finished
// Executing
```

换句话说，当异步任务仍在执行时，任务已经被标记为完成。这可能会导致意外的行为。我们需要自己开始管理状态，以便操作异步工作。

# 管理异步操作的状态

为了正确管理状态，我们需要用多线程和 KVO 支持覆盖`isFinished`和`isExecuting`属性。对于`isExecuting`属性，如下所示:

```
private var _isExecuting: Bool = false
override private(set) var isExecuting: Bool {
    get {
        return lockQueue.sync { () -> Bool in
            return _isExecuting
        }
    }
    set {
        willChangeValue(forKey: "isExecuting")
        lockQueue.sync(flags: [.barrier]) {
            _isExecuting = newValue
        }
        didChangeValue(forKey: "isExecuting")
    }
}
```

我们跟踪私有属性中的执行状态，我们只同步访问私有属性。

正如您在博文[*Swift 中的并发*](https://www.avanderlee.com/swift/concurrent-serial-dispatchqueue/) *，*中所了解的，我们需要利用锁队列来实现线程安全的读写访问。

我们利用`willChangeValue(forKey:)`和`didChangeValue(forKey:)`来添加 KVO 支持，这将确保`OperationQueue`得到正确更新。

我们还需要覆盖实际更新状态的`start()`方法。重要的是要注意，你永远不要在这个方法中调用`super.start()`，因为我们现在正在自己处理状态。

最后，我们添加了一个`finish()`方法，允许我们在异步任务完成后将状态设置为 finished。

将所有这些加在一起，我们得到一个子类，如下所示:

```
class AsyncOperation: Operation {
    private let lockQueue = DispatchQueue(label: "com.swiftlee.asyncoperation", attributes: .concurrent)

    override var isAsynchronous: Bool {
        return true
    }

    private var _isExecuting: Bool = false
    override private(set) var isExecuting: Bool {
        get {
            return lockQueue.sync { () -> Bool in
                return _isExecuting
            }
        }
        set {
            willChangeValue(forKey: "isExecuting")
            lockQueue.sync(flags: [.barrier]) {
                _isExecuting = newValue
            }
            didChangeValue(forKey: "isExecuting")
        }
    }

    private var _isFinished: Bool = false
    override private(set) var isFinished: Bool {
        get {
            return lockQueue.sync { () -> Bool in
                return _isFinished
            }
        }
        set {
            willChangeValue(forKey: "isFinished")
            lockQueue.sync(flags: [.barrier]) {
                _isFinished = newValue
            }
            didChangeValue(forKey: "isFinished")
        }
    }

    override func start() {
        print("Starting")
        isFinished = false
        isExecuting = true
        main()
    }

    override func main() {
        /// Use a dispatch after to mimic the scenario of a long-running task.
        DispatchQueue.global().asyncAfter(deadline: DispatchTime.now() + DispatchTimeInterval.seconds(1), execute: {
            print("Executing")
            self.finish()
        })
    }

    func finish() {
        isExecuting = false
        isFinished = true
    }
}
```

为了确保我们的任务能够正常工作，我们将执行与之前相同的代码:

```
let operation = AsyncOperation()
queue.addOperations([operation], waitUntilFinished: true)
print("Operations finished")

// Prints:
// Starting
// Executing
// Operations finished
```

这太棒了，这正是我们想要的！唯一缺少的是取消。

# 添加对取消的支持

由于操作可以在任何时候被取消，我们需要在开始执行时考虑到这一点。有可能在任务开始之前就已经取消了某项操作。

我们可以通过简单地在`start()`方法中添加一个保护来做到这一点:

```
override func start() {
    print("Starting")
    guard !isCancelled else { return }

    isFinished = false
    isExecuting = true
    main()
}
```

尽管此时`isFinished`和`isExecuting`属性被设置为正确的值，我们仍然需要根据[文档](https://developer.apple.com/documentation/foundation/nsoperation?language=swift#1661262)更新它们:

> “具体来说，你必须把`finished`返回的值改成`YES`，把`executing`返回的值改成`NO`。即使操作在开始执行前被取消，您也必须进行这些更改。

因此，我们从防护中的`start()`方法调用`finish()`方法，使我们的最终方法如下所示:

```
override func start() {
    print("Starting")
    guard !isCancelled else {
        finish()
        return
    }

    isFinished = false
    isExecuting = true
    main()
}
```

# 利用异步任务

创建了可用于长时间运行任务的子类后，就该从中受益了。最终的异步操作类如下所示:

```
class AsyncOperation: Operation {
    private let lockQueue = DispatchQueue(label: "com.swiftlee.asyncoperation", attributes: .concurrent)

    override var isAsynchronous: Bool {
        return true
    }

    private var _isExecuting: Bool = false
    override private(set) var isExecuting: Bool {
        get {
            return lockQueue.sync { () -> Bool in
                return _isExecuting
            }
        }
        set {
            willChangeValue(forKey: "isExecuting")
            lockQueue.sync(flags: [.barrier]) {
                _isExecuting = newValue
            }
            didChangeValue(forKey: "isExecuting")
        }
    }

    private var _isFinished: Bool = false
    override private(set) var isFinished: Bool {
        get {
            return lockQueue.sync { () -> Bool in
                return _isFinished
            }
        }
        set {
            willChangeValue(forKey: "isFinished")
            lockQueue.sync(flags: [.barrier]) {
                _isFinished = newValue
            }
            didChangeValue(forKey: "isFinished")
        }
    }

    override func start() {
        print("Starting")
        guard !isCancelled else {
            finish()
            return
        }

        isFinished = false
        isExecuting = true
        main()
    }

    override func main() {
        fatalError("Subclasses must implement `execute` without overriding super.")
    }

    func finish() {
        isExecuting = false
        isFinished = true
    }
}
```

该类需要作为子类使用，因此，当使用 main 方法时，我们会触发一个致命错误。

例如，您将上传一个带有`FileUploadOperation`的文件:

```
final class FileUploadOperation: AsyncOperation {

    private let fileURL: URL
    private let targetUploadURL: URL
    private var uploadTask: URLSessionTask?

    init(fileURL: URL, targetUploadURL: URL) {
        self.fileURL = fileURL
        self.targetUploadURL = targetUploadURL
    }

    override func main() {
        uploadTask = URLSession.shared.uploadTask(with: URLRequest(url: targetUploadURL), fromFile: fileURL) { (data, response, error) in
            // Handle the response
            // ...
            // Call finish
            self.finish()
        }
    }

    override func cancel() {
        uploadTask?.cancel()
        super.cancel()
    }
}
```

请注意，我们正在保存数据任务，以便在需要时可以取消它。

这只是一个很基本的例子。在 [*通过 WeTransfer*](https://collect.bywetransfer.com) 收集中，我们使用了很多操作，例如:

*   内容创作
*   内容接收
*   内容上传
*   内容丰富

还有很多。最棒的是你可以将这些操作链接在一起，就像你在上一篇关于[操作入门](https://www.avanderlee.com/swift/operations/)的文章中所学到的那样。

# 结论

就是这样！我们已经创建了异步操作，您可以直接在您的项目中使用。希望它能让您更好地分离关注点和高性能代码。

谢谢！
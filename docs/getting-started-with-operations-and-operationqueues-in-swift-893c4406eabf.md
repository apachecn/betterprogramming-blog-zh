# Swift 中的操作和操作队列入门

> 原文：<https://betterprogramming.pub/getting-started-with-operations-and-operationqueues-in-swift-893c4406eabf>

## Swift 中的并发性和关注点分离

![](img/f71d07d050a961c472a5851e10ed272f.png)

照片由[哈尔·盖特伍德](https://unsplash.com/@halgatewood?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/queue?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

Swift 中的操作是在跟踪进度和依赖关系的同时将职责划分到几个类别的强大方法。它们的正式名称是 NSOperations，与`OperationQueue`结合使用。

请务必先阅读我关于 Swift 中的[并发性的文章](https://www.avanderlee.com/swift/concurrent-serial-dispatchqueue/)，这样您就知道了队列和分派的基础知识。操作与调度块有很多共同之处，但是有更多的好处。让我们开始吧！

# Swift 中的操作是什么？

一个操作通常负责一个同步任务。这是一个抽象类，从来没有直接使用过。您可以利用系统定义的`BlockOperation`子类或者创建自己的子类。您可以通过将操作添加到`OperationQueue`或手动调用 start 方法来启动操作。但是，强烈建议将管理状态的全部责任交给`OperationQueue`。

利用系统定义的`BlockOperation`看起来如下:

```
let blockOperation = BlockOperation {
    print("Executing!")
}

let queue = OperationQueue()
queue.addOperation(blockOperation)
```

这也可以通过直接在队列中添加块来实现:

```
queue.addOperation {
  print("Executing!")
}
```

给定的任务被添加到`OperationQueue`中，它将尽快开始执行。

# 创建自定义操作

您创建了自定义操作的关注点分离。例如，您可以为导入内容创建一个自定义实现，为上传内容创建另一个自定义实现。

下面的代码示例显示了用于导入内容的自定义子类:

```
final class ContentImportOperation: Operation {

    let itemProvider: NSItemProvider

    init(itemProvider: NSItemProvider) {
        self.itemProvider = itemProvider
        super.init()
    }

    override func main() {
        guard !isCancelled else { return }
        print("Importing content..")

        // .. import the content using the item provider

    }
}
```

该类接受一个项提供程序，并在 main 方法中导入内容。对于同步操作，`main()`函数是唯一需要重写的方法。将操作添加到队列中，并设置一个完成块来跟踪完成情况:

```
let fileURL = URL(fileURLWithPath: "..")
let contentImportOperation = ContentImportOperation(itemProvider: NSItemProvider(contentsOf: fileURL)!)

contentImportOperation.completionBlock = {
    print("Importing completed!")
}

queue.addOperation(contentImportOperation)

// Prints:
// Importing content..
// Importing completed!
```

这将您导入内容的所有逻辑转移到一个单独的类中，您可以在这个类中跟踪进度、完成情况，并且可以轻松地编写测试。

## 操作的不同状态

根据操作的当前执行状态，操作可以处于几种状态。

*   准备好了:准备开始了
*   **正在执行:**任务当前正在运行
*   **完成:**流程完成后
*   **取消:**任务被取消

知道一个操作只能执行一次是很重要的。每当它处于完成或取消状态时，您就不能再重新启动同一个实例。

在自定义实现中，您需要在执行之前手动检查取消状态，以确保任务取消。要知道，当一个操作同时开始和取消时，可能会发生数据竞争。你可以在我的博客文章“ [Thread Sanitizer 解释:Swift](https://www.avanderlee.com/swift/thread-sanitizer-data-races/) 中的数据竞争”中阅读更多关于数据竞争的内容

一旦任务完成,`OperationQueue`将自动从其队列中删除该任务，这发生在执行或取消之后。

# 利用依赖关系

使用操作的一个好处是可以使用依赖关系。您可以轻松地在两个实例之间添加依赖关系。例如，要在内容导入后开始上传:

```
let fileURL = URL(fileURLWithPath: "..")
let contentImportOperation = ContentImportOperation(itemProvider: NSItemProvider(contentsOf: fileURL)!)
contentImportOperation.completionBlock = {
    print("Importing completed!")
}

let contentUploadOperation = UploadContentOperation()
contentUploadOperation.addDependency(contentImportOperation)
contentUploadOperation.completionBlock = {
    print("Uploading completed!")
}

queue.addOperations([contentImportOperation, contentUploadOperation], waitUntilFinished: true)

// Prints:
// Importing content..
// Uploading content..
// Importing completed!
// Uploading completed!
```

只有在内容导入完成后，上传才会开始。它不考虑取消，这意味着如果导入操作取消，上传仍然会开始。

您必须实现一个检查来查看依赖项是否被取消:

```
final class UploadContentOperation: Operation {
    override func main() {
        guard !dependencies.contains(where: { $0.isCancelled }), !isCancelled else {
            return
        }

        print("Uploading content..")
    }
}
```

# 结论

我希望您对开始在 Swift 中实施运营感到兴奋。它是一颗隐藏的宝石，允许您分离关注点，添加任务之间的依赖关系，并跟踪完成情况。
# Swift 中使用泛型的高级异步操作

> 原文：<https://betterprogramming.pub/advanced-asynchronous-operations-using-generics-in-swift-b55932a15f6a>

## 让您的异步操作更上一层楼

![](img/85143d23647616812ca00b1e85e19b50.png)

照片由 [Max Nelson](https://unsplash.com/@maxcodes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/swift?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

异步操作允许您以不同的方式编写长时间运行的任务，同时在几个任务之间添加依赖关系。通过使用`OperationQueue`，可以跟踪进度并简化调度。通过添加泛型和 Swift 结果类型，我们可以从异步操作中获得更多。

在[开始操作](https://www.avanderlee.com/swift/operations/)和[使用异步操作](https://www.avanderlee.com/swift/asynchronous-operations/)编写并发解决方案之后，现在是时候看看我们如何为我们的代码库创建一个更高级的解决方案了。这完全基于我们在 [Collect by WeTransfer](https://collect.bywetransfer.com/) 应用中使用的实现，其中我们使用了 50 多种不同的操作。

# 创建结果驱动的异步操作

通常情况下，运算会产生一个特定的值。至少有一种方法来捕捉发生的错误是有价值的。在 Swift 的`Result<Success, Failure>`类型中，值和误差一起出现。

## 向异步操作添加泛型

通过利用博客文章[中创建的`AsyncOperation`来编写并发解决方案](https://www.avanderlee.com/swift/asynchronous-operations/)，我们给了自己一个 kickstart。我们将相同的泛型作为结果类型添加到它的上面，这导致了下面的基础:

```
class AsyncResultOperation<Success, Failure>: AsyncOperation where Failure: Error {

    private(set) var result: Result<Success, Failure>?

}
```

这为异步任务提供了一个链接的结果类型，对于值和错误都有一个泛型。

## 确保完工后的结果

为了确保结果的更新，我们在 finish 方法中添加了一个检查来帮助实现者，并在开发时给他们反馈:

```
final override func finish() {
    guard !isCancelled else { return super.finish() }
    fatalError("Make use of finish(with:) instead to ensure a result")
}

func finish(with result: Result<Success, Failure>) {
    self.result = result
    super.finish()
}
```

首先，我们确保默认的`finish()`方法不再有用。当它的一个实现者使用它时，它抛出一个致命的异常:

```
Fatal error: Make use of finish(with:) instead to ensure a result: file UnfurlURLOperation.swift, line 44
```

其次，我们添加了一个新的`finish(with:)`方法，强制实现者设置结果。

## 确保取消的结果

最后一部分确保我们有一个关于取消的结果集。因为我们喜欢有一种类型的错误，我们被迫在类中创建另一个覆盖:

```
override func cancel() {
    fatalError("Make use of cancel(with:) instead to ensure a result")
}

func cancel(with error: Failure) {
    self.result = .failure(error)
    super.cancel()
}
```

我们必须在错误枚举中为取消定义一个 case。这样做给了我们强类型错误的好处，同时仍然能够在取消时得到结果。

## 将所有这些放在一个示例操作中

最后的`AsyncResultOperation`是这样的:

```
open class AsyncResultOperation<Success, Failure>: AsyncOperation where Failure: Error {

    private(set) public var result: Result<Success, Failure>?

    final override public func finish() {
        guard !isCancelled else { return super.finish() }
        fatalError("Make use of finish(with:) instead to ensure a result")
    }

    public func finish(with result: Result<Success, Failure>) {
        self.result = result
        super.finish()
    }

    override open func cancel() {
        fatalError("Make use of cancel(with:) instead to ensure a result")
    }

    public func cancel(with error: Failure) {
        self.result = .failure(error)
        super.cancel()
    }
}
```

我们可以通过创建一个自定义操作来利用这个类，比如展开短 url 的任务。

```
final class UnfurlURLOperation: AsyncResultOperation<URL, UnfurlURLOperation.Error> {
    enum Error: Swift.Error {
        case canceled
        case missingRedirectURL
        case underlying(error: Swift.Error)
    }

    private let shortURL: URL
    private var dataTask: URLSessionTask?

    init(shortURL: URL) {
        self.shortURL = shortURL
    }

    override func main() {
        var request = URLRequest(url: shortURL)
        request.httpMethod = "HEAD"

        dataTask = URLSession.shared.dataTask(with: request, completionHandler: { [weak self] (_, response, error) in
            if let error = error {
                self?.finish(with: .failure(Error.underlying(error: error)))
                return
            }

            guard let longURL = response?.url else {
                self?.finish(with: .failure(Error.missingRedirectURL))
                return
            }

            self?.finish(with: .success(longURL))
        })
        dataTask?.resume()
    }

    override func cancel() {
        dataTask?.cancel()
        cancel(with: .canceled)
    }
}
```

这个类获取一个短 URL，执行一个`HEAD`请求来获取长 URL，并返回结果。如果出现任何错误，那都是操作的结果。最后，我们确保在取消时取消数据任务，并使用我们的`canceled`错误案例调用`cancel(with:)`方法。

执行这个方法最终得到的结果是`www.avanderlee.com`作为展开的 URL:

```
let queue = OperationQueue()
let unfurlOperation = UnfurlURLOperation(shortURL: URL(string: "https://bit.ly/33UDb5L")!)
queue.addOperations([unfurlOperation], waitUntilFinished: true)
print("Operation finished with: \(unfurlOperation.result!)")

// Prints: Operation finished with: success(https://www.avanderlee.com/)
```

# 任务的强类型链接

虽然上面的操作已经很有价值了，但是我们可以再走一步，让操作更加灵活。我们可以通过创建一个链式异步操作来做到这一点。

该操作考虑了依赖性，并使用其依赖性的结果作为输入。我们首先创建一个新的`ChainedAsyncOperation`类，它将输入和输出作为通用参数:

```
open class ChainedAsyncResultOperation<Input, Output, Failure>: AsyncResultOperation<Output, Failure> where Failure: Swift.Error {

    private(set) public var input: Input?

    public init(input: Input? = nil) {
        self.input = input
    }
}
```

我们创建一个带有可选输入的初始化器。由于这个链必须从某个地方开始，我们需要有一个选项来手动设置输入，而不是从依赖项中推迟它。

## 从依赖项更新输入

下一步是更新依赖项的输入。我们希望在任务开始时就这样做，这可以通过覆盖`start()`方法来实现。

```
override public final func start() {
    updateInputFromDependencies()
    super.start()
}

/// Updates the input by fetching the output of its dependencies.
/// Will always get the first output matching dependency.
/// If `input` is already set, the input from dependencies will be ignored.
private func updateInputFromDependencies() {
    guard input == nil else { return }
    input = dependencies.compactMap { dependency in
        return (dependency as? ChainedOperationOutputProviding)?.output as? Input
    }.first
}
```

如你所见，我们已经创建了一个新的`ChainedOperationOutputProviding`协议。这个协议允许我们从链中的另一个操作获取输出。协议本身看起来如下:

```
protocol ChainedOperationOutputProviding {
    var output: Any? { get }
}

extension ChainedAsyncResultOperation: ChainedOperationOutputProviding {
    var output: Any? {
        return try? result?.get()
    }
}
```

## 通过链接两个操作将它们放在一起

最后一个链接的操作类如下所示:

```
open class ChainedAsyncResultOperation<Input, Output, Failure>: AsyncResultOperation<Output, Failure> where Failure: Swift.Error {

    private(set) public var input: Input?

    public init(input: Input? = nil) {
        self.input = input
    }

    override public final func start() {
        updateInputFromDependencies()
        super.start()
    }

    /// Updates the input by fetching the output of its dependencies.
    /// Will always get the first output matching dependency.
    /// If `input` is already set, the input from dependencies will be ignored.
    private func updateInputFromDependencies() {
        guard input == nil else { return }
        input = dependencies.compactMap { dependency in
            return (dependency as? ChainedOperationOutputProviding)?.output as? Input
        }.first
    }
}
```

我们可以使用这个类来创建一个操作链。例如，通过添加另一个获取展开的 url 的标题的操作来获取前面的展开操作。为此，我们必须创建一个新的`FetchTitleChainedOperation`:

```
public final class FetchTitleChainedOperation: ChainedAsyncResultOperation<URL, String, FetchTitleChainedOperation.Error> {
    public enum Error: Swift.Error {
        case canceled
        case dataParsingFailed
        case missingInputURL
        case missingPageTitle
        case underlying(error: Swift.Error)
    }

    private var dataTask: URLSessionTask?

    override final public func main() {
        guard let input = input else { return finish(with: .failure(.missingInputURL)) }

        var request = URLRequest(url: input)
        request.httpMethod = "GET"

        dataTask = URLSession.shared.dataTask(with: request, completionHandler: { [weak self] (data, response, error) in
            do {
                if let error = error {
                    throw error
                }

                guard let data = data, let html = String(data: data, encoding: .utf8) else {
                    throw Error.dataParsingFailed
                }

                guard let pageTitle = self?.pageTitle(for: html) else {
                    throw Error.missingPageTitle
                }

                self?.finish(with: .success(pageTitle))
            } catch {
                if let error = error as? Error {
                    self?.finish(with: .failure(error))
                }
                self?.finish(with: .failure(.underlying(error: error)))
            }
        })
        dataTask?.resume()
    }

    private func pageTitle(for html: String) -> String? {
        guard let rangeFrom = html.range(of: "<title>")?.upperBound else { return nil }
        guard let rangeTo = html[rangeFrom...].range(of: "</title>")?.lowerBound else { return nil }
        return String(html[rangeFrom..<rangeTo])
    }

    override final public func cancel() {
        dataTask?.cancel()
        cancel(with: .canceled)
    }
}
```

该操作获取输入 URL，执行 GET 请求，并从最终的 HTML 结果中获取标题。

两者一起执行看起来像这样:

```
let queue = OperationQueue()
let unfurlOperation = UnfurlURLChainedOperation(shortURL: URL(string: "https://bit.ly/33UDb5L")!)
let fetchTitleOperation = FetchTitleChainedOperation()
fetchTitleOperation.addDependency(unfurlOperation)

queue.addOperations([unfurlOperation, fetchTitleOperation], waitUntilFinished: true)

print("Operation finished with: \(fetchTitleOperation.result!)")
// Prints: Operation finished with: success("A weekly Swift Blog on Xcode and iOS Development - SwiftLee")
```

该代码演示了在设置好操作之后将它们链接在一起是多么容易。在操作之间添加一个依赖关系，这一切都解决了。

如果您必须执行多个重要的任务，并且希望将代码分开，那么组合任务尤其有用。它允许您确保关注点的分离和隔离的可测试代码。

# 结论

就是这样！我们利用 Swift 中的泛型创建了高级操作。通过将它们链接在一起，我们可以在保持代码分离的同时创建任务之间的关系。

这件作品是一个系列的一部分:

*   [Swift 中的操作和操作队列入门](https://www.avanderlee.com/swift/operations/)
*   [Swift 中编写并发解决方案的异步操作](https://www.avanderlee.com/swift/asynchronous-operations/)
*   [利用泛型实现高级异步操作](https://www.avanderlee.com/swift/advanced-asynchronous-operations/)

如果您想进一步提高您的 Swift 知识，请查看 [Swift 类别页面](https://www.avanderlee.com/category/swift/)。

谢谢！
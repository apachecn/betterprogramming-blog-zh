# Swift 并发处理的未来

> 原文：<https://betterprogramming.pub/the-future-of-concurrency-handling-in-swift-f75ab24d6ba8>

## Async/await、actors 以及如何使用它们

![](img/8e24cdc55c0d1d0d03020892ae56c8b6.png)

资料来源:undraw

炒作终于真实了。在 iOS 15 和 Xcode 13 的推出中，苹果带来了一种新的方式来支持并发，并以更结构化的方式编写异步和并行代码。它是由 swift-evolution GitHub 知识库上的社区提出的。如果你对它很好奇，[你可以在这里查看一下，值得一看](https://github.com/apple/swift-evolution/blob/main/proposals/0296-async-await.md)。还有，而且随着 Xcode 13.2 的推出，Swift 并发已经回移植到 iOS 13，这显然是一个天大的好消息！现在让我们开始吧。

# 异步ˌ非同步(asynchronous)

这是 Swift 中异步的一个关键字。这里是为了标记一个方法将执行一个异步任务。这里有一个例子

```
func getStuff() async -> String {   
    return "stuff"
}
```

目前，在这个方法中没有执行真正的异步任务(我们将在本文后面添加代码),但是它已经准备好接受一些任务，因为我们用`async`标记了方法的签名

# 等待

是另一个应该和`async`配对的关键词。当之前用来调用一个`async`方法时，它告诉代码等待它的执行和返回。

```
let stuff = await getStuff()
print(stuff)
```

在这个例子中，`print(stuff)`不会被执行，直到`await getStuff()`返回值。这就是为什么这种处理并发的方法比其他方法干净得多:它简单而高效。

# 如何一起使用它们

让我们把所有的东西放在一起。如果你试图这样调用你的`async`方法

```
func fetchData() {
    do {
        let stuff = try await getStuff()
        print(stuff)
    } catch {
        print(error)
    }
}
```

你将会得到一个来自编译器的错误

```
'async' call in a function that does not support concurrency
```

你可以在`fetchData()`签名上放一个`async`关键字来解决这个问题……但是你必须对调用`fetchData()`的方法做同样的事情，等等。

因此，我们将使用一个`Task.init`方法从一个支持并发的新任务中调用异步方法。这被称为非结构化并发。这意味着我们在这里创建的任务没有父任务，因此我们可以完全灵活地管理它。

综合所有因素，结果如下:

```
func getStuff() async throws -> String {
    let url = URL(string: "https://niceurl/api/stuff")!
    let (data,_) = try await URLSession.shared.data(from: url)
    let string = String(data: data, encoding: .utf8) ?? ""
    return string
}func fetchData() {
    Task.init {
        do {
            let stuff = try await getStuff()
            print(stuff)
        } catch {
            print(error)
        }
    }
}
```

你可以注意到`getStuff()`中添加的代码。使用`URLSession`是一个简单的请求，但有趣的是，我们在呼叫线上有另一个`await`。它显然会在处理数据并将字符串返回给`fetchData()`方法之前等待服务器的回复。一个小的附带说明是，你完全可以在`async`后添加关键字`throws`，然后用`try-catch`处理你的错误。

此外，这是一种它可以替换的代码:

```
func getStuff(completion: @escaping (Result<String, Error>)->()) {
    let url = URL(string: "https://niceurl/api/stuff")!
    let task = URLSession.shared.dataTask(with: url) {
        data, response, error in
        if let error = error {
            completion(.failure(error))
        } else if let data = data {
            let string = String(data: data, encoding: .utf8) ?? ""
            completion(.success(string))
        }
    }
    task.resume()
}getStuff(completion: { result in
    switch result {
    case .success(let value):
        print(value)
    case .failure(let error):
        print(error.localizedDescription)
    }
})
```

代码更加清晰，可读性更好，因此明显更不容易出错。此外，不使用闭包避免了保留循环的风险(补充说明:当使用闭包时，不要忘记削弱自身或任何外部属性)。最后，一个很好的优势是您可以像前面看到的那样在实现级别使用`try-catch`，这对于完成块是不可能的。

# 关于任务的更多信息

为了把所有的东西放在一起，我们稍微谈了一下任务，更具体地谈了一下`Task.init`。还有两件事需要注意。第一，虽然`Task.init`是一个运行在当前 actor 上的非结构化任务，但是您也可以使用`Task.detached`运行一个不属于当前 actor 的非结构化任务。第二，这两个方法都返回一个任务句柄，允许你和任务进行一些交互，比如说，如果需要的话可以取消任务。

```
func fetchData() {
    let taskHandle = Task.detached {
        do {
            let stuff = try await getStuff()
            print(stuff)
        } catch {
            print(error)
        }
    }
    taskHandle.cancel()
}
```

# 并行调用异步函数

如果您尝试像这样运行多个`await`调用

```
Task.init {
    do {
        let stuff0 = try await getStuff(id: 0)
        let stuff1 = try await getStuff(id: 1)
        let stuff2 = try await getStuff(id: 2)
        print(stuff0, stuff1, stuff2)
    } catch {
        print(error)
    }
}
```

你将面临一个小问题。尽管所有这些`getStuff(id:)`调用都是异步的，但此时只有一个调用会运行，而其他调用会等待前一个调用完成后才开始运行。但是在我们的例子中，不需要等待上一次调用，因为每个调用都可以独立运行。

谢天谢地，苹果为我们提供了一个解决方案来应对这种情况

```
Task.init {
    do {
        async let stuff0 = try getStuff(id: 0)
        async let stuff1 = try getStuff(id: 1)
        async let stuff2 = try getStuff(id: 2)
        try await print(stuff0, stuff1, stuff2)
    } catch {
        print(error)
    }
}
```

只需在您想要将调用结果赋给的 let 常量前写`async`，然后在您想要使用该常量时写`await`。这个例子中的一个小变化是，您需要将`try`放在`await`的前面。有了这个实现，所有的`getStuff(id:)`调用都是单独运行的，不需要等待前一个调用。最后，由于我们之前讨论过任务，`async let`语法为您创建了一个子任务，可以在任务组中添加和管理它。如果你想了解更多关于任务组的信息，请查看由[保罗·哈德森](https://www.hackingwithswift.com/about)撰写的[这篇文章。](https://www.hackingwithswift.com/quick-start/concurrency/how-to-create-a-task-group-and-add-tasks-to-it)

# 行动者

类似于类，它是一个引用类型，这意味着引用类型和值类型(如 struct)之间的比较适用于`actor`类型。关键的区别在于参与者一次只允许一个任务访问它们的可变状态，这使得多个任务中的代码可以安全地与同一个`actor`实例进行交互。

```
actor Stuff {
    let name: String
    var numberOf: Int init(name: String, numberOf: Int) {
        self.name = name
        self.numberOf = numberOf
    }
}
```

关键字`actor`替换了类，你只需使用它的初始化器来初始化它。

```
Stuff(name: "Great Stuff", numberOf: 42)
```

然后，您拉回关键字`await`来访问您的`actor`的一个属性

```
print(await greatStuff.name)
```

但是这里有一些重要的事情需要注意。上面的代码是一个可能的暂停点。因为一个`actor`一次只能让一个任务访问它的可变状态，这意味着直到`actor`允许它访问它的属性名，这段代码才会执行。

也就是说，您不需要编写`await`来从 actor 内部访问代码。例如，如果您正在修改一个由`actor`拥有的方法的属性，您就处于所谓的 actor 局部状态，这意味着只有在`actor`内部的代码才能访问这个状态。Swift 通过强制从外部调用`actor`的代码与`await`配对来保证这一点，如果不这样做就无法编译。苹果称之为*演员隔离*，这也是`actor`安全的原因。

# 结论

`async / await`和`actor`即将成为 Swift 中处理并发任务的默认解决方案。由于 Xcode 13.2 和对 iOS 13 的支持，以及今年晚些时候 iOS 16 的到来，这个新概念肯定会在未来几个月丰富许多代码库。
# Go、WebAssembly、HTTP 请求和承诺

> 原文：<https://betterprogramming.pub/go-webassembly-http-requests-and-promises-ddb76af69ba8>

## Go/Wasm 中的 JavaScript 交互指南

![](img/bc9b3a2d49b41f71d12f575fc7995c1c.png)

[萨莉娜](https://unsplash.com/@sairaa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/@sairaa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

[WebAssembly](https://webassembly.org/) ，或 Wasm，是一个开放的标准，允许开发者使用编译的编程语言构建在网络浏览器中运行的应用。使用 WebAssembly，可以用 C/C++、Rust、C#和 Go 等语言编写 web 应用程序的组件，这些组件与 JavaScript 在同一个沙箱中运行。这允许诸如移植现有库、利用 JavaScript 中不可用的功能，以及更快地运行代码，因为 WebAssembly 被编译成二进制格式。

最近我一直在用 WebAssembly 进行试验，以便能够在浏览器中运行一些 [Go](https://golang.org/) 代码。具体来说，我一直在尝试将用于存储端到端加密文档的开源项目 [prvt](https://github.com/italypaleale/prvt) 的某些部分移植到 web 浏览器中直接运行。WebAssembly 实验的原因有两个:首先，prvt 广泛使用了密码学和流，这两种东西在 JavaScript 中还不是很好；第二，prvt 本身是用 Go 编写的，因此能够在浏览器中重用代码将大大简化开发。

关于使用 WebAssembly 和 Go 的介绍，我推荐这篇关于 Golang Bot 的文章。此外，更多信息可以在 Go 项目的 [GitHub wiki](https://github.com/golang/go/wiki/WebAssembly) 和 [syscall/js](https://golang.org/pkg/syscall/js/) 包的文档中找到。
请注意，在撰写本文时，**Go 中的 WebAssembly 支持仍处于试验阶段**。因此，API 也可能会改变。本文针对 Go 1.15 进行了测试。

这篇文章包含了我在使用 WebAssembly 端口时学到的四个不同但相关的东西，我认为分享这些东西很有用。

1.  从 Go 代码使用和创建 JavaScript 对象
2.  在 Go 中创建传递异步结果的承诺
3.  从 Go 代码发出 HTTP 请求
4.  从 Go 代码流

# WebAssembly 和 Go 中的 JavaScript 对象

WebAssembly runtime for Go 会自动将最常见的 Go 类型转换为对应的 JavaScript 类型。`[js.ValueOf](https://golang.org/pkg/syscall/js/#ValueOf)`方法的文档包含一个关于 Go 和 JavaScript 类型如何匹配的很好的汇总表:

```
| Go                     | JavaScript             |
| ---------------------- | ---------------------- |
| js.Value               | [its value]            |
| js.Func                | function               |
| nil                    | null                   |
| bool                   | boolean                |
| integers and floats    | number                 |
| string                 | string                 |
| []interface{}          | new array              |
| map[string]interface{} | new object             |
```

从这里，您可以看到最常见的类型，如数字、布尔值和字符串，都是自动转换的。最后一行特别有趣，因为它解释了如何传递“普通旧 JavaScript 对象”(POJO)，这是最简单的对象(也称为字典)。

例如，下面的 Go 代码定义了一个名为`MyGoFunc`的函数，可以从 JavaScript 代码中调用该函数，该函数返回一个包含一个字符串和一个数字的字典(如您所见，类型可以是异构的)。

有关如何将 Go 代码编译成 WebAssembly 的说明，请查看 Wiki 的[入门部分](https://github.com/golang/go/wiki/WebAssembly#getting-started)。

将代码编译成 WebAssembly 并导入 JavaScript 代码后，您可以从 JavaScript 调用`MyGoFunc()`来查看结果。例如:

```
console.log(MyGoFunc())
// Prints: {hello: "world", answer: 42}
```

然而，文档中没有明确说明的是，我们还可以在 Go 代码中使用任何 JavaScript 对象，甚至是内置对象！这是事情变得更有趣的地方。

例如，让我们尝试将日期作为`[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)`对象传递。为此，我们首先需要获取`Date`构造函数，从 JS 的全局范围加载它的`js.Value`:

```
dateConstructor := js.Global().Get("Date")
```

然后我们可以用`dateConstructor.New`方法从这个构造函数创建一个新的对象，像在 JavaScript 中传递给`new Date()`构造函数一样传递任何参数给它。调用的结果是一个可以返回给 JavaScript 的`js.Value`:

```
dateConstructor.New("2020-10-01")
```

因此，我们可以修改我们的`MyGoFunc`来返回在 Go:

在 JavaScript 代码中调用`MyGoFunc()`将会返回一个`Date`对象:

```
let d = MyGoFunc()
console.log(typeof d)
console.log(d instanceof Date)
console.log(d.toString())/*
Prints:object
true
Sat Oct 03 2020 10:58:27 GMT-0700 (Pacific Daylight Time)
*/
```

# 来自 Go 的承诺的异步 JS

在 JavaScript 中，`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)`是 async/await 的基础。如果你需要重温承诺，这是一篇好文章。

例如，考虑下面的代码，它创建了一个承诺，三秒钟后通过一条消息([一个意大利绕口令](https://www.mamalisa.com/?t=es&p=5534))解决:

```
const p = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("sopra la panca la capra campa, sotto la panca la capra crepa")
    }, 3000)
})
```

在一个`async`函数中，你可以在上面的`await`上承诺，所以三秒钟后，你收到消息:

```
// This is an async function, which can contain "await" statements inside
async function MyFunc() {
    // Create the Promise
    const p = new Promise((resolve, reject) => {
        // After a 3 second timeout, this calls "resolve" with the message we're passing
        setTimeout(() => {
            resolve("sopra la panca la capra campa, sotto la panca la capra crepa")
        }, 3000)
    })
    // Await for the Promise - this resolves after 3 seconds
    const message = await p
    console.log(message)
}
```

调用`MyFunc()`会在控制台显示`sopra la panca la capra campa, sotto la panca la capra crepa`。

在 Go 中与 Wasm 合作时，承诺尤为重要。

事实上，根据文档，您不能在由 JavaScript 直接调用的函数内部进行阻塞调用——如果您这样做，您将立即得到一个死锁，并且您的应用程序将崩溃。相反，文档建议所有阻塞调用都在 goroutine 中，这就产生了将值返回给 JavaScript 代码的问题。引用自[文件](https://golang.org/pkg/syscall/js/#FuncOf):

> “从 JavaScript 调用包装的 Go 函数将暂停事件循环并产生新的 goroutine。在调用从 Go 到 JavaScript 的过程中触发的其他包装函数在同一个 goroutine 上执行。
> 
> 因此，如果一个被包装的函数阻塞，JavaScript 的事件循环将被阻塞，直到该函数返回。因此，调用任何需要事件循环的异步 JavaScript API，比如 fetch (http。客户端)，将导致立即死锁。因此，阻塞函数应该显式启动新的 goroutine。

使用 Promise 可能是解决这个问题的最好方法，既避免了死锁，又允许用惯用的 JavaScript 编程。

我们在上一节中看到，我们可以从 Go 创建定制的 JavaScript 对象，这也适用于承诺！我们只需要通过向构造函数传递一个函数来创建`Promise`对象。就像上面的 pure-JS 代码一样，这个函数接收两个参数，这两个参数本身就是函数:`resolve`应该在 Promise 的工作完成时用最终结果调用，而`reject`可以在出现错误时调用，使 Promise 失败。

这里有一个更新的`MyGoFunc`解决了一个消息([另一个意大利绕口令！](https://www.bbc.co.uk/languages/yoursay/tongue_twisters/italian/trotting_trentonians.shtml))三秒钟后:

要从 JavaScript 调用它:

```
async function MyFunc() {
    // Get the Promise from Go
    const p = MyGoFunc()
    // Show the current UNIX timestamps (in seconds)
    console.log(Math.floor(Date.now() / 1000))
    // Await for the Promise to resolve
    const message = await p
    // Show the current timestamp in seconds, then the result of the Promise
    console.log(Math.floor(Date.now() / 1000), message)
}/*
Result:
  1601746916
  1601746919 "Trentatré Trentini entrarono a Trento, tutti e trentatré trotterellando"
*/
```

如果您的 Go 代码出错，您可以通过使用`reject`函数来抛出 JavaScript 异常。例如:

当您从 JavaScript 调用这个函数时，大约有一半时间您会看到返回的对象，另一半时间您会得到一个异常。注意，我们用一个实际的 JavaScript `Error`对象调用`reject`函数，这是 JavaScript 的最佳实践。

```
async function MyFunc() {
    try {
        console.log(await MyGoFunc())
    } catch (err) {
        console.error('Caught exception', err)
    }
}/*
Result is either:
  {error: null, message: "Hooray, it worked!"}
Or a caught exception (followed by the stack trace):
  Caught exception Error: Nope, it failed
*/
```

# 从 Go 代码发出 HTTP 请求

最后，让我们看看如何使用 Go 和 WebAssembly 来发出 HTTP 请求，这是一个非常常见的任务。例如，您可以在一个[服务工作器](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers)中这样做，以[拦截网络请求](https://developer.mozilla.org/en-US/docs/Web/API/FetchEvent)并让 Go 处理它们(这就是我对 prvt 所做的，所以 Go 代码可以解密文件)。

有两件重要的事情要记住:

1.  来自 Go 的网络调用是阻塞的，所以它们必须在单独的 Goroutine 中执行。因此，我们应该从 Go 向 JavaScript 返回一个承诺，最终以网络请求的结果进行解析。
2.  如果您的目标是拦截网络请求，那么您的 Go 代码应该返回包装在 JavaScript `[Response](https://developer.mozilla.org/en-US/docs/Web/API/Response)`对象中的响应。

这里有一个例子:

然后，我们可以在 JavaScript 代码中使用它来调用任何 REST API 并获得结果，就好像它是一个`fetch`请求一样。例如，在下面的代码中，我们调用了 [taylor.rest](https://taylor.rest/) API，该 API 会从 Taylor Swift 返回一段随机引用:

```
async function MyFunc() {
    try {
        const response = await MyGoFunc('https://api.taylor.rest/')
        const message = await response.json()
        console.log(message)
    } catch (err) {
        console.error('Caught exception', err)
    }
}/*
Result is a quote from Taylor Swift, as a JSON object. For example:
  {"quote":"The only one who's got enough of me to break my heart."}
*/
```

请注意，当从 Go 发出 HTTP 请求时，WebAssembly 运行时在内部将调用转换为浏览器中的 fetch 请求。因此，即使在使用 WebAssembly 时，您仍然受到与 JavaScript `fetch`调用相同的安全策略和要求的约束，包括 CORS。

# 从 Go 代码流

最后，还有一件事。我们已经看到了如何从 WebAssembly/Go 发出 HTTP 请求并返回数据。只有一个问题:

```
data, err := ioutil.ReadAll(res.Body)
```

在这一行中，我们在将响应返回给 JavaScript 之前，在内存中读取整个响应的主体。这在很多(大多数？)案例——但是，如果您试图读取一个非常大的文件，比如一个视频，该怎么办呢？上面的调用需要大量内存。

幸运的是，我们可以将响应流回。遗憾的是，由于 JavaScript 对流的支持相对不成熟(在 Node.js 之外)，这并不简单。解决方案包括在 WebAssembly 代码中创建一个`[ReadableStream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream)` JS 对象，然后一旦数据在流中可用，就使用它的 API 来传递数据。

`MyGoFunc(url)`的最后一次迭代可以用来以流的形式检索数据。例如，在我们的 JavaScript 代码中，我们可以请求一个图像，并看到它以块的形式到达:

```
async function MyFunc() {
    try {
        const response = await MyGoFunc('https://images.unsplash.com/photo-1571079520814-c2840ce6ec7b')
        const reader = response.body.getReader()
        let done = false
        while (!done) {
            const read = await reader.read()
            done = read && read.done
            console.log('Read', read.value.length, 'bytes')
        }
    } catch (err) {
        console.error('Caught exception', err)
    }
}
```

当您调用这个函数时，您将在控制台中看到一串类似于`Read 16384 bytes`的语句被重复多次，有时字节数不同，但不会超过 16384，因为我们使用的是 16KB 的缓冲区。
# 在 Go 中使用同步原语

> 原文：<https://betterprogramming.pub/using-synchronization-primitives-in-go-mutex-waitgroup-once-2e50359cb0a7>

## 探索互斥、WaitGroup 和 Once 并举例说明

![](img/da910b66001b0e7e2f26c568a6f71e2b.png)

[霍利·曼达里奇](https://unsplash.com/@hollymandarich?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/go?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

欢迎来到适可而止 Go！

这是关于 [Go 编程语言](https://golang.org/)的系列文章的第二篇，在这篇文章中，我将介绍一些最常用的 Go 标准库包，例如[编码/json](https://golang.org/pkg/encoding/json/) 、 [io](https://golang.org/pkg/io/) 、 [net/http](https://golang.org/pkg/net/http/) 、 [sync](https://golang.org/pkg/sync/) 等。我计划保持这些相对简短和例子驱动。

除了 Goroutines 和通道之外，让我们看看 Go 在`[sync](https://godoc.org/sync)` [包](https://godoc.org/sync)中提供的一些底层同步结构。有很多，但是我们将通过例子来探索`WaitGroup`、`Mutex`和`Once`。

GitHub 上有[的代码示例。](https://github.com/abhirockzz/just-enough-go)

# 等待组

如果你的程序需要等待一堆 Goroutines 完成，使用`WaitGroup`进行协调。它类似于 Java 中的一个`CountDownLatch`。让我们看一个例子。

我们希望并行打印主目录中的所有文件。使用`WaitGroup`指定等待的任务/go routine 的数量。

在这种情况下，它与主目录中的文件/目录数量相同。我们使用`Wait()`来阻塞，直到`WaitGroup`计数器变为零。

```
...
func main() {
    homeDir, err := os.UserHomeDir()
    if err != nil {
        panic(err)
    }
    filesInHomeDir, err := ioutil.ReadDir(homeDir)
    if err != nil {
        panic(err)
    }
    var wg sync.WaitGroup
    wg.Add(len(filesInHomeDir))
    for _, file := range filesInHomeDir {
        go func(f os.FileInfo) {
            defer wg.Done()
        }(file)
    }
    wg.Wait()
}
...
```

要运行该程序:

```
curl https://raw.githubusercontent.com/abhirockzz/just-enough-go/master/sync/wait-group-example.go -o wait-group-example.go
go run wait-group-example.go
```

我们在用户主目录中找到的每一个`os.FileInfo`都会产生一个 Goroutine，一旦我们打印出它的名字，计数器就会使用`Done`递减。程序在主目录的所有内容被覆盖后退出。

# 互斥（体）…

一个`Mutex`是一个共享锁，你可以用它来提供对你的代码的某些部分的独占访问。在这个简单的例子中，我们有一个在`incr`函数中使用的共享/全局变量`accessCount`。

```
func incr() {
    mu.Lock()
    defer mu.Unlock()
    accessCount = accessCount + 1
}
```

请注意，`incr`功能受到了`Mutex`的保护。因此，一次只有一个 Goroutine 可以访问它。我们向它扔了多枚手榴弹。

```
loop := 500
for i := 1; i <= loop; i++ {
        go func(c int) {
            wg.Add(1)
            defer wg.Done()
            incr()
        }(i)
}
```

如果你运行这个，你将总是得到相同的结果，即`Final = 500`(因为 for 循环运行 500 次迭代)。要运行该程序:

```
curl https://raw.githubusercontent.com/abhirockzz/just-enough-go/master/sync/mutex-example.go -o mutex-example.go
go run mutex-example.go
```

在`incr`功能中注释(或删除)以下行，并在本地机器上运行程序，然后再次运行程序:

```
mu.Lock()
defer mu.Unlock()
```

您会注意到可变的结果，例如`Final = 474`。

我鼓励你仔细阅读`[RWMutex](https://golang.org/pkg/sync/#RWMutex)`。这是一种特殊的锁，可用于允许并发读取，但允许同步(单个写入方)写入。

# 一次

它允许你定义一个在你的程序生命周期中只执行一次的任务。

这对于类似`Singleton`的行为非常有用。它有一个单独的`Do`函数，让您传递另一个您只打算执行一次的函数。让我们看一个例子。

假设您正在使用 Go `net/http`包构建一个 REST API，并且您希望只有在调用 HTTP 处理程序时才执行一段代码(例如，获取一个 DB 连接)。

您可以用`once.Do`包装该代码，并确信它只会在第一次调用处理程序时运行。

下面是一个我们只想执行一次的函数:

```
func oneTimeOp() {
    fmt.Println("one time op start")
    time.Sleep(3 * time.Second)
    fmt.Println("one time op started")
}
```

这就是我们在 HTTP 处理程序中所做的——注意`once.Do(oneTimeOp)`。

```
func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Println("http handler start")
        once.Do(oneTimeOp)
        fmt.Println("http handler end")
        w.Write([]byte("done!"))
    })
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

运行代码并访问 REST 端点。

```
curl https://raw.githubusercontent.com/abhirockzz/just-enough-go/master/sync/once-example.go -o once-example.go
go run once-example.go
```

从不同的终端:

```
curl localhost:8080
//output - done!
```

当您第一次访问它时，返回速度会有点慢，您会在服务器中看到以下日志:

```
http handler start
one time op start
one time op end
http handler end
```

如果再次运行(任意次数)，功能`oneTimeOp`将不会被执行。检查日志以确认。

这一块就这么多了。我将非常乐意接受您希望我涵盖的特定 Go 主题的建议。
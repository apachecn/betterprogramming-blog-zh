# Golang 基础:用 Goroutines 加速你的程序

> 原文：<https://betterprogramming.pub/golang-basics-speed-up-your-program-with-goroutines-4ce84945aba8>

## 通过同时运行多个任务来提升你的 Golang 程序

![](img/0423076bd75a5a89dc4492108f412a3d.png)

Adi Goldstein 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的原始照片。

顺序程序一次只运行一个任务。并发程序可以同时运行多个任务，从而提高程序速度。

在 Golang 中，并发是通过 Goroutines 实现的。

Goroutines 允许您将顺序程序转换为并发程序，而不必担心线程问题。

# 什么是 Goroutines？

在 Golang 中，Goroutines 用于实现并发。它们是由 Go 运行时管理的轻量级线程。

Goroutines 使得创建比常规顺序程序更快执行任务的并发程序成为可能。

创建一个 Goroutine 的成本非常小。一个 Go 应用程序通常会同时运行数千个 Goroutines。

# 如何创建 Goroutine

让我们从创建一个不利用并发的程序开始，然后把它变成一个并发程序。

在这个例子中，您创建了一个`count`函数，它将数字计数到一个值。让我们想象一下，这个过程消耗了大量的资源，每个数字需要一秒钟来计算。这可以通过使用具有`Sleep`功能的`time`模块来模拟。

导入`time`模块使用`Sleep`功能延迟程序:

```
**import** (
    "fmt"
    "time"
)
```

然后创建一个`count`函数，用一秒钟来计算每个数字:

现在，调用这个函数:

```
**func** main() {
    count(5)
    count(5)
}
```

结果，从`1`到`5`的数字被打印到控制台两次，正如预期的那样。

运行该程序总共需要 2 x 5 = 10 秒才能完成。

现在，让我们通过让计数同时运行来加快速度。

您需要做的就是为第一个调用创建一个 Goroutine，使它与第二个调用同时运行。幸运的是，这非常简单。只需使用`go`关键字:

```
**func** main() {
    **go** count(5)
    count(5)
}
```

如果您现在运行这个程序，您可以看到这些数字是同时计数的，执行时间是没有 Goroutines 时的一半。

请随意使用 Goroutines。例如，如果您想同时从`1`到`5`计数五次，您可以使用 Goroutine 在五秒钟内完成:

Golang 的 Goroutines 基础到此为止。

# 结论

在一些程序中，可以利用并发性同时执行多个任务。这可以大大加快程序的速度。

在 Golang 中，并发是通过使用关键字`**go**`来实现的。Goroutine 很容易让你把一个常规的顺序程序变成一个并发程序。

例如，此程序同时从 1 到 5 计数三次:

```
**func** main() {
    **go** count(5)
    **go** count(5)
    count(5)
}
```

感谢阅读！我希望你喜欢这篇文章。

# 资源

[](https://golang.org/doc/) [## 证明文件

### Go 编程语言是一个开源项目，旨在提高程序员的工作效率。围棋富有表现力，简洁…

golang.org](https://golang.org/doc/) 

# 你可能会感兴趣

[](https://medium.com/codex/go-in-one-go-39a28333cd5) [## 一气呵成

### 快速浏览 Google Go 编程语言的基础知识。

medium.com](https://medium.com/codex/go-in-one-go-39a28333cd5)
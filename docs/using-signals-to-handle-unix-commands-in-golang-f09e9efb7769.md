# 在 Golang 中使用信号处理 Unix 命令

> 原文：<https://betterprogramming.pub/using-signals-to-handle-unix-commands-in-golang-f09e9efb7769>

## 学习使用内置的 Go 特性来处理 Unix 命令

![](img/f8ceecdd9ba74a7aa5303203b95caf32.png)

[哈沙尔·德赛](https://unsplash.com/@harshaldesai?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/signal?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

接受和处理来自操作系统的信号对于应用程序中的各种用例都很重要。

虽然许多服务器端语言有复杂或乏味的方法来处理来自操作系统的信号，但 Golang 应用程序非常直观。Golang 的内置操作系统包提供了一种简单的方法来集成和响应来自 Go 应用程序的 Unix 信号。让我们看看怎么做。

# 前提

假设我们想要构建一个 Golang 应用程序，当被请求关闭时，它会打印一条消息说，“感谢您使用 Golang。”让我们设置`main`函数，它基本上一直做一些工作，直到向应用程序提供一个退出命令。

```
func main() {
   for {
      fmt.Println("Doing Work")
      time.Sleep(1 * time.*Second*)
   }
}
```

当您运行这个应用程序并通过从您的操作系统提供一个 kill 信号(大多数情况下是`Ctrl + C`或`Ctrl + Z`)来终止它时，您可能会看到类似下面的输出:

```
Doing Work
Doing Work
Doing WorkProcess finished with exit code 2
```

现在，我们将在 Golang 应用程序中解释这个 kill 信号，并处理它以打印出所需的退出消息。

# 接收信号

我们将创建一个`channel`来接收来自操作系统的命令。操作系统包提供了处理信号的`Signal`接口，并有特定于操作系统的实现。

```
killSignal := make(chan os.Signal, 1)
```

为了通知`killSignal`，我们使用`signal`包提供的`Notify`函数。第一个参数接受一个`os.Signal`的通道，而接下来的参数接受一个我们想要通知通道的 OS 信号列表。

```
signal.Notify(killSignal, os.Interrupt)
```

或者，我们可以使用`syscall`包用特定的命令通知我们的信号。

```
signal.Notify(killSignal, syscall.*SIGINT*, syscall.*SIGTERM*)
```

为了处理信号，我们将使我们的`main`功能块使用`killSignal`通道等待`interrupt`信号。在收到来自操作系统的命令时，我们将打印退出消息并终止应用程序。

为了处理我们的工作循环，让我们使用匿名函数将它移到一个单独的`goroutine`中。

```
go func() {
   for {
      fmt.Println("Doing Work")
      time.Sleep(1 * time.*Second*)
   }
}()
```

当工作功能在单独的程序中运行时，`main`功能将等待`killSignal`并在退出前打印退出信息。

```
<-killSignal
fmt.Println("Thanks for using Golang!")
```

# 代码

将所有组件放在一起，最终代码是这样的:

在运行时，它继续执行工作循环，并在收到来自操作系统的`interrupt`信号时，打印所需的消息，然后退出。

```
Doing Work
Doing Work
Doing Work
Thanks for using Golang!
```

# 结论

这个简单的例子可以推广到处理许多现实生活中的场景，比如优雅地关闭服务器和在命令行应用程序中接收命令。
# Golang 并发:Goroutines

> 原文：<https://betterprogramming.pub/golang-concurrency-goroutine-46f0009bcd7c>

## 用并发的小程序构建更大的程序

![](img/677b818503c2691d8f6792adb56b1e42.png)

丹尼尔·麦卡洛在 [Unsplash](https://unsplash.com/s/photos/parallel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

通常，大型程序是由许多其他较小的子程序组成的。例如，web 服务器处理来自 web 浏览器的请求并提供 HTML 网页作为响应，而 API 服务器同时处理请求并返回响应。每个请求都像小程序一样被处理。

理想情况下，像这样的程序同时运行这些小组件。这个同时在多个任务上取得进展的过程被称为*并发*。Go 使用 goroutines 和通道对并发性提供了丰富的支持。

goroutine 基本上是一个能够与其他函数同时运行的函数。我们使用关键字`go`后跟一个函数调用来创建一个 goroutine，例如`doSomething(0)`:

```
package main

import "fmt"

func doSomething(n int) {
  for i := 0; i < 10; i++ {
    fmt.Println(n, ":", i)
  }
}

func main() {
  go doSomething(0)
  var input string
  fmt.Scanln(&input)
}
```

这个程序由两个程序组成。第一个 goroutine 是隐式的，它本身就是主函数。第二个 goroutine 是在我们调用`go doSomething(0)`时创建的。通常当我们调用一个函数时，我们的程序会执行函数中的所有语句，然后返回到调用后的下一行。

使用 goroutine，我们立即返回到下一行，不要等待函数完成。这就是为什么包含了对`Scanln`函数的调用；如果没有它，程序将在有机会打印所有数字之前退出。

Goroutines 是轻量级的，我们可以轻松地创建数千个。我们可以这样修改我们的程序来运行十个 goroutines:

```
func main() {
  for i := 0; i < 10; i++ {
    go doSomething(i)
  }
  var input string
  fmt.Scanln(&input)
}
```

您可能已经注意到，当您运行这个程序时，它似乎是按顺序运行 goroutines，而不是同时运行。让我们使用`time.Sleep`和`rand.Intn`给函数添加一些延迟:

```
package mainimport (
  "fmt"
  "time"
  "math/rand"
)func doSomething(n int) {
  for i := 0; i < 10; i++ {
    fmt.Println(n, ":", i)
    amt := time.Duration(rand.Intn(250))
    time.Sleep(time.Millisecond * amt)
  }
}func main() {
  for i := 0; i < 10; i++ {
    go doSomething(i)
  }
  var input string
  fmt.Scanln(&input)
}
```

`doSomething`打印从 0 到 10 的数字，每打印一个数字后等待 0 到 250 毫秒。goroutines 现在应该同时运行。

# 频道

通道允许两个 goroutines 之间和内部的通信，并同步它们的执行。下面是一个使用通道的示例程序:

```
package mainimport (
  "fmt"
  "time"
)func pinger(c chan string) {
  for i := 0; ; i++ {
    c <- "ping"
  }
}func printer(c chan string) {
  for {
    msg := <- c
    fmt.Println(msg)
    time.Sleep(time.Second * 1)
  }
}func main() {
  var c chan string = make(chan string) go pinger(c)
  go printer(c) var input string
  fmt.Scanln(&input)
}
```

这个程序将永远打印“ping”(按回车键停止)。通道类型用关键字`chan`表示，后跟在通道上传递的事物的类型(在本例中，我们传递的是字符串)。

`<-`(左箭头)操作符用于在通道上发送和接收消息。`c <- "ping"`意为派遣`"ping"`。`msg := <- c`表示接收一条信息并存储在`msg`中。`fmt`行也可以写成这样:`fmt.Println(<-c)`，在这种情况下，我们可以删除前一行。

使用这样的通道可以同步两个 goroutines。当`pinger`试图在信道上发送消息时，它将等待，直到`printer`准备好接收消息。(这就是所谓的阻塞)

让我们在程序中添加另一个发送者，看看会发生什么。添加此功能:

```
func ponger(c chan string) {
  for i := 0; ; i++ {
    c <- "pong"
  }
}
```

并修改`main`:

```
func main() {
  var c chan string = make(chan string) go pinger(c)
  go ponger(c)
  go printer(c) var input string
  fmt.Scanln(&input)
}
```

程序现在将轮流打印“ping”和“pong”。

# 渠道方向

我们可以在一个通道类型上指定一个方向，从而将它限制为发送或接收。例如，pinger 的函数签名可以更改为:

```
func pinger(c chan<- string)
```

现在`c`只能送到。试图从`c`接收将导致编译器错误。类似地，我们可以将打印机更改为:

```
func printer(c <-chan string)
```

没有这些限制的信道被称为双向信道。双向通道可以传递给只接受发送通道或只接受接收通道的函数，但反之则不然。

# 挑选

Go 有一个名为`select`的特殊语句，其工作方式类似于`switch`，但是对于通道:

```
func main() {
  c1 := make(chan string)
  c2 := make(chan string) go func() {
    for {
      c1 <- "from 1"
      time.Sleep(time.Second * 2)
    }
  }() go func() {
    for {
      c2 <- "from 2"
      time.Sleep(time.Second * 3)
    }
  }() go func() {
    for {
      select {
      case msg1 := <- c1:
        fmt.Println(msg1)
      case msg2 := <- c2:
        fmt.Println(msg2)
      }
    }
  }() var input string
  fmt.Scanln(&input)
}
```

这个程序每两秒钟打印一次“从 1 开始”，每三秒钟打印一次“从 2 开始”。`select`选择第一个准备好的通道，并从该通道接收(或向其发送)。如果不止一个通道准备好了，那么它会随机选择从哪个通道接收。如果没有准备好的通道，语句将阻塞，直到有一个通道可用。

`select`语句通常用于实现超时:

```
select {
case msg1 := <- c1:
  fmt.Println("Message 1", msg1)
case msg2 := <- c2:
  fmt.Println("Message 2", msg2)
case <- time.After(time.Second):
  fmt.Println("timeout")
}
```

`time.After`创建一个频道，并在给定的持续时间后发送当前时间。(我们对时间不感兴趣，所以没有存储在变量中。)我们还可以指定一个`default`案例:

```
select {
case msg1 := <- c1:
  fmt.Println("Message 1", msg1)
case msg2 := <- c2:
  fmt.Println("Message 2", msg2)
case <- time.After(time.Second):
  fmt.Println("timeout")
default:
  fmt.Println("nothing ready")
}
```

如果没有通道准备好，默认情况会立即发生。

# 缓冲通道

创建通道时，也可以向`make`函数传递第二个参数:

```
c := make(chan int, 1)
```

这将创建一个容量为 1 的缓冲通道。通常通道是同步的；通道两边都要等到对方准备好了。缓冲通道是异步的；除非通道已满，否则发送或接收消息不会等待。
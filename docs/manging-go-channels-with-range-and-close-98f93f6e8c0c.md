# 如何管理有范围和关闭的 Go 频道

> 原文：<https://betterprogramming.pub/manging-go-channels-with-range-and-close-98f93f6e8c0c>

## 从通道读取数据，然后关闭通道

![](img/c7bb9dfa529cfbc87fec6f579a23fbb4.png)

照片由[在](https://unsplash.com/@drew_beamer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/s/photos/numbers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上绘制光束

欢迎参加“[恰到好处”系列](https://medium.com/@abhishek1987/just-enough-go-blog-series-c1cd62b04beb)的另一部分，其中我们将介绍如何使用`range`从通道读取数据，以及如何使用`close`关闭通道。

 [## “适可而止”——博客系列

### 欢迎光临！👋👋

medium.com](https://medium.com/@abhishek1987/just-enough-go-blog-series-c1cd62b04beb) 

要运行不同的案例/示例，请使用 GitHub 上的[代码。](https://github.com/abhirockzz/just-enough-go/blob/master/channels-range-close/channels-range-close.go)

您可以使用`<-`(例如`<-myChannel`)从通道接受值。

# 简单场景

```
func f1() {
	c := make(chan int) //producer
	go func() {
	 for i := 1; i <= 5; i++ {
	  c <- i
	  time.Sleep(1 * time.Second)
	 }
	}() //consumer
	go func() {
	 for x := 1; x <= 5; x++ {
	  i := <-c
          fmt.Println("i =", i)
         }
         fmt.Println("press ctrl+c to exit")
        }() e := make(chan os.Signal)
	signal.Notify(e, syscall.SIGINT, syscall.SIGTERM)
	<-e
}
```

生产者 goroutine 发送五个整数，消费者 goroutine 接受它们。交换的记录数量(本例中为 5 个)是固定/已知的，这意味着这是一个理想的场景。消费者确切知道何时结束/退出

如果你运行这个:接收器将打印 1-5，要求你退出。

要运行，只需取消`main`中的`f1()`的注释，并使用`go run channels-range-close.go`运行程序。

```
i = 1
i = 2
i = 3
i = 4
i = 5
consumer finished. press ctrl+c to exit
producer finished
^C
```

# Goroutine 泄漏

这是一个过于简单化的案例。让我们通过移除`for`循环计数器并将其转换为`infinite`循环来做一个小小的改变。这是为了模拟一个场景，其中接收者希望获得生产者发送的所有值，但不知道具体细节，即，将发送多少个值(在实际应用程序中，通常是这种情况)。

```
//consumer
go func() {
  for {
   i := <-c
   fmt.Println("i =", i)
  }
  fmt.Println("consumer finished. press ctrl+c to exit")
 }()
```

修改后的程序的输出是:

要运行，只需取消`main`中的`f2()`注释，并使用`go run channels-range-close.go`运行程序。

```
i = 1
i = 2
i = 3
i = 4
i = 5
producer finished
```

注意制作人 goroutine 退出了，但是您没有看到`consumer finished. press ctrl+c to exit`消息。一旦生产者发送完五个整数，并且消费者收到了所有的整数，它就停留在那里等待下一个值，`i := <-c`，并且不能返回/退出。

在长时间运行的程序中，这会导致一个`goroutine leak`。

# `'range’`和`close’`来救援

这就是`range`和`close`可以帮忙的地方:

*   `range`提供了一种迭代通道值的方法(就像对切片一样)
*   `close`可以向某个频道的用户发出信号，表明该频道上不会发送任何其他内容

让我们重构程序。首先，改变消费者使用`range`。取下`i := <-c`钻头，换上`for i := range c`。

```
go func() {
  for i := range c {
   fmt.Println("i =", i)
  }
  fmt.Println("consumer finished. press ctrl+c to exit")
}()
```

更新生成器 goroutine 以在`for`循环外添加`close(c)`。这将确保消费者 goroutine 得到信号，即不再有来自通道的信号，并且`range`循环将终止。

```
go func() {
   for i := 1; i <= 5; i++ {
    c <- i
    time.Sleep(1 * time.Second)
 }
 close(c)
 fmt.Println("producer finished")
}()
```

如果您现在运行该程序，您应该会看到以下输出:

要运行，只需取消`main`中的`f3()`注释，并使用`go run channels-range-close.go`运行程序。

```
i = 1
i = 2
i = 3
i = 4
i = 5
producer finished
consumer finished. press ctrl+c to exit
```

# 奖金

消费者 goroutine 不必与生产者 goroutine 共存来接收值，即，即使生产者 goroutine 完成(并关闭通道)，消费者 goroutine `range`循环也将接收所有值。当消费者按顺序处理记录时，这很有帮助。

我们可以通过使用以下组合来模拟这一场景:

*   生成器中的缓冲通道
*   通过添加一个`time.Sleep()`来延迟消费者出行

在生成器中，我们创建一个容量为 5 的缓冲通道`c := make(chan int, 5)`。这是为了确保生产者 goroutine 不会在消费者不在时阻塞:

```
c := make(chan int, 5)

//producer
go func() {
  for i := 1; i <= 5; i++ {
  c <- i
 }
 close(c)
 fmt.Println("producer finished")
}()
```

消费者保持不变，除了`time.Sleep(5 * time.Second)`，它允许生产者 goroutine 在消费者可以开始之前退出。

```
go func() {
	time.Sleep(5 * time.Second)
        fmt.Println("consumer started")
	for i := range c {
	  fmt.Println("i =", i)
	} fmt.Println("consumer finished. press ctrl+c to exit")
}()
```

下面是您应该看到的输出:

要运行，只需取消`main`中的`f4()`注释，并使用`go run channels-range-close.go`运行程序。

```
producer finished
consumer started
i = 1
i = 2
i = 3
i = 4
i = 5
consumer finished. press ctrl+c to exit
```

制作人 goroutine 发完了五张唱片。消费者过了一会儿醒来，接收并打印出生产者发送的所有 e 5 消息。

# 结论

目前就这些。请继续关注本系列即将发布的文章！
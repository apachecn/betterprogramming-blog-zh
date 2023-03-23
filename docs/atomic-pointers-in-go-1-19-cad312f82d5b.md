# Go 1.19 中的原子指针

> 原文：<https://betterprogramming.pub/atomic-pointers-in-go-1-19-cad312f82d5b>

## 轻松管理共享资源

![](img/7c27bdd62c8bd31767b22fbd3157b18a.png)

“原子的”，在计算机编程中，指的是一次执行一个操作。Objective-C 具有原子属性，它确保从不同的线程安全地读写属性。

在 Objective-C 中，它用于不可变类型。这是因为不可变类型实际上是为了改变它而“重新创建”的。换句话说，在代码中更改不可变类型不会导致编译器抛出错误。

然而，当你这样做时，它将实例化一个新的对象。一个例子是 Go 的`append`函数，它在数组容量满的时候生成一个新的数组。在 Obj-C 中，原子属性将确保操作一个接一个地执行，以防止线程同时访问一个内存地址。因为 Go 是多线程的，所以它也支持原子操作。

Go 1.19 引入了新的原子类型。我最喜欢的附加功能是`atomic.Pointer`，它为`atomic.Value`提供了一个圆滑的替代方案。这也很好地展示了泛型是如何增强开发人员体验的。

# 原子。指针

`atomic.Pointer`是通用类型。与`Value`不同，它不需要声明你的存储值来访问它。下面是定义和存储指针的代码块:

```
package mainimport (
 "fmt"
 "net"
 "sync/atomic"
)type **ServerConn** struct {
 Connection net.Conn
 ID string
 Open bool
}func main() {
 p := atomic.Pointer[**ServerConn**]{} s := ServerConn{ ID : "first_conn"}
 p.**Store**( &s ) fmt.Println(p.Load()) // Will display value stored.
}
```

我将变量`p`实例化为一个结构文字。然后，我继续将变量`s`的指针存储在`p`中，`s`表示一个服务器连接。瞧，我们已经向原子性迈出了第一步。

通过将变量存储为原子值，我们将确保不会同时访问内存地址。例如，如果一个程序同时被读取和写入，映射将导致它死机。锁是防止这些混乱发生的好方法，原子操作也是如此。

# 我的原子指针用例

为了构建前面提供的代码块，我将使用一个`atomic.Pointer`每 13 秒重新创建一个数据库连接。我将从编写一个用于每 10 秒记录一次连接 ID 的函数开始。

我的机制是查看新的连接对象是否已经被传播。然后我将定义一个内联函数来每 13 秒改变一次连接。下面是代码的样子:

```
...func ShowConnection(p * **atomic.Pointer**[ServerConn]){for {
  time.Sleep(10 * time.Second)
  fmt.Println(p, **p.Load()**)
 }

}func main() { c := make(chan bool)
 p := **atomic.Pointer**[ServerConn]{}
 s := ServerConn{ ID : "first_conn"}
 p.Store( &s ) go ShowConnection(&p) go func(){
   for {
    time.Sleep(13 * time.Second)
    newConn := ServerConn{ ID : "new_conn"}
    p.**Swap**(&newConn)
   }
  }() <- c
}
```

`ShowConnection`作为 Goroutine 调用。内联函数将实例化一个`ServerConn`对象，并与当前的连接对象交换。这仅用指针是可能的，然而，它需要实现一个“锁定-解锁”系统。

原子包对此进行了抽象，并确保每次加载和保存都一个接一个地进行。这是一个简单的例子，也是一个不常见的用例。另外，`atomic.Pointer`的使用可能是一种“过度工程化”的情况，因为我的程序的 Goroutines 在不同的时间间隔运行。我将使用 Go 的`race`标志来查看我的程序的 Goroutines 是否同时访问同一个内存地址。我还将重写上面的代码，使用指针代替`atomic.Pointer`。

# 数据竞赛

“当两个 goroutines 同时访问同一个变量，并且其中至少有一个访问是写操作时，就会发生数据竞争。”为了快速验证数据竞争，您可以执行带有标志`race`的`go run`来执行测试。为了演示原子类型如何防止这种情况，我重写了上面的例子，使用经典的 Go 指针。下面是代码的样子:

```
package mainimport (
 "fmt"
 "net"
 "time"
)type ServerConn struct {
 Connection net.Conn
 ID string
 Open bool
}func ShowConnection(p * **ServerConn**){for {
  time.Sleep(10 * time.Second)
  fmt.Println(p, *p)
 }

}func main() { c := make(chan bool)
 p :=  ServerConn{ ID : "first_conn"} go ShowConnection(**&p**) go func(){
   for {
    time.Sleep(13 * time.Second)
    newConn := ServerConn{ ID : "new_conn"}
    **p = newConn**
   }
 }() <- c
}
```

检查数据竞争后，终端上的输出如下:

```
cheikh@cheikh-s5-1110:~/go/src/atomic$ go run -race main_classic.go 
&{<nil> first_conn false} {<nil> first_conn false}
==================
WARNING: DATA RACE
Write at 0x00c000074570 by goroutine 8:
  main.main.func1()
      /home/cheikh/go/src/atomic/main_classic.go:37 +0x6fPrevious read at 0x00c000074570 by goroutine 7:
  runtime.convT()
      /usr/lib/go-1.18/src/runtime/iface.go:321 +0x0
  main.ShowConnection()
      /home/cheikh/go/src/atomic/main_classic.go:19 +0x65
  main.main.func2()
      /home/cheikh/go/src/atomic/main_classic.go:30 +0x39Goroutine 8 (running) created at:
  main.main()
      /home/cheikh/go/src/atomic/main_classic.go:33 +0x16eGoroutine 7 (running) created at:
  main.main()
      /home/cheikh/go/src/atomic/main_classic.go:30 +0x104
==================
&{<nil> new_conn false} {<nil> new_conn false}
&{<nil> new_conn false} {<nil> new_conn false}
&{<nil> new_conn false} {<nil> new_conn false}
```

尽管这两个函数以不同的时间间隔运行，但它们会在某个点发生冲突。具有原子指针的代码没有返回关于数据竞争的反馈。这是一个原子指针在多线程环境中表现更好的例子。

# 结论

Go 原子类型是管理共享资源的一种简单方法。它消除了不断实现互斥体来控制资源访问的需要。

这并不意味着互斥体已经过时，因为在某些操作中仍然需要它们。

总之，`atomic.Pointer`是将原子内存原语引入程序的好方法。这是一种简单的方法来防止数据竞争，而不需要花哨的互斥代码。下面是这篇文章中使用的代码的链接。

[](https://go.dev/doc/articles/race_detector) [## 数据竞争检测器——Go 编程语言

### 数据竞争是并发系统中最常见也是最难调试的错误类型之一。当…时，会发生数据竞争

go.dev](https://go.dev/doc/articles/race_detector) [](https://github.com/cheikhshift/medium_examples/tree/main/atomic) [## medium _ examples/原子 at main cheikh shift/medium _ examples

### 中型文章的代码示例。在 GitHub 上创建一个帐户，为 cheikhshift/medium_examples 开发做贡献。

github.com](https://github.com/cheikhshift/medium_examples/tree/main/atomic)
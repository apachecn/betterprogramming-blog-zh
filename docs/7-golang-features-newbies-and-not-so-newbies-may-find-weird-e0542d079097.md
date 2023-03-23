# 你可能会觉得奇怪的 7 个 Golang 特性

> 原文：<https://betterprogramming.pub/7-golang-features-newbies-and-not-so-newbies-may-find-weird-e0542d079097>

## 围棋写作三年回顾

![](img/6041063f1ba13291cc4e532fb4193d7e.png)

[胡安·戈麦斯](https://unsplash.com/@nosoylasonia?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/keyboard?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

人类天生就有偏见。当我们开始用一种我们觉得不舒服的编程语言编码时，我们总是指出最让我们恼火的特性。

有时很难将这些特性放到相应的环境中，也很难理解设计它们的原因。这并不一定意味着这些功能是错误的或设计不正确的。它们是学习过程的一部分，并逐渐融入我们的编程技能。

在本文中，我将列举我在学习 Golang 时发现的一些最麻烦/怪异的特性(在本例中是七个)。

这是一篇个人的完全主观的文章。下面的列表只是我选择的一小部分，没有任何特别的标准。给读者一些背景，我有很多语言的经验:C、C++、Java、Scala、Python、R(如果我们可以把 R 看作一种语言的话)等等——并且有将近 20 年的编写代码的经验。

总的来说，我发现 Go 是一种学习曲线平滑的语言。这可能是因为它有一个明确定义的目的，消除了一些隐含更复杂语法的特性。无论如何，这是我的清单。

# **1。不必要的导入和变量**

## **Go 强制代码最小化**

这意味着未使用的导入和变量会触发编译错误。例如:

```
import (
    "fmt"
    "os" //not used
)func main() {
    fmt.Println("Hola")
}
```

编译器返回:

```
imported and not used: "os"
```

# **2。迭代集合**

## **用于迭代集合的 range 函数返回两个值**

第一个是条目在集合中的位置。第二个值包含条目值本身。

```
x := [4]string{"one","two","three","four"}
for i, entry := range(x) {
   fmt.Printf("Element at position %d is %s\n", i, entry)
}
```

这非常方便，因为在每次迭代中，您都有两个最常用的值可以在循环中使用。然而，它们并不总是必需的。你可能会这样做:

```
x := [4]string{"one","two","three","four"}
for i, entry := range(x) {
  fmt.Printf("Element %s\n", entry)
}
```

这将在编译期间返回一个错误:

```
i declared but not used
```

或者更糟，你会跳过`i`变量，就像这样:

```
x := [4]string{"one","two","three","four"}
for entry := range(x) {
   fmt.Printf("Element %s\n", entry)
}
```

这可能会引起混淆，因为它返回了一个变量中的位置值，该值应该是入口值。

```
Element %!s(int=0)
Element %!s(int=1)
Element %!s(int=2)
Element %!s(int=3)
```

我们只需指出一个未使用的变量`i`。

```
x := [4]string{"one","two","three","four"}
    for _, entry := range(x) {
       fmt.Printf("Element %s\n", entry)
    }
```

# **3。属性可见性**

## **如果属性以大写字母**开头，则属性可见

如果不是，它们是私有的。很简单。然而，我经常忘记这一点，导致愚蠢的错误。

```
type Message struct {
 Text string // This is public
 text string // This is private
}
```

# **4。重载方法怎么了？**

## 没有任何重载方法

如果您来自 Java 世界，您可能习惯于重载方法。对于重载方法，在一个方法中，我们可以有几个签名。嗯… Golang 没有方法重载。

# **5。遗产怎么了？**

## **没有继承**

仅此而已。你可以做一些变通，像这里描述的[这里的](https://golangbot.com/inheritance/)，但我不能说这真的是继承。

# **6。接口呢？**

## **有接口**

它们可以被定义为方法签名的集合。然而，它们很奇怪，因为你在其他语言中使用它们。

为什么？因为你没有以编程的方式指明你的结构实现了一个接口(类似于类`A`实现了接口`I`)。如果您的结构具有接口枚举的方法，则它会实现该接口。举个例子比较好理解。

```
package mainimport (
    "fmt"
)type Speaker interface {
    SayYourName() string
    SayHello(b Speaker) string
}type HappySpeaker struct {}func(hs HappySpeaker) SayYourName() string {
    return "Happy"
}func(hs HappySpeaker) SayHello(b Speaker) string {
    return fmt.Sprintf("Hello %s!",b.SayYourName())
}type AngrySpeaker struct {}func(as AngrySpeaker) SayYourName() string {
    return "Angry"
}func(as AngrySpeaker) SayHello(b Speaker) string {
    return fmt.Sprintf("I'm not going to say hello to %s!",b.SayYourName())
}func main() {
    // We have two different structs
    happy := HappySpeaker{}
    angry := AngrySpeaker{}
    // they can say their names
    fmt.Println(happy.SayYourName())
    fmt.Println(angry.SayYourName()) // But they are also speakers
    fmt.Println(happy.SayHello(angry))
    fmt.Println(angry.SayHello(happy)) // This is also valid
    var mrSpeaker Speaker = happy
    fmt.Println(mrSpeaker.SayHello(angry))
}
```

可以想象，这在编码时会产生影响。Go 中的接口是一个更深入的讨论主题，您可以找到许多讨论其利弊的例子。

# **7。构造函数呢？**

## **当实例化一个新的结构时，可以跳过属性集**

在任何面向对象的语言中，你都找不到类似的构造函数。结构定义与 C 中使用的结构定义非常相似。有一个潜在的问题:在实例化一个新的结构时，可以跳过属性集。在下面的代码中，`halfMessage1`和`halfMessage2`有未设置的属性。

```
package mainimport (
    "fmt"
)type Message struct {
    MsgA string
    MsgB string
}func(m Message) SayIt() {
  fmt.Printf("[%s] - [%s]\n",m.MsgA, m.MsgB)
}func main() { fullMessage1 := Message{"hello","bye"}
    fullMessage2 := Message{MsgA: "hello", MsgB: "bye"} halfMessage1 := Message{"hello",""}
    halfMessage2 := Message{MsgA: "hello"} emptyMessage := Message{} fullMessage1.SayIt()
    fullMessage2.SayIt()
    halfMessage1.SayIt()
    halfMessage2.SayIt()    
    emptyMessage.SayIt()        
}
```

输出是:

```
[hello] - [bye]
[hello] - [bye]
[hello] - []
[hello] - []
[] - [] 
```

这总是一个潜在的问题，因为您可能有期望设置值的方法。减轻这种情况的一种方法是定义静态构造函数。

```
package mainimport (
    "fmt"
)type Message struct {
    MsgA string
    MsgB string
}func(m Message) SayIt() {
  fmt.Printf("[%s] - [%s]\n",m.MsgA, m.MsgB)
}func NewMessage(msgA string, msgB string) *Message{
  if len(msgA) * len(msgB) == 0 {
     return nil
  } 
  return &Message{MsgA: msgA, MsgB: msgB}
}func main() {
   // A correct message
   msg1 := NewMessage("hello","bye")    
   if msg1 != nil {
      msg1.SayIt()
   } else {
      fmt.Println("There was an error")
   } // An incorrect message
   msg2 := NewMessage("","") if msg2 != nil {
      msg2.SayIt()
   } else {
      fmt.Println("There was an error")
   }
}
```

# 摘要

这是在 Go 中编码时要考虑的所有潜在因素的一小部分。我想听听你的经历。你觉得最诡异的围棋特点是什么？
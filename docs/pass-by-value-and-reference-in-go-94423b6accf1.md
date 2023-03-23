# Go 中的按值传递和按引用传递

> 原文：<https://betterprogramming.pub/pass-by-value-and-reference-in-go-94423b6accf1>

## 了解两者的区别，这样你就不会犯代价高昂的错误

![](img/f5f9bdac7a2bcfed490cc7523ab9ff6d.png)

照片由 [Unsplash](https://unsplash.com/s/photos/patterns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄

# 介绍

许多编程语言支持通过值和/或引用传递参数。在本文中，我们将学习 Go 的函数如何处理传递的参数。

# 什么是按值传递？

在 Go 中，当一个参数通过值传递给一个函数时，意味着这个参数被复制到内存的另一个位置。当访问或修改函数中的变量时，只会访问或修改副本，而不会修改原始值。Go 中的所有原语/基本类型(int 及其变体、float 及其变体、boolean、string、array 和 struct)都是通过值传递的。按值传递通常是将值传递给函数的方式。让我们看一些例子:

如果您运行上面的示例，您可以确认传递给函数的变量值在函数调用前后保持不变。简而言之，变量是通过值传递的。

现在，让我们探索 Go 函数处理参数的另一种方式——按引用传递。

# 什么是按引用传递？

对于 Go 复合类型是否通过引用传递给函数有一种理解/争论。具体来说，Go 不支持“按引用传递”语义。原因很简单；Go 不像 C++等其他编程语言那样支持引用变量。从概念上讲，在 Map 的情况下，当您创建一种类型的 Map，然后将它传递给一个函数时，如果该函数修改了参数，其效果也会影响原始变量。这看起来好像 Map 变量是通过引用传递的，但这是不正确的。当您使用“make()”创建 Map 类型的变量时，它调用 makemap()，后者返回*hmap(这是一个指针)。因此，将变量传递给函数是一种指针传递，而不是引用传递。同样的概念也适用于渠道。虽然，Slice 的数据结构相对不同(一个 struct 有三种类型；指向底层数组的指针、切片的长度以及切片的容量)。但是，它也被视为传递指针。

在我们继续讨论 Go 函数如何处理复合类型(切片、映射)、通道、指针和函数的一些示例之前，让我们先来看看这个代码片段，它确认 Go 复合类型不是按引用传递的:

```
package mainimport "fmt" func myMap(v map[int]int) {
    v = make(map[int]int) // make() declares and initializes v to 0} func myInt(v []int) {
    v = make([]int) // make() declares and initializes v to 0}func main() {

   //v is declared but NOT initialized, which means its value is nil
     var v map[int]int myMap(v)   
     fmt.Println(v == nil) // true //i is declared but NOT initialized, which means its value is nil
     var i []int myInt(i)
     fmt.Println(i == nil) // true}
```

看上面的例子，我们可以看出，即使在声明了变量“V”之后，调用 myF()将它初始化为 0。最终，当我们在调用后测试它的值时，它的结果是“true”。这意味着 myF()没有将“v”视为按引用传递(因为 Go 不支持这种语义)。如果我们尝试切片和通道，会得到相同的结果。你可以在戴夫·切尼的博客中读到更多关于这个话题的内容。

下面是在 Go 中传递复合类型和其他类型(除了上面讨论的原始类型)的例子:

浏览上面的例子，我们可以看到向函数传递参数的效果。例如，在`slice`中，我们可以确认变量`coffeeBox`的值在传递给函数`modifySlice`时被修改了。`map`、`pointer`、`function`、`channel`也是如此。

如果您发现自己需要修改基本类型(int、float、bool 等)的值，只需将变量的内存地址传递给函数(换句话说，将参数视为指针)。指针部分清楚地说明了这种情况。

# 摘要

Go 支持按值传递语义；当参数通过值传递时，函数接收每个参数的副本，对副本的修改不会影响调用方。另一方面，它不支持按引用传递。但是，它支持按指针传递，可以用来修改底层参数的值。

在这篇短文中，我们探讨了 Go 处理传递给函数的参数的两种方式。了解这个概念很重要，这样可以避免错误的期望。

直到下一篇文章，继续走下去！
# Golang 简化了闭包

> 原文：<https://betterprogramming.pub/closures-made-simple-with-golang-69db3017cd7b>

## 了解如何在 Go 中使用闭包和匿名函数

![](img/a8b8cfa7114904616345451b1875a6f5.png)

[欧文·史密斯](https://unsplash.com/@mr_vero?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

在 Golang 中，闭包是引用其作用域之外的变量的函数。闭包可以比创建它的作用域更长寿。因此，它可以访问该范围内的变量，即使在该范围被销毁之后。

在深入研究闭包之前，您需要理解什么是**匿名函数**。

# Go 中有哪些匿名函数？

匿名函数是没有名字的函数。

例如，让我们创建一个常规函数:

```
**func** send(message string) {
    fmt.Println(message)
}
```

你可以这样称呼它:

```
send("Hi, it's me")
```

输出:

```
Hi, it's me!
```

现在，让我们创建这个函数的匿名版本:

```
**func** (message string) {
    fmt.Println(message)
}("Hi, it's me!")
```

结果:

```
Hi, it's me!
```

该函数类似于常规函数，只是它没有名称。此外，在本例中，您直接在定义后调用函数。

接下来，让我们通过创建一个返回函数的函数来使用匿名函数。

## 返回一个函数的函数

你可能见过返回整数、字符串等的函数。但是你知道函数可以返回函数吗？

例如，让我们创建一个返回匿名函数的函数:

```
**func** give_me_a_func() func(string) {
    **return** func(message string){
        fmt.Println(message)
    }
}
```

*   这个函数的返回类型是`func(string)`，因为您**返回了**一个以字符串作为参数的函数。

现在，我们来玩玩`give_me_a_func()`。

你可以创建一个变量来存储由`give_me_a_func()`返回的函数。然后可以像调用函数一样调用该变量:

```
send_func := give_me_a_func()
send_func("Hi, it's me!")
```

输出:

```
Hi, it's me!
```

现在你知道什么是匿名函数以及它是如何工作的了。你也知道一个函数可以返回另一个函数。

接下来，我们来谈谈 Go 中的闭包。

# Go 中的闭包是什么？

闭包是一个引用自身函数体(作用域)之外的变量的函数。

换句话说，闭包是一个内部函数，它可以访问创建它的作用域中的变量。即使当外部函数完成执行并且作用域被销毁时也是如此。

让我们看看这意味着什么。

让我们创建一个名为`incrementor()`的函数。这个函数返回一个匿名函数。换句话说，它返回一个闭包。这个闭包是一个返回整数的函数:

让我们创建一个变量来存储由`incrementor()`返回的函数。让我们也称它几次:

```
next := incrementor()  // next is a **function** returned by incrementorfmt.Println(next())    // prints 1
fmt.Println(next())    // prints 2
fmt.Println(next())    // prints 3
```

调用`next()`增加在`incrementor`函数中声明的变量`i`。但是它怎么知道`i`是什么？

这是可行的，因为闭包是一个内部函数，它可以访问创建它的作用域中的所有变量。这甚至适用于外部函数完成执行后的**。**

让我们来看看`incrementor()`函数的作用域(花括号内的所有内容):

```
i := 0
**return** **func**() int {
    i++
    return i
}
```

*   当`incrementor()`执行结束时，上述范围被破坏。但是闭包仍然能够访问和更新作用域的变量，即使它已经不存在了。

这就完成了对 Go 中闭包的介绍。请随意使用这些示例，以便更深入地理解正在发生的事情。

# 结论

匿名函数是没有名字的函数。

在 Go 中，函数可以返回其他函数。例如，这可以通过返回匿名函数来实现。

闭包是一个引用自身函数体外部变量的函数值。闭包能够比定义它的函数的作用域更长久。这意味着它可以访问作用域外的变量。

感谢阅读。我希望你觉得这很有用。

# 资源

[](https://golang.org/doc/) [## 证明文件

### Go 编程语言是一个开源项目，旨在提高程序员的工作效率。围棋富有表现力，简洁…

golang.org](https://golang.org/doc/)
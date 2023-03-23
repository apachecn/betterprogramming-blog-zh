# 停止在 Golang 中将常量与类型字符串混合

> 原文：<https://betterprogramming.pub/stop-mixing-constants-with-the-type-string-in-golang-d3589d8ae84d>

## Golang 的无类型常量可能会使您的工作更容易。但是有一个条件

![](img/a260800d310fa2547a0018fadf84aad1.png)

比尔·牛津在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

如果在我使用一个库的时候有一件事让我恼火，不管是什么编程语言，那就是当作者让我不知道应该给函数提供什么参数的时候。当函数期望我提供一个特定的常量时，情况会变得更糟。

Go 是一种强静态类型语言，因此我希望看到一些线索，告诉我哪些常量适合哪个函数，而不必对库进行逆向工程。我的 IDE 应该告诉我应该用什么选项来给函数输入。

这个问题的核心来自这样的代码:

*去游乐场试试:*[*https://play.golang.org/p/EMEvOL-zSVA*](https://play.golang.org/p/EMEvOL-zSVA)

两个非类型化的字符串常量 *(* `FormalGreeting`和`CasualGreeting`)不提供任何关于在哪里使用它们的信息。相反，谁会在不看源代码的情况下猜测常量是作为`sayHello`函数的输入而存在的呢？这个例子可能过于简单了，但是对于许多使用库的开发人员来说，这种常量确实是浪费时间。

当您考虑这个问题并且来自不同的编程语言时，您可能会立即想到枚举作为一种解决方案。Golang 不实现枚举，但提供了更好地管理常数的惯用方法。

所以非类型化的字符串常量是不好的，因为它们会误导人。

```
const FormalGreeting = "Hello, "
```

需要明确的是，这不是一个好的解决方案，因为您无法解决前面提到的任何问题:

```
const FormalGreeting string = "Hello, "
```

在 Go 中，要创建类型化枚举，只需创建一个自定义类型并定义该类型的常量。

```
type GreetingType string
const FormalGreeting GreetingType = "Hello, "
```

这就是我的主张的来源。您总是可以为您的字符串常量创建一个自定义类型，从而提高可用性和可维护性。因此，在 Golang 中，任何常量都不应该是 string 类型，也不应该是非类型化的。

*操场试玩改版:*【https://play.golang.org/p/dauiqCxbAE5】

# *如果我遵循这个建议，我应该期待什么变化？*

*坏消息是类型`GreetingType`的常量不能赋给字符串变量。在极少数情况下，您可能需要将自定义类型转换回字符串。*

```
*var s string
s = string(FormalGreeting)*
```

*相反，您可能需要将一个字符串转换成您的自定义类型(例如在一些测试案例中)。*

```
*var g GreetingType
g = GreetingType("What's up")*
```

# *如果非类型化常量如此有害，那么它们有什么用？*

*我不停地说你不应该使用无类型的常量。我不得不深入挖掘，理解它们为什么存在。以下是我的发现:*

*   *对数值类型非常严格，非类型常量在数值运算时会有所帮助。因此，您可以一劳永逸地定义 PI 值，然后乘以该常数，而不管要乘以的变量是什么类型。*
*   *一般来说，无类型常数是为了更容易处理混合数据类型和表达式。*

# *从那时起，下一个学习目标是什么？*

*我认为这篇文章是介绍在 Go 中编写枚举的惯用方法的好机会。但是，在写的时候，发现了一篇不到一个月前写的文章。*

*[](https://levelup.gitconnected.com/implementing-enums-in-golang-9537c433d6e2) [## 在 Golang 中实现枚举

### 枚举将相关的常数组合在一个类型中。枚举是一个具有广泛用途的强大功能。然而…

levelup.gitconnected.com](https://levelup.gitconnected.com/implementing-enums-in-golang-9537c433d6e2)* 

# *参考*

*   *官方文件:[https://blog.golang.org/constants](https://blog.golang.org/constants)*
*   *关于类型化和非类型化变量的解释:[https://www.callicoder.com/golang-typed-untyped-constants/](https://www.callicoder.com/golang-typed-untyped-constants/)*
*   *类型化与非类型化的比较[https://medium . com/golangspec/untyped-constants-in-go-2c 69 EB 519 b 5b](https://medium.com/golangspec/untyped-constants-in-go-2c69eb519b5b)*
*   *Go 2.0 可能有 enum 支持:[https://github.com/golang/go/issues/19814](https://github.com/golang/go/issues/19814)*
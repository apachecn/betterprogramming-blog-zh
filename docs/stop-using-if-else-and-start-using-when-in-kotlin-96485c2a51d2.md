# 停止使用 If-Else，开始使用 When in Kotlin

> 原文：<https://betterprogramming.pub/stop-using-if-else-and-start-using-when-in-kotlin-96485c2a51d2>

## Kotlin 中的“when”是传统 if-else 的优雅版本。它提供了优于 if-else 的可读性和简洁性

![](img/c02376275b09992ddefc3d32f7b55d72.png)

由 [Camylla Battani](https://unsplash.com/@camylla93?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

通常，我们的应用程序需要根据输入的类型来决定做什么。

这样做的传统方式是使用多个`if else`语句——与我们感兴趣的不同输入类型的数量一样多，加上一个可选的默认情况或`else`。如果您来自 Java，那么您可能已经使用了`switch case`语句来实现这一点。

这样做的“现代”方式就像 Scala 中的`match case`和 Kotlin 中的`when`。与传统的`if else`或`switch case`语句相比，它们的好处是更干净、更简洁、可读性更强。

在这个简短的教程中，我们将学习如何在 Kotlin 中使用`when`语句。

# 基本用途

`when`的基本用途是根据输入值决定如何处理输入。一个典型的例子是一个应用程序，它接受一个数字，并根据这个值对它做一些事情。

这就是在`when`语句中的写法:

```
when {
    number < 0 -> *println*("please enter a positive number")
    number == 0 -> *println*("try any higher number than 0")
    number % 2 == 0 -> *println*("event")
    else -> *println*("it's an odd number")
}
```

很干净，简洁，可读性强，不是吗？

如果将上述语句写成一个`if else`语句，它看起来会像这样:

```
if (number < 0) {
    *println*("please enter a positive number")
} else if (number == 0) {
    *println*("try any higher number than 0")
} else if (number % 2 == 0) {
    *println*("event")
} else {
    *println*("it's an odd number")
}
```

我们是否都同意`when`版本比`if else`优雅得多？

注意:`when`执行第一个匹配的分支(或条件)。所以，条件的顺序很重要。最终，如果没有一个条件匹配，那么它将转到`else`分支并执行它。

# 不管有没有争论

有了 Kotlin 的`when`，我们也可以给它传递一个参数。让我们看一个如何做到这一点的例子:

```
when (statusCode) {
    200 -> println("all good")    
    400 -> *println*("it's a bad request")
    404 -> *println*("that page is not found")
    else -> *println*("something went wrong")
}
```

在这个例子中，Kotlin 知道我们试图将`statusCode`的值与我们在`when`子句的条件中指定的值相匹配。因此，Kotlin 将此解释为`statusCode == 400`，以此类推，直到如果没有匹配到任何内容，则到达`else`条件。

另一件要注意的事情是，我们可以给一个变量赋值，作为`when`的参数。让我们看看这意味着什么:

```
data class Payload(val message: String,
                   val statusCode: Int)

val payload = Payload("hello world", 200)

when (val statusCode = payload.statusCode) {
    200 -> *println*("all good $statusCode")
    400 -> *println*("it's a bad request $statusCode")
    404 -> *println*("that page is not found $statusCode")
    else -> *println*("something went wrong $statusCode")
}
```

当我们在`when`子句中的多个(如果不是全部)条件中使用同一个变量时，这很有用。

# 聪明的演员

在某些情况下，我们的应用程序可能接受不同类型的输入(即`Any`类型的输入),下一个动作完全基于该输入类型。

一个真实的用例是我们的应用程序使用来自 Kafka 主题的消息，该主题有多种事件类型(称为`union`类型)。我们的`when`表达式应该是这样的:

```
when(event) {
    is CustomerSignUp -> println("create a new customer ${event.customerId}")
    is PurchaseIsMade -> println("charge customer account ${event.accountNo}")
    is EmailIsUpdated -> println("send a verification email to ${event.email}")
    else -> println("unrecognised event $event. ignoring...")
}
```

如果我们将鼠标悬停在`when`子句内的事件上，我们的 ide(假设是 IntelliJ)将提示我们智能转换已应用于该事件。

# 多行语句

如果需要，我们可以在`when`子句中编写多行语句。让我们来看一个例子:

```
when(event) {
    is CustomerSignUp -> {
        println("create a new customer ${event.customerId}")
        customerTable.save(event)
    }
    is PurchaseIsMade -> {
        println("charge customer account ${event.accountNo}")
        customerService.charge(event)
    }
    is EmailIsUpdated -> {
        println("send a verification email to ${event.email}")
        customerTable.update(event)
    }
    else -> println("unrecognised event $event. ignoring...")
}
```

这很方便，尤其是当我们需要对正在处理的值做一些事情的时候。

# 作为一种表达

我们可以使用`when`语句作为表达式来给变量赋值。让我们看一个简单的例子。

```
val code: Int = when (command) {
    "FAILED" -> 0
    "SUCCESS" -> 1
    "RETRY" -> 8
    else -> 99
}
```

在上面的表达式中，我们正在检查`command`的值，并基于此，给我们的`code`变量赋值。

# 包裹

`when`语句是 Kotlin 提供的一个很棒的控制流，是传统`if else`语句的一个很好的替代。当我们有很多条件要检查某个值时，值得考虑使用`when`而不是`if else`。

当我们开始用`when`替换长的`if else`语句时，我们的代码可读性肯定会提高。

就是这样！

![](img/7ddbe53159f1493a643746cb7a3d3f6d.png)

照片由[贝琳达·费因斯](https://unsplash.com/@bel2000a?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄
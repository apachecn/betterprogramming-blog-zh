# 科特林的猫王接线员

> 原文：<https://betterprogramming.pub/elvis-operator-in-kotlin-if-null-then-use-this-instead-e4989677a9b1>

## 如果为空，则使用以下代码

![](img/ec15502f9fada59298264ac4aa06eb65.png)

Shane Hauser 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

“我想使用那个值，但是如果它是 null，那么我可以使用这个非 null 值来代替。”

很多时候，我们在编写应用程序时都会面临上述情况。这可能是我们的应用程序代码中最常见的情况之一。

不同的编程语言提供了不同的方法，但是今天，我们来看看 Kotlin 是如何做到的。

向猫王接线员问好！它被表示为`?:`。

# 何时使用 Elvis 操作员

使用 Elvis 操作符的时机是当我们期望一个可空值并且需要处理它以避免著名的`NullPointerException`时。

一些使用案例包括:

*   通过主键从数据库中查询某些东西，可能不会返回任何记录。
*   从一个对象映射到另一个对象，其中一个或多个字段可能为空。

# 如何使用 Elvis 操作员

使用 Elvis 操作符的语法非常简单明了。

```
val result = x ?: y
```

这基本上就是说如果值不是`null`就用`x`，否则就用`y`。Elvis 运算符右侧的表达式或本例中的`y`，仅在`x`为`null`的情况下计算。

`y`可以是一个值，`return`可以是一个表达式，`throw`可以是一个表达式。

我们来看几个例子。

# 查询可能不返回记录的表

下面是一个函数，它基于主键`customerId`对 [DynamoDB](https://aws.amazon.com/dynamodb/) 表执行`GetItem`操作，并返回其项目的地图(如果存在的话)。

然后，我们希望获得客户的电子邮件，如果它不存在，我们希望我们的应用程序抛出一个`Exception`。

这就是我们使用 Elvis 操作符的方式。

```
val email = getCustomerInfo("123")?.get("emailAddress")?.s() ?: throw IllegalArgumentException("email should exist")
```

# 为可空字段提供默认值

在某些情况下，我们需要将一个对象的值赋给另一个对象。假设我们将下面的`dataclass`命名为`Address`。

```
data class Address(val streetNumber: Int, val streetAddress: String, val streetSuffix: String?)
```

我们有一个简单的函数来返回完整的级联地址，如下所示。

```
fun returnFullAddress(address: Address): String {
    val fullAddress = address.streetNumber.toString() + " " + address.streetAddress + " " + (address.streetSuffix ?: "")
    return fullAddress.*trim*()
}
```

然后，我们可以像这样使用它来处理可空的`streetSuffix`字段。

```
val address = Address(12, "John Drive", null)

returnFullAddress(address)// 12 John Drive
```

# 链式猫王操作员

我们也可以锁住猫王操作员。

记住 Elvis 操作符只会在左边的表达式是`null`的时候对右边的表达式求值(或者我们称之为右操作数)。

# 包裹

当您希望得到一个`null`值并希望很好地处理它时，Elvis 操作符非常方便。

希望这篇简短的教程能让您了解何时以及如何使用这个操作符。
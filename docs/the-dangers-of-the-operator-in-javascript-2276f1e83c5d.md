# JavaScript 中'+'操作符的危险

> 原文：<https://betterprogramming.pub/the-dangers-of-the-operator-in-javascript-2276f1e83c5d>

## 简单的加法会以你意想不到的方式破坏 JavaScript 应用程序

![](img/a405ce07f9dbfb3e006d3ab4c38d7e41.png)

凯文·杰瑞特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像大多数编程语言一样，JavaScript 有一个`+`操作符，可以用来执行加法。然而，与其他编程语言不同，运算符还有第二个用途:字符串格式化。

这篇文章探索了`+`操作符是如何工作的，以及如果你不小心的话，它是如何失败并导致错误的。它还显示了如何首先避免这些问题。

# 基础知识

如前所述，`+`操作符在 JavaScript 中有两个用途:

1.  添加数字
2.  串联字符串

`+`运算符的第一个目的是将数字相加。这里有一个例子:

```
const price = 20
const shipping = 3
const tax = 3
const total = price + shipping + tax
console.log(total) // Outputs 26
```

运算符的另一个目的是连接字符串。它可以这样使用:

```
const firstName = 'Marty'
const lastName = 'McFly'
const fullName = firstName + ' ' + lastName 
console.log(fullName) // Outputs 'Marty McFly'
```

这至少目前看来是可行的，但是当你使用字符串形式的数字时会发生什么呢？

```
console.log('2' + '2') // This outputs '22'
```

运算符的行为取决于给定的数据类型。如果它收到两个数，那么它将执行加法。但是，如果您给它一个或多个字符串，行为将变为字符串连接。

```
console.log(2 + '2') // This also outputs '22'!
```

`+`操作符的模糊性会在 JavaScript 应用程序中造成大问题。让我们看看如何。

# 现实世界的例子

上面的例子以最基本的形式展示了这个问题。然而，在现实世界中，问题往往隐藏在复杂性中。这里有一个更现实的情况:

您的公司维护着一个电子商务网站。您的任务是在网站的标题上添加一个小计指示器，这样用户就可以看到他们购物时花了多少钱。

小计是通过对用户购物车中的所有产品求和来计算的。产品数据以 JSON 格式从 REST API 中获取。用户购物车中的产品用一个对象文字数组表示。例如，它可能是这样的:

```
[
  {
    "id": 23173128,
    "title": "Nutella Hazelnut Spread",
    "price": 8
  },
  {
    "id": 89123012,
    "title": "Oreo Chocolate Sandwich",
    "price": 5
  }
]
```

您可以通过结合使用`+`操作符和`reduce`数组方法来计算购物车中产品的总和:

```
const subtotal = productsInCart
  .reduce((total, product) => total + product.price, 0)
```

目前这样做很好，但是如果后端 API 发生变化，价格意外地从数字转换为字符串，会发生什么情况呢？

```
{
  {
    "id": 23173128,
    "title": "Nutella Hazelnut Spread",
    "price": "8" // Price is now a string!
  },
  {
    "id": 89123012,
    "title": "Oreo Chocolate Sandwich Cookies",
    "price": "5"
  }
}
```

代码仍然会运行，但是结果会有所不同。`8 + 5`导致`13`，而`'8' + '5'`导致`'85'`！不会抛出任何错误，因此 bug 可能会悄悄进入生产环境而不被发现。

这很危险！

# 如何避免这个问题

如果您想要添加您无法绝对控制的数字(例如，来自 API 或用户输入)，那么最好使用`parseInt`、`parseFloat`、`Number`或类似的方式显式强制您的值为数字。

```
console.log(parseInt('2') + parseInt('2')) // This outputs 4
```

通过`parseFloat`函数传递购物车中每个商品的价格，可以避免上面电子商务示例中引入的错误:

```
const subtotal = productsInCart
  .reduce((total, product) => total + parseFloat(product.price), 0)
```

另一方面，如果您想要从可能包含数字的值形成一个字符串，那么您应该显式地强制这些值成为字符串:

```
const prefix = 777
const suffix = 888
const result = prefix.toString() + suffix.toString()
console.log(result) // Outputs '777888'
```

更好的是，不要使用`+`操作符来格式化字符串。请改用 ES6 模板文字:

```
const prefix = 777
const suffix = 888
const result = `${prefix}${suffix}`
console.log(result) // Outputs '777888'
```

# 摘要

在 JavaScript 中,`+`操作符用于执行加法和字符串连接。如果不小心的话，这种双重用途的功能可能会导致危险的错误，当想要添加时会发生连接，反之亦然。

处理这一潜在问题的最佳方式是主动将未知数据转换为其预期类型。可以使用`parseInt`、`parseFloat`或`Number`将值转换为数字。可以通过使用`toString`方法或模板文字将值转换为字符串。
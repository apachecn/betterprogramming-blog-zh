# 使用 JavaScript 对象析构编写干净的代码

> 原文：<https://betterprogramming.pub/write-clean-code-using-javascript-object-destructuring-3551302130e7>

## 比较传统的和 ES6 的析构来解包 JavaScript 中的值

![](img/84a54d83690419ea35d3c761311f55da.png)

[肖斯特](https://unsplash.com/@shauste)在 [Unsplash](https://unsplash.com/) 上拍照。

在本文中，我将使用 ES6 的析构赋值语法来比较传统方法和新方法。该语法允许您从复杂的对象和数组中解包值。这可以用来编写干净的代码，肯定会为您节省一些行。

首先，我们将讨论对象析构。然后我们将继续讨论数组。

> *"****析构赋值*** *语法是一个 JavaScript 表达式，它可以将数组中的值或对象中的属性解包到不同的变量中。"—* [*MDN 网络文档*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

# 对象析构

让我们以客户为例。`customer`对象有类似`name`的属性和类似`address`的嵌套属性:

客户对象

## 基本变量赋值

在传统方法中，我们会使用`.(dot)`符号或`user[key-name]`来检索数据:

无析构赋值的基本变量赋值

这是一个两行的工作，但是我们可以用下面的一行来完成:

带析构赋值的基本变量赋值

如果你想用一个不同的名字解包变量，这也是可能的。请注意，属性名称总是在左侧:

分配给新的变量名

## 为声明的变量赋值

几乎是同样的过程，但是语法有点不同。我们必须在赋值语句周围使用`()`:

为声明的变量赋值

为什么要加`()`？因为左边的`{`被认为是块而不是对象文字。如果您省略`()`，您将得到一个错误:

```
{ name, email } = customer;
                ^SyntaxError: Unexpected token '='
```

## 嵌套对象

很方便，对吧？这是基本的用例。如果您想访问像`city`和`country`这样的属性，您必须一次检查一个键:

无析构赋值的嵌套对象赋值

但是如果你使用对象析构，你可以写在一行中。要访问嵌套对象，我们必须在根键后使用另一个`{}`:

带有析构赋值的嵌套对象赋值

## 使用默认值进行析构

想象一下这样一种情况，`customer`对象有一个名为`married`的属性。选项有`yes`或`no`，但是有些客户不提供该字段的值。所以我们必须给它赋值。否则，它将成为`undefined`。在没有析构的情况下，将按以下方法处理这种情况:

在不析构赋值的情况下赋值默认值

但是有了析构赋值，我们可以在一行中处理它。可读性更好，我们可以删除代码中的`if`语句:

用析构赋值来赋值默认值

## 在对象析构中使用 rest

`rest`语法将收集析构模式尚未挑选出的剩余可枚举属性键。注意`rest`不是一个关键字。您可以使用任何带三个点的变量名。

在对象析构中使用 rest

## 处理空对象

如果那个对象是`null`，如果我们破坏它，它会产生一个错误。但这也是可以避免的。

处理空对象

```
let { name, email } = getCustomer();
      ^

TypeError: Cannot destructure property `name` of 'undefined' or 'null'.
```

为了处理这样的情况，我们可以使用`OR`操作符(`||`)让`null`对象返回到一个空对象:

处理空对象—错误

## 析构函数参数

我们可以析构传递给函数的对象。首先，让我们看看如何在不破坏的情况下完成:

函数参数

使用析构，我们可以编写如下代码:

析构函数参数

# 数组析构

对象析构中的所有语法也可以应用于数组。除此之外，数组析构还有一些很酷的特性。让我们通过定义一个`fruits`数组来讨论它们:

水果阵列

## 基本数组析构

在没有析构赋值的情况下，我们需要像这样通过索引来访问它们:

按索引赋值

但是使用析构赋值，我们可以在一行中轻松地声明和赋值变量:

使用析构赋值来赋值

## 跳过项目并分配休息

想象一下，如果第一项是你最喜欢的水果，第三和第四项是你朋友最喜欢的水果。你需要把它们赋给两个变量。所以我们需要跳过数组中的第二项。在这种情况下，`,`开始发挥作用:

跳过项目并分配休息

您可以使用`,`跳过数组中任意数量的项目。

## 使用析构赋值交换值

好，现在你想交换这些变量。在没有析构赋值的情况下，我们必须声明第三个变量并交换值。但是有了析构赋值，就很容易了。

交换值

# 结论

感谢阅读。我相信这篇文章可以让你的代码更简单，可读性更好。

## 参考

*   [销毁作业](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)
*   [如何在 JavaScript 中使用数组和对象析构](https://www.freecodecamp.org/news/array-and-object-destructuring-in-javascript/)
*   [JavaScript 对象析构](https://www.javascripttutorial.net/es6/javascript-object-destructuring/)
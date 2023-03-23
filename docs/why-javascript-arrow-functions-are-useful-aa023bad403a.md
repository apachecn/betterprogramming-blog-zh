# JavaScript 箭头函数为什么有用？

> 原文：<https://betterprogramming.pub/why-javascript-arrow-functions-are-useful-aa023bad403a>

## 我们应该什么时候使用它们？

![](img/4b908e74dda70dab100147c0c9ee1108.png)

照片由 [Steinar Engeland](https://unsplash.com/@steinart?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/arrow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

从 ES6 开始，有两种类型的通用函数。一个是用`function`关键字声明的传统函数。另一个是 ES6 引入的较新的箭头功能。

在本文中，我们将看看为什么箭头函数是有用的，以及何时应该使用它们。

# 定义箭头函数

箭头函数是带有粗箭头的函数。例如，我们可以将它们声明如下:

```
const fn = () => 'foo';
```

上面的代码将定义`fn`函数并返回字符串`'foo'`。

多行函数可以写成如下形式:

```
const add = (a, b) => {
  return a + b;
}
```

上面的代码将两个数相加。

# 句法糖

箭头函数的语法没有传统函数那么冗长。从上面的第一个例子可以看出，我们可以在一行中定义函数。如果函数只有一行，返回是隐式的。

同样，我们不必一直写`function`关键字来声明函数。

# 我们不必担心‘这个’

它没有自己的`this`。这意味着我们不用担心它里面的`this`的值。

这在大多数情况下是件好事，除非我们想写类方法或构造函数方法。在所有其他情况下，如果我们使用箭头函数而不是函数，我们真的不在乎`this`。

同样对于传统函数，如果我们想在内部函数中访问外部函数内部的`this`的值，我们必须如下访问该值:

在上面的代码中，我们想从`foobar`函数中访问`this.bar`。我们必须写下来:

```
var that = this;
```

而在`foobar`函数中，我们写道:

```
console.log(that.bar);
```

这是一种痛苦，很容易忘记。

如果我们使用箭头函数，我们可以删除两行，写出:

这要好得多，因为我们需要编写更少的代码，并且我们不必担心 arrow 函数中`this`的值。

此外，我们不能使用`bind`、`apply`或`call`来改变箭头函数中`this`的值，因为`this`不能在箭头函数中改变。

对于不使用`this`的函数来说，这是一个很好的特性，因为消除了很多混淆。

![](img/76e8a6b81e866fb9eccaea8b93439c19.png)

图片由 [Adam Grabek](https://unsplash.com/@agmakonts?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 提升和函数声明与表达式

在 JavaScript 中有两种方法定义传统函数。

一个是函数声明，一个是函数表达式。函数声明在任何代码运行之前加载，而函数表达式在 JavaScript 解释器加载代码时加载。

下面是一个函数声明的示例:

```
function foo() {
  return 1;
}
```

下面是一个函数表达式的示例:

```
const foo = function() {
  return 1;
}
```

因为函数声明是在其他任何东西运行之前加载的，所以可以在代码文件的任何部分用函数声明引用它们。这叫做*吊装*。

在定义函数的代码运行之前，函数表达式不可用。

这种差异经常被忽视，很容易引起混淆。我们可以通过使用箭头函数来消除这种混乱，因为我们不必担心提升、函数声明和函数表达式。

对于箭头函数，我们只能像上面那样定义函数，而且在声明之前它是不可用的。

# 参数对象

箭头函数没有自己的`arguments`对象。`arguments`对象是一个类似数组的对象，拥有我们传递给函数调用的所有参数。

`arguments`是一个类似数组的对象，所以它有索引、访问其条目的括号符号和`length`属性。而且，我们可以用`for`循环遍历这些条目，但是除此之外，它一点也不像数组。

这是另一个混乱的来源，因为它有点像数组，但不是真正的数组。对于箭头函数，我们可以使用 rest 参数来代替，这确实给了我们一个数组。

例如，我们可以写:

```
const foo = (...args) => {
  return args;
}
```

那么当我们如下调用`foo`时:

```
foo(1, 2, 3, 4, 5)
```

我们得到:

```
[1, 2, 3, 4, 5]
```

这是一个实数组，所以我们可以使用数组方法和扩展操作符。这比在 arrow 函数和 rest 操作符出现之前，我们不得不使用令人困惑的`arguments`对象来获取动态参数值要好得多。

# 结论

正如我们所见，箭头函数非常有用，除了作为类方法或构造函数方法使用。通过`this`、提升、函数声明与表达式等等，消除了许多混淆。

此外，从语法上来说，它要干净得多，因为我们不必使用`function`关键字来声明函数，并且 return 隐含在一行函数中。

此外，我们不必再对`arguments`对象感到困惑，因为`arguments`不会绑定到箭头函数。
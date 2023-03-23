# 常见的 JavaScript 错误—第 2 部分

> 原文：<https://betterprogramming.pub/common-javascript-mistakes-part-2-e84b60371489>

## 未定义和空，加法和连接，等等

![](img/93aaa8a347e41f982144459e32beeb85.png)

照片由[埃里克韩](https://unsplash.com/@madeyes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种比世界上许多其他编程语言更友好的语言。然而，在编写 JavaScript 代码时，由于误解或忽略我们已经知道的东西，仍然很容易犯错误。通过避免下面的一些错误，我们可以通过防止代码中的错误和错别字让我们的生活变得更容易，这些错误和错别字会让我们陷入意想不到的结果。

# 混淆未定义和空

JavaScript 对于非值既有`undefined`又有`null`。然而，这两者之间有相当多的不同之处。`undefined`意味着变量可能已经被声明了，但是没有给它设置任何东西。变量也可以显式设置为`undefined`。当用`typeof`操作符检查类型时，`undefined`变量的类型将会得到类型`'undefined'`。不返回任何东西的函数返回`undefined`。另一方面，`null`值必须由返回`null`的函数显式设置，或者直接设置为变量。当我们检查一个设置了`null`值的对象时，我们会发现如果一个变量有`null`值，那么它的类型就是`'object'`。

出于这个原因，当我们将变量值设置为非值时，尽可能坚持使用`undefined`可能更容易。这减少了混淆，我们只需要检查变量的类型是否是`'undefined'`就可以知道它是否是`undefined`。对于`null`和`undefined`来说，这比进行两次检查要少得多。

要编写返回`undefined`的函数，我们不需要像下面的例子那样做任何事情:

```
const f = () => {}
```

要设置一个被赋予其他值的变量给`undefined`，我们可以写:

```
x = undefined;
```

要检查属性值是否为`undefine`，我们可以写:

```
typeof obj.prop === 'undefined'
```

或者

```
obj.prop === undefined
```

为了检查一个变量是否是`undefined`，我们可以编写下面的代码:

```
typeof x === 'undefined'
```

一个没有被自动赋值的声明变量的值是`undefined`。

如果我们必须检查`null`，那么我们可以写:

```
obj.prop === null
```

或者

```
x === null
```

对于变量。我们不能使用`typeof`操作符来检查`null`，因为`null`的数据类型是`'object'`。

![](img/c55e07e9ef50d7ca4da6bd8419ab0129.png)

米哈伊尔·瓦西里耶夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 混淆加法和连接

在 JavaScript 中，`+`操作符既用于将两个数相加，也用于将字符串连接在一起。因为 JavaScript 是一种动态语言，所以在应用操作之前，操作数都自动转换为相同的类型。例如，如果我们有:

```
let x = 1 + 1;
```

那么我们得到两个，因为它们都是数字。`+`操作像我们预期的那样用于加法。然而，如果我们有下面的表达式:

```
let x = 1 + '1';
```

然后我们得到`'11'`，因为在应用`+`操作之前，第一个操作数被强制转换成一个字符串。`+`操作用于连接而不是加法。当我们在多个变量上使用`+`操作符时，这使得知道类型更加困难。例如，如果我们有:

```
let x = 1;
let y = 2;
let z = x + y;
```

正如我们所料，我们得到 3，因为`x`和`y`都是数字。另一方面，如果我们有:

```
let x = 1;
let y = '2';
let z = x + y;
```

然后我们得到`'12'`，因为`y`是一个字符串，所以`+`操作符被用于连接。为了解决这个问题，我们应该在使用`+`操作符之前先将所有操作数转换成数字。例如，我们应该将上面的示例重写为以下内容:

```
let x = 1;
let y = '2';
let z = Number(x) + Number(y);
```

上面的代码将得到 3 作为`z`的值，因为我们首先用`Number`工厂函数将它们转换成数字。`Number`函数接受任何对象，如果可以解析为数字，则返回一个数字，否则返回`NaN`。另一种方法是使用`new Number(...).valueOf()`函数，正如我们在下面的代码中所做的:

```
let x = 1;
let y = '2';
let z = new Number(x).valueOf() + new Number(y).valueOf();
```

由于`new Number(...)`是一个创建`object`类型的构造函数，我们想使用`valueOf`函数将其转换回原始类型，以确保我们得到的是一个数字类型。一种更简单的方法是这样写:

```
let x = 1;
let y = '2';
let z = +x + +y;
```

单个操作数前面的`+`符号将尝试将单个操作数转换为数字，如果不能转换为数字，则转换为`NaN`。它的功能与`Number`功能相同。我们还可以将变量转换成特定类型的数字。`Number`对象有一个`parseInt`函数将字符串或对象转换成整数，还有一个`parseFloat`函数将字符串或对象转换成浮点数。`parseInt`将需要转换为数字的对象作为第一个参数。它还将基数作为可选的第二个参数，这是数学数字系统的基础。如果字符串以`0x`开头，那么基数将设置为 16。如果字符串以任何其他字符开头，那么基数将被设置为 10。

我们可以在下面的例子中使用它们:

```
let x = 1;
let y = '2';
let z = Number.parseInt(x) + Number.parseInt(y)
```

同样，我们可以使用下面代码中的`parseFloat`函数:

```
let x = 1;
let y = '2';
let z = Number.parseFloat(x) + Number.parseFloat(y)
```

在上面的两个例子中，我们都会得到 3。

# 将 Return 语句分成多行

JavaScript 在最后关闭一个语句，所以一行代码被认为是不同的。例如，如果我们有:

```
const add = (a, b) => {
  return
  a + b;
}
```

如果我们在运行`a + b`之前运行了结束函数执行的`return`语句，然后运行`console.log(add(1, 2));`，那么我们将得到`undefined`。因此，`a + b`永远不会在这个功能中运行。为了解决这个问题，我们要么将所有的`return`语句放在一行中，要么用括号将我们想要返回的内容括起来。例如，我们可以写:

```
const add = (a, b) => {
  return a + b;
}
```

如果我们运行`console.log(add(1, 2));`，它将记录 3，因为我们实际上是在函数中返回计算结果。我们也可以写:

```
const add = (a, b) => {
  return (
    a + b
  );
}
```

这对于返回可能长于一行的表达式很方便。如果我们运行`console.log(add(1, 2));`，也会记录 3。对于箭头函数，我们也可以写成:

```
const add = (a, b) => a + b
```

对于单行函数，因为`return`语句对于单行箭头函数是隐含的。我们也可以写:

```
const add = (a, b) => (a + b)
```

得到同样的东西。这也适用于单行箭头函数。

在 JavaScript 中，如果语句不完整，比如:

```
const power = (a) => {
  const
    power = 10;
  return a ** 10;
}
```

在函数内部，JavaScript 解释器将运行第一行和第二行来获得完整的语句。所以:

```
const
  power = 10;
```

与以下内容相同:

```
const power = 10;
```

然而，对于像`return`语句这样的完整语句，JavaScript 解释器会将它们视为单独的行。所以:

```
return 
  a ** 10;
```

不等同于:

```
return a ** 10;
```

即使 JavaScript 是一种友好的语言，在编写 JavaScript 代码时仍然很容易出错。当我们不熟悉 JavaScript 时，很容易混淆`undefined`和`null`。由于 JavaScript 的动态类型特性，像`+`这样可以做多件事的操作符很容易被转换成我们不期望的类型，并产生错误的结果。此外，如果语句可以独立完成，那么如果我们希望两行在一起，它们就不应该写在自己的行中。
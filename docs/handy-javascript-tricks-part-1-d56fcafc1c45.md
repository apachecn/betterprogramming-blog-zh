# 方便的 JavaScript 技巧—第 1 部分

> 原文：<https://betterprogramming.pub/handy-javascript-tricks-part-1-d56fcafc1c45>

## 如何删除重复的元素，从数组中删除假值，创建空对象，等等

![](img/e927de6cdb32dc30b51de7343b1fdc3d.png)

[Luis Monse](https://unsplash.com/@luismonse?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 和其他编程语言一样，有许多方便的技巧，让我们可以更容易地编写程序。

在本文中，我们将了解如何删除重复的元素，从数组中删除 falsy 值，创建空对象，以及检查所需的函数参数。

# 移除重复元素

在 ES6 之前，从数组中删除重复元素是一件痛苦的事情。我们必须检查每个条目是否存在，如果已经存在，那么我们删除在另一个已经存在的地方重复的条目。

另一种方法是创建一个字典，将数组条目作为键，然后遍历字典的键来创建另一个数组。

在 ES6 中，我们不必再这样做了，因为我们有了一个新的可迭代对象`Sets`。正如它听起来的那样，它代表了数学中的一个集合。

数学中的一个`Set`是不能有重复的东西的集合。使用`Set`构造函数，我们可以将一个数组直接传递给`Set`构造函数来创建一个集合。

例如，如果我们有一个包含重复元素的数组，那么我们可以将它转换成一个`Set`，然后再转换回一个数组。例如，我们可以写:

```
let arr = [1, 1, 2, 2, 3, 3, 4, 4, 5, 5];
const set = new Set(arr);
arr = Array.from(set);
console.log(arr);
```

在上面的代码中，我们有一个包含许多重复条目的数字数组`arr`。为了删除重复的元素，我们首先用`arr`数组创建一个`Set`。

然后，我们用`Array.from`方法将它转换回一个数组，该方法将任何类似数组的对象作为有效参数，然后我们将它赋回`arr`。

这样，我们对数组使用相同的变量，但是所有重复的元素都消失了。如果我们在上面的代码中运行`console.log`语句，我们将得到`[1, 2, 3, 4, 5]`。

或者，我们可以用扩展操作符替换`Array.from`方法，因为`Set`是一个可迭代对象，这意味着它使用扩展操作符。我们可以像下面这样编写上面的代码:

```
let arr = [1, 1, 2, 2, 3, 3, 4, 4, 5, 5];
const set = new Set(arr);
arr = [...set]
console.log(arr);
```

如果我们运行上面的代码，我们应该得到与第一个例子相同的`console.log`输出。为了使它更短，我们甚至不必创建`set`变量，而是使用 spread 运算符，如下面的代码所示:

```
let arr = [1, 1, 2, 2, 3, 3, 4, 4, 5, 5];
arr = [...new Set(arr)];
console.log(arr);
```

这甚至更短，使用更少的内存，因为我们不必创建另一个变量来删除重复的元素。有了 spread 操作符，现在我们不需要一个循环来删除数组中的重复元素。

# 移除虚假值

要从数组中删除 falsy 值，如`0`、`undefined`、`null`、`NaN`或`false`，我们可以调用`Boolean`方法来完成，因为`Boolean`会将 falsy 值转换为布尔值`false`。

我们可以删除这些值，如下面的代码所示:

```
let arr = [1, 2, 3, 4, 5, 6, false, null, undefined, , null, NaN, 0];
arr = arr.filter(a => Boolean(a));
console.log(arr);
```

在上面的代码中，我们有一个数组`arr`，其中有一些数字和假值，如数组中的`false`、`null`、`undefined`、`NaN`和`0`，以及数组中间的一个整体，解释为`undefined`。

我们通过使用返回这些值的真值的`Boolean`工厂函数来过滤掉它们。虚假的被转换成`false`，然后是一个新的数组。删除 falsy 值后，它返回`filter`函数。

然后，返回的数组被赋回给`arr`变量，这使得`arr`数组采用新值，其中删除了 falsy 值。最后，我们得到了`console.log`输出`[1, 2, 3, 4, 5, 6]`。

由于`Boolean`函数接受一个参数，即您要转换为布尔值的值，并且该值与我们传递给`filter`方法的回调函数的签名相匹配，因此我们可以通过将`Boolean`函数直接传递给`filter`方法作为其回调函数来缩短这个过程，如以下代码所示:

```
let arr = [1, 2, 3, 4, 5, 6, false, null, undefined, , null, NaN, 0];
arr = arr.filter(Boolean);
console.log(arr);
```

使用上面的代码，我们应该从`console.log`得到与上面相同的输出。注意，我们从`Boolean`函数中移除了括号。

这是因为我们将对`filter`方法的函数引用作为回调函数传入。如果我们传入一个函数的引用作为另一个函数的参数，那么我们不应该包括括号，因为我们不调用这个函数，我们只想传入函数引用。

# 创建空对象

如果我们想要创建一个没有任何原型的纯空对象，我们不应该使用`{}`文字，因为这会创建一个具有原型类型的对象。如果我们跑:

```
console.log({}.__proto__)
```

或者:

```
console.log(Object.getPrototypeOf({}))
```

我们得到这样的东西:

```
constructor: ƒ Object()
hasOwnProperty: ƒ hasOwnProperty()
isPrototypeOf: ƒ isPrototypeOf()
propertyIsEnumerable: ƒ propertyIsEnumerable()
toLocaleString: ƒ toLocaleString()
toString: ƒ toString()
valueOf: ƒ valueOf()
__defineGetter__: ƒ __defineGetter__()
__defineSetter__: ƒ __defineSetter__()
__lookupGetter__: ƒ __lookupGetter__()
__lookupSetter__: ƒ __lookupSetter__()
get __proto__: ƒ __proto__()
set __proto__: ƒ __proto__()
```

作为输出。正如我们所见，已经有一堆从`{}`的原型继承而来的方法。

如果我们不需要这些方法，我们可以通过将`null`传递给`Object.create`方法来创建一个没有任何原型的对象，如下面的代码所示:

```
const obj = Object.create(null);
console.log(obj.__proto__);
console.log(Object.getPrototypeOf(obj))
```

上面的代码将创建一个没有原型的对象，这就是为什么我们将`null`传递到`Object.create`方法的参数中。

如果我们运行上面的第一个`console.log`语句，我们应该得到`undefined`，对于第二个语句，我们应该得到`null`。

# 检查所需的参数

使用 ES6，我们可以在函数签名中设置函数参数的默认值。我们可以将一个值设置为默认参数，也可以将另一个函数的返回值设置为默认参数。

这意味着我们可以运行另一个函数，因为我们调用一个带参数的函数。我们可以用它来检查所需的值，方法是通过函数调用来指定参数的默认值。

例如，我们可以编写以下代码:

```
const checkRequired = (paramName) => {
  throw new Error(`${paramName} is required`);
};const greet = (name = checkRequired('name'), greeting = checkRequired('greeting')) => {
  console.log(`Hello ${name}, ${greeting}`)
};greet('Jane', 'How are you?');
try {
  greet('Jane');
} catch (error) {
  console.log(error);
}try {
  greet('How are you?');
} catch (error) {
  console.log(error);
}try {
  greet();
} catch (error) {
  console.log(error);
}
```

然后，我们应该得到这样的东西:

```
Hello Jane, How are you?Error: greeting is required
    at checkRequired ((index):33)
    at greet ((index):36)
    at window.onload ((index):42)Error: greeting is required
    at checkRequired ((index):33)
    at greet ((index):36)
    at window.onload ((index):48)Error: name is required
    at checkRequired ((index):33)
    at greet ((index):36)
    at window.onload ((index):54)
```

作为`console.log`语句的输出。

我们用上面的代码所做的是，当我们用给定的参数调用`greet`函数时，我们正在运行`checkRequired`函数。

对于每个函数调用，我们运行`checkRequired`函数，因为如果参数为`undefined`则每个参数都被传递到参数中，或者如果参数被省略或`undefined`被传递，则我们将`checkRequired`函数的返回值设置为参数的默认值。

`checkRequired`的回报是什么并不重要。我们只是希望它在参数值被省略或者是`undefined`的情况下运行。这样，如果两个参数都没有传入，`greeting`函数就不会运行。

JavaScript 和其他编程语言一样，有许多方便的技巧，让我们可以更容易地编写程序。

在本文中，我们研究了如何删除重复的元素、从数组中删除 falsy 值、创建空对象以及检查所需的函数参数。

有了这些技巧，我们减少了编写代码的工作量，使我们的生活更加轻松。
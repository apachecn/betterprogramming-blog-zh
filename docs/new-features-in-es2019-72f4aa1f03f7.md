# JavaScript 的新功能— ES2019

> 原文：<https://betterprogramming.pub/new-features-in-es2019-72f4aa1f03f7>

## Array.flatMap、try/catch 改进等等

![](img/63912618552756880e7c4bdd831f08d0.png)

Artem Sapegin 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

自从 ES2015 发布以来，这本身就是一个巨大的飞跃，JavaScript 一直在快速改进。从那以后，JavaScript 规范中每年都会增加新的特性。特性一直在增加，比如内置 JavaScript 的新语法和新方法。

在 ES2016 和 2017 中，`Object`对象增加了类似`Object.values`和`Object.entries`的方法。ES2017 中加入了类似`padStart`和`padEnd`的字符串方法。ES2017 中还增加了`async`和`await`，这是一种链接承诺的简写语法。ES2016 中增加了数组的`includes`方法。ES2018 是另一个具有许多新功能的版本。在 ES2018 中，扩展语法现在可用于对象文字。还添加了休息参数。添加了`for await...of`循环，这是一个顺序遍历承诺的循环语法。添加了用于表示无法分离的原始二进制数据的`SharedArrayBuffer`对象。一个`finally`函数也被添加到`Promise`对象中。

2019 年，甚至增加了更多新的 JavaScript 功能。2019 的发布为数组和字符串带来了更多的新方法。`catch`子句不再需要添加绑定——也就是说，不再需要`catch`关键字后括号中的变量。

`description`方法已添加到`Symbol`中。`Function`对象的`toString`方法现在保留了函数的所有字符，这样它就像在你的代码中一样，现在`JSON.stringify`将总是以 UTF-8 编码。

# **Array.flat()**

函数的作用是:将嵌套数组中的条目转换成数组顶层的条目。它递归地这样做，直到给定的深度。要在一个数组上调用`flat`函数，我们只需进入您想要展平的级别的深度，默认为 1。它返回一个新数组，子数组元素串联在其中。为了展平所有级别的嵌套数组，我们可以传入`Infinity`。

例如，我们可以写:

```
const arr1 = [5, 6, [7, 8]];
console.log(arr1.flat());
// [5, 6, 7, 8]const arr2 = [5, 6, [7, 8, [9, 10]]];
console.log(arr2.flat());
// [5, 6, 7, 8, [9, 10]]const arr3 = [5, 6, [7, 8, [9, 10]]];
console.log(arr3.flat(2));
//  [5, 6, 7, 8, 9, 10]const arr4 = [6, 7, [8, 9, [10, 11, [12, 13, [14, 15]]]]];
console.log(arr4.flat(Infinity));
//  [6, 7, 8, 9, 10, 11, 12, 13, 14, 15]
```

正如您所看到的，这是从嵌套数组中移除嵌套的一种非常方便的方法。这是非常有价值的，因为我们很难编写自己的函数来递归地将嵌套数组展平为一个没有嵌套或嵌套较少的展平数组。

运行`Array.flat`后，阵列中的空槽被移除。例如:

```
const arr = [1, 2, , 3, 4];
arr.flat();
// [1, 2, 3, 4]
```

空槽从`arr`中移除。

# Array.flatMap()

`flatMap`方法将一个数组的条目映射到另一个数组，然后展平结果。这相当于在一个数组上调用`map`，后跟深度为 1 的`flat`。这是映射和展平的一个很好的速记，有一些用例，因为很多数组嵌套不是很深。`flatMap`函数对如何映射数组采取回调函数。回调函数将数组、索引和调用`flatMap`方法的原始数组的当前值作为参数。

例如，我们可以在下面的例子中使用`flatMap`:

```
let arr = [1, 2, 3];

arr.map(x => [x * 10]); 
// [[10], [20], [30]]]

arr.flatMap(x => [x * 10]);
// [10, 20, 30]

arr.flatMap(x => [[x * 2]]);
// [[2], [4], [6]]
```

如您所见，`flatMap`只展平了数组的一个级别。如果我们想在映射后展平多个级别，我们必须分别调用`map`和`flat`。

# Function.toString()更改

函数对象总是使用`toString()`方法来获取函数的字符串表示。在 ES2019 中，`toString()`函数现在保留注释和空格，以获得我们定义的函数的精确字符串表示。

例如，如果我们有以下内容:

```
function fn() {
  /* comment */
}console.log(fn.toString())
```

如果我们在函数上运行`toString()`，我们现在将得到与代码完全相同的东西。这意味着这将被记录:

```
function fn() {
  /* comment */
}
```

# JSON.stringify()修复

`JSON.stringify`现在返回特殊 unicode 字符的正确字符，因为现在所有字符都是 UTF-8 编码的。受影响的字符范围从代码 U+D800 到 U+DFF。

现在，如果我们用这些字符在一个对象上调用`JSON.stringify`，我们不再得到错误的字符。如果我们调用`JSON.stringify`,然后在`JSON.parse`返回的字符串上调用`JSON.parse`,我们在字符串化之前得到相同的字符。

![](img/0766d8baf7d935c4da3db589d568de13.png)

[阿德里·托莫](https://unsplash.com/@tormius?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# String.trimStart()

string 对象现在有了一个`trimStart()`函数来修剪字符串开头的空白。还有一个`trimLeft()`方法，它是这个方法的别名。

例如，我们可以写:

```
let message = '   Hi! How\'s it going?   ';console.log(message);// We get '   Hi! How's it going?   'let message = '   Hi! How\'s it going?   ';
console.log(message.trimStart());// We get 'Hi! How's it going?   '
```

正如我们所看到的，左边的空白消失了。我们可以用`trimLeft()`做同样的事情:

```
let message = '   Hi! How\'s it going?   ';
console.log(message.trimLeft());// We get 'Hi! How's it going?   '
```

注意，如果用`trimStart`或`trimLeft`返回一个新的字符串，那么原来的字符串保持不变。这可以防止我们改变原始字符串，这对于防止意外改变对象的错误是很方便的。

# String.trimEnd()

`trimEnd`方法删除字符串右端的空白。`trimRight`是`trimEnd`方法的别名。例如，我们写道:

```
let message = '   Hi! How\'s it going?   ';
console.log(message);
// We get '   Hi! How's it going?   'let message = '   Hi! How\'s it going?';
console.log(message.trimEnd());
// We get '   Hi! How\'s it going?'
```

注意，如果用`trimEnd`或`trimRight`返回一个新字符串，那么原始字符串保持不变。这可以防止我们改变原始字符串，这对于防止意外改变对象的错误是很方便的。

# 可选 Catch 绑定

在 ES2019 中，`catch`子句不必添加绑定。也就是说，`catch`关键字后括号中的变量不再需要。这意味着现在我们可以这样写:

```
try {
    ···
} catch {
    ···
}
```

现在我们不必写这个:

```
try {
    ···
} catch (error) {
    ···
}
```

这意味着如果我们不必使用`error`对象，我们就不必将绑定添加到`catch`子句中。这对于忽略错误或者我们不关心错误是什么很有用。然而，`error`对象对于记录和检查属性输入仍然很方便——并且优雅地处理错误将防止抛出异常。

例如，我们可以用它来捕捉任何类型的异常，比如用`JSON.parse`:

```
try {
  return JSON.parse(str);
} catch {
  return {}
}
```

我们可以优雅地处理无效 JSON 字符串的错误。唯一的问题是我们只是吞下所有的异常，所以捕捉一些类型的错误可能是一个更好的主意。

我们还可以用它来使用并非所有浏览器都支持的浏览器功能。例如:

```
try {
  navigator.geolocation
} catch {
  return false;
}
```

# 符号.描述

Symbols 是一种原始数据类型，它有自己的类型。有一些它自己的静态属性和方法公开全局符号注册表。它就像一个内置对象，但是它没有构造函数，所以我们不能写`new Symbol`用`new`关键字构造一个符号对象。它主要用于对象中的唯一标识符。这是一个符号的唯一目的。

为了创造新的符号，我们可以这样写:

```
const fooSymbol = Symbol('foo')
```

注意，每次我们调用`Symbol`函数，我们都会得到一个新的符号——这个表达式可能是假的:

```
Symbol('sym') === Symbol('sym')
```

对于 ES2019，我们有一个只读的`description`属性。它返回一个字符串，该字符串包含我们传递给`Symbol`函数的参数或众所周知的符号的对象属性路径。

如果我们使用上面的例子并使用`description`属性，我们得到这样的结果:

```
const fooSymbol = Symbol('foo');
console.log(fooSymbol.description);
```

我们得到`foo`日志。

通过 ES2019，我们获得了更多可用于开发 JavaScript 应用的新功能。

2019 的发布为数组和字符串带来了更多的新方法。`catch`子句不再需要添加绑定。也就是说，`catch`关键字后括号中的变量不再需要。

`descrption`方法已添加到`Symbol`中。

`Function`对象的`toString`方法现在保留了函数的所有字符，这样它就像在你的代码中一样。

`JSON.stringify`将始终以 UTF-8 编码，所以现在当我们尝试从代码 U+D800 到 U+DFF 的字符串字符时，不会出现畸形字符。

最后，现在不用用`flat`和`flatMap`方法编写我们自己的代码就可以展平数组。
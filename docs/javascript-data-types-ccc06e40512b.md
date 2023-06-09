# JavaScript 数据类型介绍

> 原文：<https://betterprogramming.pub/javascript-data-types-ccc06e40512b>

## 我们看 JavaScript 数据类型，如数字、BigInt、字符串等。

![](img/db06ccc368fb1dbfc70c016f25c02c2a.png)

斯蒂芬·道森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 和其他编程语言一样，有自己的数据结构和类型。为了用 JavaScript 构建程序，我们必须了解一些数据类型。不同的数据可以放在一起构建更复杂的数据结构。

JavaScript 是一种松散类型或动态类型的语言。这意味着用一种类型声明的变量可以转换成另一种类型，而不用显式地将数据转换成另一种类型。变量也可以在任何时候包含任何类型，这取决于赋值的内容。例如，如果我们编写以下代码:

```
let x = 1;    
x = 'bar';
x = true;
```

第一行中的`x`是一个数字，但是在第二行中，同一个变量`x`已经被重新赋值为一个字符串。在最后一行，它再次被重新分配，这次是分配给一个布尔值。

JavaScript 有多种数据类型。有七种原始数据类型和一种对象类型。这七种基本类型是布尔、空、未定义、数字、BigInt、字符串和符号。

JavaScript 的所有原始类型都是不可变的，这意味着它们不能被改变。基本类型包含一旦定义就固定的值。布尔类型是`true`或`false`，代表逻辑实体。空类型只有一个值，就是`null`。`null`值意味着它指向一些不存在或无效的对象或地址。未定义的类型是 JavaScript 所特有的。这意味着变量没有被赋值。

# 数字

JavaScript 中有两种数字类型，分别是 number 和 BigInt。数字类型是双精度 64 位数字，其值可以在-2 的 53 次方减 1 和 2 的 53 次方减 1 之间。整数没有特定的类型。所有数字都是浮点数。还有三个符号值:`Infinity`、`-Infinity`、`NaN`。

数字的最大和最小可用值分别是`Infinity`和`-Infinity`。我们也可以使用常数`Number.MAX_VALUE`或`Number.MIN_VALUE`来表示最大和最小的数字。我们可以使用`Number.isSafeInteger()`函数来检查一个数字是否在 JavaScript 允许的可用数字范围内。还有常量`Number.MAX_SAFE_INTEGER`和`Number.MIN_SAFE_NUMBER`来检查您指定的数字是否在安全范围内。超出该范围的任何内容都是不安全的，将成为该值的双精度浮点型。数字 0 在 JavaScript 中有两种表示:有+0 和-0，0 是+0 的别名。如果你试图将一个数除以 0，它会注意到:

```
1/+0 // Infinity
1/-0 // -Infinity
```

有时候，数字可以用位操作符表示布尔值，将它们作为布尔值进行操作，但这是一种不好的做法，因为 JavaScript 已经有了布尔值类型，所以使用数字表示布尔值对于阅读代码的人来说是不清楚的。这是因为数字可以表示数字，或者如果有人选择这样使用它们，它们可以表示布尔值。

在 JavaScript 中，BigInt 类型存储超出安全整数范围的数字。可以通过在一个数字的末尾添加一个`n`字符来创建一个 BigInt 数字。使用 BigInt，我们可以计算出超出正常数字安全范围的结果。例如，我们可以写出下面的表达式，并且仍然得到我们期望的数字:

```
const x = 2n ** 55n;
const y = x + 1n;
```

对于`x`，我们得到`36028797018963968n`，对于`y`，我们得到`36028797018963969n`，这就是我们所期望的。BigInts 可以使用与`+`、`*`、`-`、`**`和`%`等数字相同的算术运算。当使用函数、关键字或运算符(如`Boolean`、`if`、`||`、`&&`、`!`)转换为布尔值时，BigInt 的行为类似于数字。BigInts 不能在与 numbers 相同的表达式中操作。如果我们这样做，我们会得到一个`TypeError`。

# 用线串

字符串用于表示文本数据。字符串中的每个元素在字符串中都有自己的位置。它是零索引的，所以字符串第一个字符的位置是 0。字符串的`length`属性有字符串的总字符数。

JavaScript 字符串是不可变的。我们不能修改已经创建的字符串，但是我们仍然可以创建一个包含最初定义的字符串的新字符串。我们可以使用`substr()`函数从字符串中提取子字符串，并使用`concat()`函数连接两个字符串。

我们应该只用字符串表示文本数据。如果你的数据结构需要更复杂的结构，那么它们就不应该用字符串来表示。相反，它们应该是对象。这是因为字符串很容易出错，因为我们可以输入我们想要的字符。所以，容易犯错误。

# 标志

符号是 ES2015 的新功能。这是一个唯一且不可变的标识符。一旦创建，就不能复制。每次你创造一个新的符号，它都是独一无二的。符号主要用于对象中的唯一标识符。这是一个符号的唯一目的。

有一些它自己的静态属性和方法公开全局符号注册表。它就像一个内置对象，但是它没有构造函数，所以我们不能用`new`关键字写`new Symbol`来构造一个符号对象。

为了创造新的符号，我们可以写:

```
const fooSymbol = Symbol('foo')
```

注意，每次我们调用`Symbol`函数，我们都会得到一个新的符号，所以如果我们写

```
Symbol('sym') === Symbol('sym')
```

上面的表达式应该是`false`。

![](img/50013b141eb2b32b83d2d19eb3c9d4e7.png)

乔安娜·科辛斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 目标

对象是一种引用数据类型，这意味着它可以被指向对象在内存中的位置的标识符引用。在内存中，对象的值被存储，通过标识符，我们可以访问该值。对象具有属性，属性是键值对，其值能够包含原始类型或其他对象的数据。这意味着我们可以使用对象来构建复杂的数据结构。键是属性值的标识符，可以存储为字符串或符号。有两种类型的属性在对象中具有某些特性。对象具有数据属性和访问器属性。

JavaScript 对象具有以下数据属性:

*   `[[Value]]` —这可以是任何类型。它具有由属性的 getter 检索的值。默认为`undefined`。
*   `[[Writable]]` —这是一个布尔值。如果是`false`，那么`[[Value]]`是不能改变的。默认为`false`。
*   `[[Enumerable]]` —这是一个布尔值。如果是`true`，那么它可以被`for...in`循环迭代，该循环用于迭代对象的属性。默认为`false`。
*   `[[Configurable]]` —这是一个布尔值。如果是`true`，则可以删除该属性或将其更改为访问器属性，并且可以更改所有属性。否则，不能删除该属性或将其更改为访问器属性，并且不能更改除`[[Value]]`和`[[Writable]]`之外的属性。默认为`false`。

JavaScript 对象具有以下访问器属性:

*   `[[Get]]` —这要么是一个函数，要么是`undefined`。这可能包含一个函数，用于在检索属性时检索属性值。默认为`undefined`。
*   `[[Set]]` —这要么是一个函数，要么是`undefined`。这让我们可以在试图改变对象属性时，为对象的属性设置赋值。默认为`undefined`。
*   `[[Enumerable]]` —这是一个布尔值，默认为`false`。如果是`true`，那么当我们用`for...in`循环遍历属性时，属性将被包含。
*   `[[Configurable]]` —这是一个布尔值，默认为`false`。如果是`false`，那么我们就不能删除该属性，也不能对其进行修改。

JavaScript 有一个内置在标准库中的`Date`对象，所以我们可以用它来操作日期。

数组也是对象。`Array`可以存储一列数据，用整数索引来表示其位置。JavaScript 数组的第一个索引是 0。还有一个`length`属性来获取数组的大小。`Array`对象有许多方便的方法来操作数组，比如`push`方法将条目添加到数组的末尾，而`indexOf`方法查找给定值第一次出现的索引。`TypedArray`对象是让我们看到二进制数据缓冲区的类似数组的视图的对象。

自 ES2015 起，以下类型化数组对象可用:

*   `Int8Array`，取值范围从-128 到 127
*   `Uint8Array`，取值范围从 0 到 255
*   `Uint8ClampedArray`，取值范围从 0 到 255
*   `Int16Array`，取值范围从-32768 到 32767
*   `Uint16Array`，取值范围从 0 到 65535
*   `Int32Array`，取值范围从-2147483648 到 2147483647
*   `Unit32Array`，取值范围从 0 到 4294967295
*   `Floar32Array`，取值范围从-1.2 乘以 10 的 38 次方到 3.4 乘以 10 的 38 次方
*   `Float64Array`，取值范围从 5.0 乘以 10 的 324 到 1.8 乘以 10 的 308
*   `BigInt64Array`，取值范围从-2 到 63 到 2 到 63 减 1
*   `BigUint64Array`，取值范围从 0 到 2 到 64 减 1

从 ES2015 开始，我们有了新的可迭代对象类型。分别是`Map`、`Set`、`WeakMap`、`WeakSet`。`Set`和`WeakSet`表示对象集合，`Map`和`WeakMap`表示具有一系列键值对的对象。`Map`键可以被迭代，但是`WeakMap`的键不能。

# JSON

JSON 是一种类似于 JavaScript 对象的数据序列化格式。它们可以包含普通数据，这意味着函数和其他类型的动态代码在 JSON 中无效。JSON 经常用于为 HTTP 请求发送和接收数据。

# 运算符的类型

使用`typeof`操作符，我们可以确定变量或给定值的类型。

JavaScript 数据类型用于将存储在内存中的数据分类成不同的类型。我们有 numbers 和 BigInts 来存储大小数字。此外，我们有字符串来存储数据。为了存储更复杂的数据，我们可以使用对象来存储数组对象中的列表和键值对的映射。对象本身也由键值对组成。在 JavaScript 的最新版本中，我们还使用类型化数组来存储具有不同取值范围的二进制数据。为了传输和接收数据，我们可以使用 JSON。最后，我们可以使用`typeof`操作符来确定变量或值的数据类型。
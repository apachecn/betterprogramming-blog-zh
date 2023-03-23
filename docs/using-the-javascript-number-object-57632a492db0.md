# 使用 JavaScript 数字对象

> 原文：<https://betterprogramming.pub/using-the-javascript-number-object-57632a492db0>

## JavaScript Number 函数和它创建的对象可以做很多事情

![](img/6576cb86bef662ed9960da2646470df2.png)

K. Mitch Hodge 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 对象是一个包装器对象，它通过为我们提供各种常量和方法来让我们处理数字。可以通过`Number()`函数创建一个原始数字。JavaScript 数字是双精度 64 位二进制格式的 IEEE 754 值。

# 创建数字对象

我们通过编写以下代码，使用`Number`函数创建一个`Number`对象:

```
new Number('123'); 
const a = new Number('123');
const b = Number('123');
```

`Number`函数的主要目的是从字符串中创建一个数字。与`new`操作符一起使用的`Number`构造函数用于创建一个`Number`对象，它的类型是“object”而不是“number”数字原语不是`Number`的实例。原始数值的数据类型是数字。带有`typeof`操作符的类型和带有`instanceof`操作符的构造函数如下所示:

```
console.log(typeof new Number('123'));
console.log(new Number('123') instanceof Number);
```

当我们运行上面两行代码时，我们分别得到“object”和`true`。另一方面，如果我们改为编写以下代码:

```
console.log(typeof Number('123'));
console.log(Number('123') instanceof Number);
```

我们分别得到`number`和`false`。

`Number`对象的主要用途是检查一个对象是否可以转换成数字。如果一个非数字被传入`Number`函数，我们得到返回的`NaN`。如果在非构造函数上下文中没有使用`new`操作符就使用了`Number`，那么用于类型转换就很方便。

`Number`对象具有以下属性:

*   `Number.EPSILON`:两个可表示数字之间的最小间隔。
*   `Number.MAX_SAFE_INTEGER`:JavaScript 中最大安全整数，2⁵减 1。
*   `Number.MAX_VALUE`:可表示的最大正数(1.7976931348623157e+308)。
*   `Number.MIN_SAFE_INTEGER`:JavaScript 中的最小安全整数，-2⁵减 1。
*   `Number.MIN_VALUE`:最小的正可表示数；最接近零但实际上不为零的正数。
*   `Number.NaN`:“非数字”值。
*   `Number.NEGATIVE_INFINITY`:代表负无穷大的值。
*   `Number.POSITIVE_INFINITY`:代表无穷大的值。

# 静态方法

`Number`对象有一些静态方法。

## `Number.isNaN()`

确定传递的值是否为`NaN`。如果变量值为`NaN`，则返回`true`。例如，我们可以用下面的代码来确定一个对象是否是一个数字:

```
console.log(Number.isNaN(+'abc')); // true
console.log(Number.isNaN(+'123')); // false
```

第一行记录了`true`，因为当`'abc'`用一元运算符`+`转换成一个数字时，我们得到了`NaN`。另一方面，第二行记录了`false`，因为当`'123'`用`+`操作符转换成一个数字时，我们得到的`123`是一个数字，所以第二行记录了`false`。

## `Number.isFinite()`

确定传递的值是否为有限数字。如果数字是有限的，返回`true`，否则返回`false`。例如，我们可以用下面的代码来确定一个对象是否是一个数字:

```
console.log(Number.isFinite(Infinity)); // false
console.log(Number.isFinite(123)); // true
```

我们得到第一行的`false`,因为`Infinity`不是一个有限的数，但是第二行记录了`true`,因为`123`是一个有限的数。

## `Number.isInteger()`

确定传递的值是否为整数。如果数字是整数，返回`true`，否则返回`false`。例如，我们可以在下面的代码中使用此方法:

```
console.log(Number.isInteger(123)); // true
console.log(Number.isInteger(123.45)); // false
```

第一行记录了`true`，因为`123`是一个整数，但是第二行记录了`false`，因为它不是一个整数。如果传递的参数不是数字类型，那么它将返回`false`。比如`Number.isInteger('10');`会返回`false`。

## `Number.isSafeInteger()`

确定参数中传递的整数是否在安全整数的范围内，即该数字是否在-2⁵减 1 和 2⁵减 1 之间。例如，如果我们有:

```
console.log(Number.isSafeInteger(Math.pow(2, 53) - 1)); // true
console.log(Number.isSafeInteger(Math.pow(2, 53))); // false
```

第一行记录`true`，因为`Math.pow(2, 53) — 1`在安全范围内，但是第二行记录`false`，因为`Math.pow(2, 53)`不在安全范围内。

## `Number.parseFloat()`

`parseFloat`方法转换传入参数的字符串，并返回一个从字符串解析为浮点数的数字。如果不能解析，则返回`NaN`。例如，如果我们有:

```
console.log(Number.parseFloat('123.45'));
console.log(Number.parseFloat('123'));
console.log(Number.parseFloat('abc'));
```

我们从第一行得到`123.45`，从第二行得到`123`，从第三行得到`NaN`。

## `Number.parseInt()`

`parseInt`方法转换传入参数的字符串，并返回一个从字符串中解析出来的整数。如果字符串的第一个字符无法解析，并且`radix`小于 11，则返回`NaN`。例如，如果我们有:

```
console.log(Number.parseFloat('123.45'));
console.log(Number.parseFloat('123'));
console.log(Number.parseFloat('abc'));
```

然后我们从第一行和第二行得到`123`，从第三行得到`NaN`。

它还将 a `radix`作为第二个自变量，这是数学数系的基础。如果字符串以`0x`开始，那么`radix`将被设置为 16。如果字符串以其他任何东西开始，那么`radix`将被设置为 10。

要将十六进制字符串转换成数字，我们可以编写如下代码:

```
console.log(Number.parseInt('0x1'));
```

当最后一行运行时，我们得到`1`。要将二进制字符串转换为十进制字符串，我们可以编写如下代码:

```
console.log(Number.parseInt('0111', 2));
```

上面的行将记录`7`，它是二进制数`0111`的十进制等价物。

![](img/396b7f0db32e5a82147d1ed05518f1c2.png)

安德鲁·布坎南在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 实例方法

所有的`Number`实例都继承自`Number.prototype`，这为对象提供了一些实例方法。下面是`Number`的实例方法。

## `Number.toExponential()`

`toExponential`方法返回一个用指数记数法表示数字的字符串。它采用一个可选参数，指定要包含的小数位数。例如，我们可以写:

```
(123).toExponential(1);
```

然后我们得到`1.2e+2`。

## `Number.toFixed()`

`toFixed`方法返回一个用定点符号表示数字的字符串。它采用一个可选参数，指定小数点后要包含的小数位数。例如，我们可以写:

```
console.log((123).toFixed(1));
```

我们得到了`123.0`。

## `Number.toLocaleString()`

此方法返回一个字符串，该字符串包含数字的区分语言的表示形式。它覆盖了`Object.prototype.toLocaleString()`方法。第一个参数是`locales`参数，它接受一个区域设置字符串或一个区域设置字符串数组。这是一个可选参数。它使用一个 BCP 47 语言标签和可选的 Unicode 扩展键`nu`来指定数字格式的编号系统。`nu`的可能值包括:`"arab"`、`"arabext"`、`"bali"`、`"beng"`、`"deva"`、`"fullwide"`、`"gujr"`、`"guru"`、`"hanidec"`、`"khmr"`、`"knda"`、`"laoo"`、`"latn"`、`"limb"`、`"mlym"`、`"mong"`、`"mymr"`、`"orya"`、`"tamldec"`、`"telu"`、`"thai"`、`"tibt"`。构造函数创建的对象实例让`format`方法返回一个带格式化数字的字符串。

第二个参数接受一个具有几个属性的对象:`localeMatcher`、`style`、`unitDisplay`、`currency`、`useGrouping`、`minimumIntegerDigits`、`minimumFractionDigits`、`maximumFractionDigits`、`minimumSignificantDigits`和`maximumSignificantDigits`。

`localeMatcher`选项指定要使用的区域匹配算法。可能的值是`lookup`和`best fit`。查找算法搜索区域设置，直到找到符合正在比较的字符串的字符集的区域设置。`best fit`查找至少与`lookup`算法一样，但可能更适合的语言环境。

## 风格

`style`选项指定要使用的格式样式。`style`选项的可能值包括`decimal`、`currency`、`percent`和`unit`。`decimal`是默认选项，用于普通数字格式，`currency`用于货币格式，`percent`用于百分比格式，`unit`用于`unit`格式。

属性`currency`的可能值是 ISO 4217 货币代码，例如`USD`代表美元，`EUR`代表欧元。没有默认值。如果`style`属性设置为`currency`，则必须提供`currency`属性。`currencyDisplay`属性设置如何在货币格式中显示货币。可能的值有`symbol`用于添加本地化货币符号，这是默认值，`code`用于添加 ISO 货币代码，`name`用于使用本地化货币名称，如“美元”`useGrouping`选项用于设置数字使用的分组分隔符。这是一个布尔值。

`minimumIntegerDigits`、`minimumFractionDigits`和`maximumFractionDigits`被认为是一组选项。`minimumIntegerDigits`指定要使用的最小整数位数，范围从 1 到 21，1 为默认选项。`minimumFractionDigits`是要使用的最小分数位数，范围从 0 到 20。对于普通数字和百分比格式，默认值为 0。货币格式的默认值由 ISO 4217 货币代码列表指定，如果列表中没有指定，则为 2。`maximumFractionDigits`是分数位数的最大值，取值范围为 0 到 20。普通数字的默认值是`minimumFractionDigits`和 3 之间的最大值。货币格式的默认值是`minimumFractionDigits`和 ISO 4217 货币代码列表提供的小数位数之间的最大值，如果列表不提供该信息，则为 2。百分比格式的默认值是介于`minimumFractionDigits`和 0 之间的最大值。

`minimumSignificantDigits`和`maximumSignificantDigits`被认为是另一组选项。如果至少定义了该组中的一个选项，则忽略第一组。`minimumSignificantDigits`是要使用的最小有效位数，取值范围为 1 到 21，默认值为 1。`maximumSignificantDigits`是要使用的最大有效位数，取值范围为 1 到 21，默认值为 21。

例如，我们可以在下面的例子中使用这种方法:

```
const number = 123.45;console.log(number.toLocaleString('fr', {
  style: 'currency',
  currency: 'EUR'
}));console.log(number.toLocaleString('zh-Hant-CN-u-nu-hanidec', {
  style: 'currency',
  currency: 'JPY'
}))console.log(number.toLocaleString('en-IN', {
  maximumSignificantDigits: 3
}));
```

The first `console.log` statement will log `123,45 €`, the second `console.log` will log `¥一二三`, and the third `console.log` will get `123`.

## `Number.toPrecision()`

`toPrecision`方法返回一个字符串，该字符串以定点或指数记数法表示指定精度的数字。它采用一个参数，可选地让我们指定 1 到 100 之间的基数。如果未指定，则将返回小数点后的所有数字。例如，我们可以在下面的代码中使用它:

```
const numObj = 5.12345;console.log(numObj.toPrecision());
console.log(numObj.toPrecision(100));
console.log(numObj.toPrecision(5));
console.log(numObj.toPrecision(2));
console.log(numObj.toPrecision(1));
```

我们为每一行记录了以下内容:

```
5.123455.1234500000000000596855898038484156131744384765625000000000000000000000000000000000000000000000000005.12355.15
```

## `Number.toString()`

`toString`方法返回一个字符串，该字符串代表指定的`radix`中的指定对象，或数字的基数。它覆盖了`Object.prototype.toString()`方法。`radix`是一个可选参数，让我们指定 2 到 36 之间的基数。默认的`radix`是 10。如果数字是负数，则符号保持不变。如果它不是一个整数，那么将使用一个点号来分隔小数位。例如，我们可以写:

```
const count = 10;

console.log(count.toString());    // '10'
console.log((15).toString());     // '15'
console.log((15.2).toString());   // '15.2'
```

它也适用于非十进制数。例如，我们可以写:

```
console.log((-0b0111).toString(10)); // '-7'
```

将不同基数的数字转换成其他数字很方便。

## `Number.valueOf()`

方法返回指定对象的原始值。它覆盖了`Object.prototype.valueOf()`方法。例如，如果我们有一个用`new`操作符构造的`Number`对象，我们可以写:

```
const numObj = new Number('10');
const numPrim = numObj.valueOf();
console.log(numPrim);
console.log(typeof numPrim);
```

运行上面的代码时，我们会记录以下内容:

```
10
number
```

# 将其他对象转换为数字

使用`Number`函数，我们可以将`Date`对象和`null`转换成数字。例如，对于`Date`对象，我们可以写:

```
const d = new Date(2019, 0, 1, 0, 0, 0);
console.log(Number(d));
```

我们得到`1546329600000`，这是 2019 年 1 月 1 日的 UNIX 时间戳。`null`对象将被转换为 0，所以如果我们写:

```
console.log(Number(null));
```

我们得到 0。

# 结论

JavaScript 对象是一个包装器对象，它通过为我们提供各种常量和方法来让我们处理数字。通过`Number()`功能可以创建一个原始数字。它有许多有用的静态和实例方法，用于检查数字以及在字符串和数字之间进行转换，反之亦然。`toString`方法也便于将数字转换成不同的基数。
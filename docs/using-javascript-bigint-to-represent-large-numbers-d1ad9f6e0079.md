# 使用 JavaScript BigInt 表示大数

> 原文：<https://betterprogramming.pub/using-javascript-bigint-to-represent-large-numbers-d1ad9f6e0079>

## 添加一个新的原始数据类型来处理大整数

![](img/3387158354b6c5aabbb417d100ce93b0.png)

照片由[拉利斯 T](https://unsplash.com/@im_laleeth?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了在 JavaScript 中表示大于 2 的 53 次方减 1 的整数，我们可以使用`BigInt`对象来表示这些值。

它可以通过算术运算符之类的常规运算来操作，如加、减、乘、除、余数和取幂。

它可以由数字和十六进制或二进制字符串构成。此外，它还支持像 AND、OR、NOT 和 XOR 这样的位运算。唯一不起作用的位运算是零填充右移运算符(`>>>`)，因为`BigInts`都是有符号的。

另外，不支持一元`+`运算符不中断 [asm.js](http://asmjs.org/) 。只有当所有操作数都是`BigInts`时，这些操作才会完成。我们不能有的操作数是`BigInts`有的是数字。

在 JavaScript 中,`BigInt`与普通数字不同。它与普通数字的区别在于数字末尾有一个`n`。我们可以用`BigInt`工厂函数定义一个`BigInt`。

它接受一个参数，该参数可以是整数，也可以是表示十进制整数、十六进制字符串或二进制字符串的字符串。`BigInt`不能与内置的`Math`对象一起使用。

此外，当在数字和`BigInt`之间进行转换时，我们必须小心，因为在将`BigInt`转换为数字时，可能会丢失`BigInt`的精度。

# 使用

为了定义一个`BigInt`，如果我们想传入一个整数，我们可以写如下:

```
const bigInt = BigInt(1);
console.log(bigInt);
```

当我们运行`console.log`语句时，它会记录`1n`。如果我们想将一个字符串传递给工厂函数，我们可以这样写:

```
const bigInt = BigInt('2222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222');
console.log(bigInt);
```

我们还可以将一个十六进制数字字符串和一个以`0x`开头的字符串传递给工厂函数:

```
const bigHex = BigInt("0x1fffffffffffff111111111");
console.log(bigHex);
```

当我们在`bigHex`上运行`console.log`时，上面的代码会记录`618970019642690073311383825n`。

同样，我们可以传入一个二进制字符串，其中一个字符串以`0b`开头，另一个二进制字符串位于字符串的剩余部分，以获得一个`BigInt`:

```
const bigBin = BigInt("0b111111111111111000000000011111111111111111111111");
console.log(bigBin);
```

当我们在上面运行`console.log`时，上面的代码会让我们得到`281466395164671n`。如果我们想要创建的`BigInt`超出了 number 类型可以接受的范围，那么传入字符串会很方便。

我们也可以用一个`BigInt`字面值定义一个`BigInt`，我们可以在整数上附加一个`n`字符。例如，我们可以写:

```
const bigInt = 22222222222222222222222222222222n;
console.log(bigInt);
```

然后，当我们记录值时，我们得到`22222222222222222222222222222222n`作为`bigInt`的值。

`BigInt`在 JavaScript 中有自己的数据类型。当您对`BigInt`变量、常量或值运行`typeof`操作符时，我们将得到`bigint`。例如，当我们运行:

```
typeof 2n === 'bigint';
typeof BigInt(2) === 'bigint';
```

两行代码的计算结果都是`true`。然而，如果我们用`Object`工厂函数包装它，我们得到的类型是一个对象:

```
typeof Object(2n) === 'object';
```

上面的代码将评估为`true`。

我们可以将数字运算应用于`BigInts`。

这包括算术运算符——加、减、乘、除、余数和取幂。此外，它还支持像 AND、OR、NOT 和 XOR 这样的位运算。

唯一不起作用的位运算是零填充右移运算符(`>>>`)，因为`BigInts`都是有符号的。此外，不支持一元`+`操作符，以免破坏 asm.js。

这些操作只有在所有操作数都是`BigInts`时才进行。我们不能有的操作数是`BigInts`有的是数字。例如，如果我们有:

```
const bigInt = BigInt(Number.MAX_SAFE_INTEGER);
console.log(bigInt);const biggerInt = bigInt + BigInt(1);
console.log(biggerInt);const evenBiggerInt = bigInt + BigInt(2);
console.log(evenBiggerInt);const multi = bigInt * BigInt(2);
console.log(multi);const subtr = bigInt - BigInt(10);
console.log(subtr);const remainder = bigInt % BigInt(1);
console.log(remainder);const bigN = bigInt ** BigInt(54);
console.log(bigN);const veryNegativeNum = bigN * -BigInt(1)
console.log(veryNegativeNum);
```

我们得到`bigInt`的 9007199254740991n，`biggerInt`的 9007199254740992n，`evenBiggerInt`的 9007199254740993n，`multi`的 18014398509481982n，`subtr`的 9007199254740981n，`remainder`的 0n，353059244

注意，当我们得到小数结果时，小数部分将被截断。`BigInt`是一个大整数，不是用来存储小数的。例如，在下面的示例中:

```
const expected = 8n / 2n;
console.log(expected)const rounded = 9n / 2n;
console.log(rounded)
```

对于`expected`和`rounded`我们得到 4n。这是因为小数部分已从`BigInt`中删除。

比较运算符可以应用于 BigInts。操作数可以是`BigInt`或数字。我们可以将大于、大于或等于、小于、小于或等于、两倍等于和三倍等于运算符与 BigInts 一起使用。

例如，我们可以将 1n 比作 1:

```
1n === 1
```

上面的代码将计算为`false`，因为`BigInt`和数字不是同一类型。但是当我们用两个相等替换三个相等时，如下面的代码所示:

```
1n == 1
```

上面的语句评估为`true`，因为只有值被比较。

注意，在这两个例子中，我们混合了`BigInt`操作数和数字操作数。这对于比较运算符是允许的。

BigInts 和 numbers 也可以与其他运算符一起比较，如下例所示:

```
1n < 9
// true9n > 1
// true9 > 9n
// false

9n > 9
// false

9n >= 9
// true
```

此外，它们可以在一个数组中混合在一起并一起排序。例如，如果我们有以下代码:

```
const mixedNums = [5n, 6, -120n, 12, 24, 0, 0n];
mixedNums.sort();
console.log(mixedNums)
```

我们得到`[-120n, 0, 0n, 12, 24, 5n, 6]`。我们还使用以下代码对其进行降序排序:

```
const mixedNums = [5n, 6, -120n, 12, 24, 0, 0n];
mixedNums.sort((a, b) => {
  if (a > b) {
    return -1
  } else if (a < b) {
    return 1
  }
  return 0
});
console.log(mixedNums)
```

在上面的代码中，我们使用了比较运算符来比较数字的值，如果第一个数字大于第二个数字，则返回-1，如果第一个数字小于第二个数字，则返回 1，否则返回 0。

如果我们用`Object`包装 BigInts，那么它们就作为对象进行比较，所以只有引用了同一个实例，两个对象才被认为是相同的。例如，如果我们有:

```
0n === Object(0n);
Object(0n) === Object(0n);
const o = Object(0n);
o === o;
```

那么上面代码中的前三行将计算为`false`，而最后一行将计算为`true`。

当 BigInts 被强制转换为布尔值时，它们的行为就像是数字一样。例如，`Boolean(0n)`会返回`false`，其他任何东西都会返回`true`。

例如，我们可以将它们强制转换为布尔值，如下面的代码所示:

```
0n || 11n
// 11n

0n && 11n
// 0n

Boolean(0n)
// false

Boolean(11n)
// true

!11n
// false

!0n
// true
```

# BigInt 方法

`BigInt`对象有几个方法。有静态的`asIntN()` 和`asUintN()` 方法，以及`toLocaleString()`、`toString()`和`valueOf()` 实例方法。

`asIntN`方法在-2 到宽度减 1 和 2 到宽度减 1 之间包装一个`BigInt`值。例如，如果我们有:

```
const bigNum = 2n ** (62n - 1n) - 1n;
console.log(BigInt.asIntN(62, bigNum));
```

然后我们得到返回的`bigNum`模 62 的实际值的字面量，就是 2305843009213693951n。然而，如果我们有:

```
const bigNum = 2n ** (63n - 1n) - 1n;
console.log(BigInt.asIntN(62, bigNum));
```

我们得到-1n，因为`2n ** (63n — 1n)`模`2n ** 63n`被返回。

`asUintN()`方法将 0 和 2 之间的`BigInt`值包装为宽度减 1。例如，如果我们有:

```
const bigNum = 2n ** 11n - 1n;
console.log(BigInt.asUintN(11, bigNum));
```

然后我们得到返回的`bigNum`实际值的字面量，是 2305843009213693951n。然而，如果我们有:

```
const bigNum = 2n ** 11n - 1n;
console.log(BigInt.asUintN(11, bigNum));
```

我们得到 2047n，因为返回了`2n ** 11n — 1n`模`2n ** 11n`。

`BigInt`有一个`toLocaleString()`方法根据我们传入的地区返回`BigInt`的字符串值。例如，如果我们想得到一个`BigInt`的法语表示，我们可以写:

```
const bigNum = 2n ** 60n - 1n;
console.log(bigNum.toLocaleString('fr'));
```

上面的代码会记录`1 152 921 504 606 846 975`。

方法将把一个`BigInt`转换成一个字符串。例如，我们可以写:

```
const bigNum = 2n ** 60n - 1n;
console.log(bigNum.toString());
```

上面的代码将记录`1152921504606846975`。

`valueOf`方法将获得`BigInt`对象的值。例如，如果我们运行:

```
const bigNum = 2n ** 60n - 1n;
console.log(bigNum.valueOf());
```

然后我们得到`1152921504606846975n`日志。

JSON 不支持 BigInts，所以如果我们试图用`JSON.stringify()`将它转换成 JSON，就会引发`TypeError`。

但是，我们可以把它转换成支持的东西，比如一个字符串，然后它可以存储为 JSON。我们可以通过编写以下代码来覆盖`BigInt`的`toJSON`方法:

```
BigInt.prototype.toJSON = function() {
  return this.toString();
}const bigIntString = JSON.stringify(88888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888n)
console.log(bigIntString);
```

然后，我们得到:`“88888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888888”`。

BigInts 用于表示大于 2 的 53 次方减 1 的整数。在 JavaScript 中，我们可以使用`BigInt`对象来表示这些值。

它可以通过算术运算符之类的常规运算来操作，如加、减、乘、除、余数和取幂。

BigInts 支持大多数位运算，如 AND、OR、NOT 和 XOR。

它们也可以从十六进制或二进制数字转换而来。

只有当所有操作数都是 BigInts 时，才会执行这些操作。我们不能有一些操作数是 BigInts，一些是数字。

在 JavaScript 中，一个`BigInt`和一个普通的数字不一样。它与普通数字的区别在于数字末尾有一个`n`。我们可以用`BigInt`工厂函数定义一个`BigInt`。

它接受一个参数，该参数可以是整数，也可以是表示十进制整数、十六进制字符串或二进制字符串的字符串。`BigInt`不能与内置的`Math`对象一起使用。
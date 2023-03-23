# 在 JavaScript 中将字符串转换成数字的最佳方式

> 原文：<https://betterprogramming.pub/what-is-the-best-way-to-convert-a-string-to-a-number-in-javascript-67052a1706c6>

## 让辩论开始吧

![](img/52e34d0adf3c0da4bd39714b7322010d.png)

照片由[沃尔坎·奥尔梅斯](https://unsplash.com/@volkanolmez?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/numbers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

您可能已经知道，在 JavaScript 中有多种方法可以将字符串转换成整数。然而，对于开发人员来说，哪种方法是“最好的”方法还有待讨论。让我们来看看一些选项。

# parseInt()

```
parseInt(string, radix)
```

我们从`parseInt()`开始，它有两个参数，一个字符串和一个基数。该函数返回从给定字符串解析的整数。

如果第一个参数不是字符串，使用`ToString()`自动将其转换为字符串。无论哪种方式，任何前导空格都会被忽略。

`parseInt()`方法从位置 0 开始，确定在那里找到的字符是否可以转换成有效的数字。如果不是，该方法返回`NaN`并停止。这里有一个例子:

```
const str = “What’s funnier than 24?”,console.log(parseInt(str)) // NaN
```

输出将是`NaN`，尽管字符串包含一个数字。

如果位置 0 处的字符有效，则该方法继续执行相同的测试。这种情况一直持续到`parseInt()`遇到一个无效数字，这时它会将当前字符串转换成一个数字。

```
console.log(parseInt(“24haha25”)) // 24
```

这将返回`24`，因为它将在`haha`即`NaN`处停止解析。

MDN 建议，当使用`parseInt()`时，您应该“总是指定一个`radix`来避免……不可靠的行为。”

这是因为 ECMAScript 5 之前的版本，`parseInt()`使用八进制基数(8)作为字符串以“0”开头的默认值。从 ECMAScript 5 开始，默认值为十进制基数(10):

> 如果基数为`undefined`或`0`，则假定为`10`，除非数字以字符对`0x`或`0X`开头，在这种情况下假定基数为`16`— MDN 文档

# parseFloat()

```
parseFloat(string)
```

`parseFloat()`和`parseInt()`很像，主要有两个区别。

第一，`parseFloat()`与`parseInt()`不同，它不把一个基数作为自变量。这意味着 string 必须以十进制(基数 10)表示浮点数，而不是八进制(基数 8)或十六进制(基数 6)。

第二，当使用`parseFloat()`时，小数点是一个有效字符，但只有第一次遇到时才有效。如果到达第二个小数点，该方法将在该位置停止解析。

```
console.log(parseFloat(“3.141.59”)) // 3.141
```

另外，`parseInt()`的其他行为在这里也有:

*   只返回字符串中的第一个数字。
*   允许并忽略前导和尾随空格。
*   如果第一个字符不是有效数字，则返回`NaN`。

# 数字( )

```
Number(value)
```

解析函数和`Number()`在很大程度上是可以互换的。`Number()`可以处理一个小数，但也会被抛出一个以上的小数点。前导和尾随空白仍然被接受和忽略。

有一些不同之处。顾名思义，解析方法试图在转换字符串时逐段解析字符串，`Number()`试图一次将整个字符串参数转换成数字。

```
**const** str = '123abc';
Number(str); *// NaN (attempts to convert entire string, cannot)*
parseInt(s); *// 123 (stops as soon as it hits `a`)* 
```

`Number()`是作为函数调用的 JavaScript `Number`构造函数，它执行*类型转换。*它试图将*的任何*值转换成一个数字:

*   `false`变成`0`，`true`变成`1`。
*   `null`变成`0`，`undefined`变成`NaN`。
*   空字符串`''`变为`0`，非数字字符串变为`NaN`。
*   `Number('')`赐`0`；`parseInt('')`给出`NaN`。

下面是来自 W3Schools[的`Number()`概述:](https://www.w3schools.com/)

# 一元+运算符

您可能知道也可能不知道`+`一元运算符在 JavaScript 中将字符串转换成数字的能力。例如:

```
+"123" // 123
+"123.456" // 123.456
+"123.456.789" // NaN
+"I'm a number" // NaN
```

据我所知，以这种方式使用一元运算符`+`完全模仿了`Number()`的行为，没有任何例外。那么，为什么要使用`Number()`？

有些人将`+`操作符视为一种语法糖，因为它可以用来简化解析函数或`Number()`功能。

然而，更少的代码并不总是等于更简洁的代码。在做出关于这种捷径的决定时，应当考虑清楚。

不是每个人都知道这种行为，这可能会给阅读您代码的人带来困惑，而`parseInt()`是清晰简洁的。

在我看来，一般来说，你应该使用描述它做什么的代码，而不是使用非操作副作用的代码

# 乘以 1

另一种快捷方式是，如果你知道你要转换的字符串是一个数字，你可以把它乘以 1 来转换它:

```
"123"*1 // 123
"123.456"*1 // 123.456
"123.456.789"*1 // NaN
"I'm a number"*1 // NaN
```

同样，这个方法的行为与`Number()`相同，我已经看到它被吹捧为最快的转换方法。然而，我以前对这种语法糖的有用性的担心在这里也适用。

# 正则表达式

允许定制的一个选项是在您自己构造的函数中实现正则表达式。以下是 MDN 的一个例子:

# 那么，哪种方法最好呢？

亲爱的读者，我真诚地问你这个问题！现在我们已经讨论了一些(但不是全部)选项，我很想听听你认为哪种(或哪些)方法是最好的(以及为什么)。

我确信你们中的许多人对性能时间的了解比我多得多，尽管操作符和函数的性能会随着浏览器中 JavaScript 引擎的优化而改变。

我希望阅读一些有益和健康的辩论，如果没有别的，我希望这是一个很好的回顾，也许你甚至学到了一些新的东西。

感谢阅读！
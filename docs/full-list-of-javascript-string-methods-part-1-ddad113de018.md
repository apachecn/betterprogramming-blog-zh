# JavaScript 字符串方法的完整列表(第 1 部分)

> 原文：<https://betterprogramming.pub/full-list-of-javascript-string-methods-part-1-ddad113de018>

## 我们看了每一种可以用来操作字符串的方法

![](img/243180c94f1b94288ac72570be51aee1.png)

[Raychan](https://unsplash.com/@wx1993?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

字符串是 JavaScript 中一个重要的全局对象。它们代表一系列字符。它们可以被各种操作符使用，比如比较操作符，并且有各种方法可以用来操作它们。有不同的方法来操作字符串，在不同的位置查找字符串的各个部分，以及修剪和替换字符串。随着 JavaScript 规范的每一次发布，都会添加更多的方法来使字符串搜索和操作变得更加容易。

# 静态方法

调用静态方法无需创建字符串或对象。有以下几种方法:

## `String.fromCharCode()`

使用`fromCharCode`方法，我们可以构造一个字符串，方法是传入一个以逗号分隔的 HTML 字符代码列表或十六进制 Unicode 字符代码作为参数，并获得一个返回实际字符的字符串。例如，我们可以写:

```
const str = String.fromCharCode(38, 8226)
```

然后，当我们用`console.log`登录`str`时，我们得到`&•`。我们可以传入十六进制字符代码，如下面的代码:

```
const str = String.fromCharCode(0x0026, 0x2022)
```

然后，当我们用`console.log`记录`str`时，我们也得到了`&•`。

## String.fromCodePoint()

`String.fromCodePoint()`方法返回一个字符串，该字符串由您在参数中输入的字符代码给出。代码点可以是 HTML 字符代码或十六进制 Unicode 字符代码。例如，我们可以写:

```
const str = String.fromCodePoint(38, 8226)
```

然后，当我们用`console.log`记录`str`时，我们得到`&•`。我们可以传入十六进制字符代码，如下面的代码:

```
const str = String.fromCodePoint(0x0026, 0x2022)
```

然后，当我们用`console.log`记录`str`时，我们也得到`&•`。

# 实例方法

字符串的实例方法在字符串文字或字符串对象上调用。他们对被调用的字符串的实例做一些事情。

## String.charAt()

`charAt`返回位于字符串索引处的字符。

例如:

```
const str = "Hello";
const res = str.charAt(0); // 'H'
```

## String.charCodeAt()

`charCodeAt`返回位于字符串索引处的 UTF-16 字符代码。

例如:

```
const str = "Hello";
const res = str.charCodeAt(0); // 72
```

## String.codePointAt()

`codePointAt`返回位于字符串索引处的 UTF-16 编码码位的码位值。

例如:

```
const str = "Hello";
const res = str.codePointAt(0); // 72
```

## String.concat()

`concat`方法组合两个字符串并返回一个新的字符串。例如，我们可以写:

```
const str1 = "Hello";
const str2 = " World";
const res = str1.concat(str2); // 'Hello World'
```

## String.endsWith()

`endsWith`检查一个字符串是否以你传入的子字符串结尾。

例如:

```
const str = "Hello world.";
const hasHello = str.endsWith("world."); // trueconst str2 = "Hello world.";
const hasHello2 = str.endsWith("abc"); // false
```

## String.includes()

`includes`检查传入的子串是否在字符串中。如果在字符串中，它返回`true`，否则返回`false`。

```
const str = "Hello";
const hasH = str.includes('H'); // true
const hasW = str.includes('W'); // false
```

## String.indexOf()

`indexOf`查找子字符串第一次出现的索引。如果没有找到，则返回-1。

例如:

```
const str = "Hello Hello.";
const hasHello = str.indexOf("Hello"); // 0
const hasHello2 = str.indexOf("abc"); // -1
```

## String.lastIndexOf()

`lastIndexOf`查找子字符串最后一次出现的索引。如果没有找到，则返回-1。

例如:

```
const str = "Hello Hello.";
const hasHello = str.lastIndexOf("Hello"); // 6
const hasHello2 = str.lastIndexOf("abc"); // -1
```

![](img/0c6ca057db6a8ed1bd6c2b4047aab282.png)

帕斯卡尔·贝纳登在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## String.localeCompare()

`localeCompare`方法通过使用地区规则比较字符串的优先顺序来比较一个字符串是否在另一个之前。调用此方法的一般语法是:

```
referenceStr.localeCompare(compareString, locales, options)
```

根据`referenceStr`是否在`compareStr`之前，它返回以下值:

*   当`referenceStr` 出现在`compareStr`之前时为负
*   当`referenceStr` 发生在`compareStr`之后时为正
*   如果它们相等，则返回 0

该方法的第一个参数是要比较的字符串。

该方法的第二个参数可以选择一个区域设置字符串或区域设置字符串数组，它们是带有可选 Unicode 扩展键的 BCP 47 语言标记。Unicode 扩展键，包括`"big5han"`、`"dict"`、`"direct"`、`"ducet"`、`"gb2312"`、`"phonebk"`、`"phonetic"`、`"pinyin"`、`"reformed"`、`"searchjl"`、`"stroke"`、`"trad"`、`"unihan"`在我们的区域设置字符串中也是允许的。它们指定了我们想要用来比较字符串的排序规则。但是，当第二个参数的选项中有与此重叠的字段时，该参数中的选项将覆盖第一个参数中指定的 Unicode 扩展键。

可以通过在第一个参数中将`kn`添加到您的区域设置字符串中来指定数字排序规则。例如，如果我们想比较数字串，那么我们可以写:

```
console.log('10'.localeCompare('2', 'en-u-kn-true'));
```

然后，我们得到一个，因为我们在构造函数的区域字符串中指定了`kn`。这使得排序器比较数字，10 跟在 2 后面。

此外，我们可以用`kf`扩展键指定是大写字母还是小写字母应该首先排序。可能的选项有`upper`、`lower`或`false`。`false`表示区域设置的默认值将是选项。该选项可以在区域设置字符串中设置，方法是将其作为 Unicode 扩展键添加到区域设置字符串中，如果两者都提供了，那么`option`属性将优先。例如，要使大写字母优先于小写字母，我们可以写:

```
console.log('Able'.localeCompare('able', 'en-ca-u-kf-upper'));
```

这将首先对大写字母的同一个单词进行排序。当我们运行`console.log`时，我们得到-1，因为我们在‘能力’中有一个大写的‘A’，在‘能力’中有一个小写的‘A’另一方面，如果我们改为在构造函数中传递`en-ca-u-kf-lower`，如下面的代码所示:

```
console.log('Able'.localeCompare('able', 'en-ca-u-kf-lower'));
```

然后在`console.log`之后我们得到一个，因为`kf-lower`意味着我们将小写字母的同一个单词排在大写字母的前面。

该方法的第三个参数可以选择接受一个可以具有多个属性的对象。对象接受的属性有`localeMatcher`、`usage`、`sensitivity`、`ignorePunctuation`、`numeric`和`caseFirst`。`numeric`与区域设置字符串中 Unicode 扩展键的`kn`选项相同，`caseFirst`与`kf`选项相同。`localeMatcher`选项指定要使用的语言环境匹配算法。可能的值是`lookup`和`best fit`。查找算法搜索区域设置，直到找到符合正在比较的字符串的字符集的区域设置。`best fit`查找可能比`lookup`算法更适合的语言环境。

`usage`选项指定排序器是用于排序还是搜索字符串。默认选项是`sort`。

敏感度选项指定比较字符串的方式。可能的选项有`base`、`accent`、`case`和`variant`。`base`比较字母的基数，忽略重音。比如`a`和`b`不一样，`a`和`á`一样，`a`和`Ä`T35 一样。 `accent`指定字符串只有在基本字母或重音不同时才不同，忽略大小写。所以，`a`不等同于`b`，而`a`等同于`A`。`a`和`á`不一样。

`case`选项指定基本字母或大小写不同的字符串被认为是不相等的，因此`a`不会与`A`相同，`a`不会与`c`相同，但是`a`与`á`相同。`variant`表示具有不同基本字母、重音符号、其他标记或大小写的字符串被视为不相等。例如，`a`不会与`A`相同，`a`不会与`c`相同，但`a`也不会与`á`相同。

`ignorePunctuation`指定排序字符串时是否应忽略标点符号。这是一个布尔属性，默认值是`false`。

## String.match()

`match`方法在给定要匹配的正则表达式的情况下检索字符串的子字符串匹配。`match`方法将正则表达式作为参数。它可以是正则表达式文字，也可以是`RegExp`对象。如果没有给定正则表达式，我们将得到一个空字符串数组。如果正则有`g`标志，那么一个字符串中的所有匹配都将被返回到数组中。否则，将只返回第一个。

匹配项作为数组返回。例如，我们可以使用`i`标志进行不区分大小写的搜索:

```
const str = 'foo';
const re = /foo/i;
const found = str.match(re);
console.log(found);
```

如果我们运行上面的代码，我们会得到:

```
[
  "foo"
]
```

从`console.log`的声明上面来看。

如果我们打开和关闭`g`标志，我们会得到不同的结果。如果正则有`g`标志，那么一个字符串中的所有匹配都将被返回到数组中。否则，将只返回第一个。如果我们打开了`g`标志，如下面的代码所示:

```
const str = 'The quick brown fox jumps over the lazy dog. It barked.';
const globalRe = /[A-Z]/g;
const globalFound = str.match(globalRe);
console.log(globalFound);
console.log(JSON.stringify(globalFound, (key, value) => value, 2));
```

然后我们得到:

```
[
  "T",
  "I"
]
```

从上面的`console.log`语句来看。但是，如果我们删除了`g`标志，如下面的代码所示:

```
const re = /[A-Z]/;
const found = str.match(re);
console.log(found);
console.log(JSON.stringify(found, (key, value) => value, 2));
```

然后我们得到:

```
[
  "T"
]
```

从上面的`console.log`语句来看。

## String.matchAll()

`matchAll`方法将返回通过向`matchAll`方法的参数传递正则表达式找到的所有结果。它返回一个迭代器，将结果的数组作为数组条目。例如，如果我们使用以下代码进行全局搜索:

```
const str = 'The quick brown fox jumps over the lazy dog. It barked.';
const globalRe = /[A-Z]/g;
const globalFound = [...str.matchAll(globalRe)];
console.log(globalFound);
console.log(JSON.stringify(globalFound, (key, value) => value, 2));
```

然后我们得到:

```
[
  [
    "T"
  ],
  [
    "I"
  ]
]
```

另一方面，如果正则表达式中没有全局标志来进行全局搜索，如下面的代码所示:

```
const re = /[A-Z]/;
const found = [...str.matchAll(re)];
console.log(found);
console.log(JSON.stringify(found, (key, value) => value, 2));
```

然后我们得到:

```
[
  [
    "T"
  ]
]
```

从`console.log`的声明上面来看。

字符串是 JavaScript 中一个重要的全局对象。它们代表一系列字符。有一些方法可以操作字符串，在不同的位置查找字符串的各个部分，以及修剪和替换字符串。随着 JavaScript 规范的每一次发布，都会添加更多的方法来使字符串搜索和操作比以往更加容易。请继续关注名单的第二部分。
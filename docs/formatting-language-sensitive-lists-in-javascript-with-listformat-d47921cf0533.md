# 用 ListFormat 在 JavaScript 中格式化区分语言的列表

> 原文：<https://betterprogramming.pub/formatting-language-sensitive-lists-in-javascript-with-listformat-d47921cf0533>

## 轻松设置区域设置、类型和样式

![](img/f361f1936719c3555c2cfaaafacacebf.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

使用`Intl.ListFormat`构造函数，我们可以轻松格式化语言敏感列表。它让我们设置格式化列表的语言环境。此外，它还允许我们设置格式化列表的选项，如列表的类型或样式。为了使用它，我们可以用`Intl.ListFormat`构造函数创建一个新的`ListFormat`对象。

构造函数最多接受两个参数。第一个参数是要设置列表格式的区域设置字符串。第二个参数接受一个带有格式化和样式化列表选项的对象。`Intl.ListFormat`构造函数的一个例子是:

```
const arr = ['bus', 'car', 'train'];
const formatter = new Intl.ListFormat('en', {
  style: 'long',
  type: 'conjunction'
});
const formattedList = formatter.format(arr);
console.log(formattedList);
```

如果我们运行上面的代码，我们会看到从`console.log`语句中记录的*公共汽车*、*汽车*、*和* *火车*。`Intl.ListFormat`构造函数创建一个 formatter 对象，该对象具有`format`方法，根据我们设置的区域设置和格式选项将数组转换成列表。

在上面的例子中，我们将英语的区域设置为`en`，将`style`属性设置为`long`，这将字符串数组格式化为类似于 *A* 、 *B* 、*和 C* 或 *A、B 或 C* 的格式。属性指定了我们想要格式化的列表类型。合取意味着我们将一个列表格式化为 *A、B 和 C* 。

构造函数的参数是要将列表格式化成的区域设置。它可以是一个字符串或字符串数组，包含要格式化的列表的区域设置。区域设置字符串应该是 BCP 47 语言标记。`locales`参数是可选的。BCP-47 语言标签的简略列表包括:

```
ar — Arabicbg — Bulgarianca — Catalanzh-Hans — Chinese, Han (Simplified variant)cs — Czechda — Danishde — Germanel — Modern Greek (1453 and later)en — Englishes — Spanishfi — Finnishfr — Frenchhe — Hebrewhu — Hungarianis — Icelandicit — Italianja — Japaneseko — Koreannl — Dutchno — Norwegianpl — Polishpt — Portugueserm — Romanshro — Romanianru — Russianhr — Croatiansk — Slovaksq — Albaniansv — Swedishth — Thaitr — Turkishur — Urduid — Indonesian
```

构造函数的第二个参数是一个对象，它允许我们设置如何格式化列表字符串的选项。这个对象有三个属性:`localeMatcher`、`type`和`style`。`localeMatcher`选项指定要使用的区域匹配算法。可能的值是`lookup`和`best fit`。

`lookup`算法搜索语言环境，直到找到符合正在比较的字符串字符集的语言环境。

`best fit`查找至少与`lookup`算法一样适合，但可能更适合的语言环境。

属性可以有两个可能的值:`conjunction`、`disjunction`或`unit`。`conjunction`表示列表由一个*和*连接，如 *A、B 和 C* 。这是默认选项。`disnjunction`表示列表由*或*连接，如 *A、B 或 C* 。

`unit`代表带有单位的数值列表。

`style`属性指定格式化消息的长度。该属性有两个可能的值。既可以是`long`(如 *A、B、C* )，`short`(如 *A、B、C* )，也可以是`narrow`(如 *A B C* )。当`style`为`short`或`narrow`时，`unit`是该选项唯一允许的值。

例如，我们可以用它将一个列表格式化成一个字符串，这个字符串在末尾用一个*和*连接起来。为此，我们可以编写以下代码:

```
const arr = ['bus', 'car', 'bicycle'];
const formatter = new Intl.ListFormat('en', {
  style: 'long',
  type: 'conjunction'
});
const formattedList = formatter.format(arr);
console.log(formattedList);
```

如果我们像上面那样记录`formattedList`常量，我们得到*公共汽车、汽车和自行车*。我们设置`en`语言环境来格式化英语语言环境的字符串，样式为`long`并作为一个连接词，这意味着列表将在末尾用一个*和*连接。如果我们想获得一个基于*或*的列表，我们可以将`type`属性改为`disjunction`，如下面的代码所示:

```
const arr = ['bus', 'car', 'bicycle'];
const formatter = new Intl.ListFormat('en', {
  style: 'long',
  type: 'disjunction'
});
const formattedList = formatter.format(arr);
console.log(formattedList);
```

如果我们运行上面的代码，那么我们会在上面的`console.log`语句中记录*公共汽车、汽车或自行车*。

我们可以通过使用`style`属性的`short`或`narrow`选项将其转换成一个更短的列表。例如，我们可以写:

```
const arr = ['bus', 'car', 'bicycle'];
const formatter = new Intl.ListFormat('en', {
  style: 'short',
  type: 'conjunction'
});
const formattedList = formatter.format(arr);
console.log(formattedList);
```

然后，当我们运行上面的代码时，在`console.log`输出中得到*公共汽车、汽车、&自行车*。`short`和`disjunction`组合与`long`和`disjunction`组合相同。如果我们写:

```
const arr = ['bus', 'car', 'bicycle'];
const formatter = new Intl.ListFormat('en', {
  style: 'short',
  type: 'disjunction'
});
const formattedList = formatter.format(arr);
console.log(formattedList);
```

然后我们乘公共汽车、小汽车或自行车。`narrow`选项会使它更短。例如，如果我们把:

```
const arr = ['bus', 'car', 'bicycle'];
const formatter = new Intl.ListFormat('en', {
  style: 'narrow',
  type: 'conjunction'
});
const formattedList = formatter.format(arr);
console.log(formattedList);
```

然后，当我们运行上面的代码时，我们在`console.log`中记录了*公共汽车、汽车、自行车*。

这也适用于非英语语言环境。例如，如果我们想将一个中文字符串列表格式化成一个列表，我们可以编写下面的代码:

```
const arr = ['日', '月', '星'];
const formatter = new Intl.ListFormat('zh-hant', {
  style: 'narrow',
  type: 'conjunction'
});
const formattedList = formatter.format(arr);
console.log(formattedList);
```

Then we get ‘日、月和星’, which means *sun, moon, and stars*. If we switch the `style` option to `long` or `short`, we would get the same thing because in Chinese there’s only one way to write a conjunction, unlike in English. `disjunction` also works with Chinese. For instance, if we have:

```
const arr = ['日', '月', '星'];
const formatter = new Intl.ListFormat('zh-hant', {
  style: 'long',
  type: 'disjunction'
});
const formattedList = formatter.format(arr);
console.log(formattedList);
```

Then we get ‘日、月或星’ which means *sun, moon, or stars*. If we switch the `style` option to `long` or `short`, as with Chinese conjunctions, we would get the same thing because in Chinese there’s only one way to write a disjunction, unlike in English.

# formatToParts()方法

除了`format`方法之外，`Intl.ListFormat`实例还有`formatToParts`方法，它将一个数组格式化为一个合取或析取字符串，然后将其作为格式化字符串的一个数组返回。

例如，如果我们想为一个列表返回一个格式化的英文字符串部分的数组，那么我们可以编写下面的代码:

```
const arr = ['bus', 'car', 'bicycle'];
const formatter = new Intl.ListFormat('en', {
  style: 'long',
  type: 'conjunction'
});
const formattedList = formatter.formatToParts(arr);
console.log(formattedList);
```

然后我们得到:

```
[
  {
    "type": "element",
    "value": "bus"
  },
  {
    "type": "literal",
    "value": ", "
  },
  {
    "type": "element",
    "value": "car"
  },
  {
    "type": "literal",
    "value": ", and "
  },
  {
    "type": "element",
    "value": "bicycle"
  }
]
```

从`console.log`语句。这些是我们用`format`方法得到的格式化字符串的一部分，但是被分解成单独的条目。如果我们只需要格式化字符串的某些部分，这个方法很方便。

使用`Intl.ListFormat`构造函数，格式化语言敏感列表很容易。构造函数将一个区域设置字符串或区域设置字符串数组作为第一个参数，将一个带有某些选项的对象作为第二个参数。我们可以用`Intl.ListFormat`实例的`format`方法将一个数组转换成一个字符串，该字符串将列表格式化为合取或析取，该方法接受一个地区字符串和格式化字符串的长度样式的选项，以及它是合取、析取还是单位字符串。它还有一个`formatToParts`方法将它转换成一个格式化的列表，然后将各个部分分解成一个数组。
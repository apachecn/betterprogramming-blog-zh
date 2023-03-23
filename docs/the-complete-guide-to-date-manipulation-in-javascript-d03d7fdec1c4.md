# JavaScript 中日期操作的完整指南

> 原文：<https://betterprogramming.pub/the-complete-guide-to-date-manipulation-in-javascript-d03d7fdec1c4>

## 了解如何在 JavaScript 中处理日期和时间

![](img/37db0fd1359160ff56c9dccc23934b48.png)

作者照片。

理论上，作为开发人员处理日期就像创建、存储和操作日期一样简单。但是作为一名 JavaScript 开发人员，在您真正开始处理日期之后，您就会知道这个理论并不成立。除了不同的日期时间格式，您还必须考虑时区和地区差异。

由于这个原因，许多 JavaScript 开发人员在管理应用程序中的日期时会向第三方库寻求帮助。虽然这些库降低了任务的复杂性，但是清楚地理解如何处理普通的 JavaScript 日期也有它的好处。

本教程将向您介绍如何使用普通 JavaScript 和有用的第三方库来处理日期，以帮助您简化更复杂的日期相关任务。

# JavaScript 日期对象

JavaScript 中的`Date`对象是处理日期和时间的主要元素。它记录自 1970 年 1 月 1 日 00:00:00 (UTC)以来经过的单个时间点，以毫秒为单位。这个日期时间组合被称为纪元时间。就 JavaScript 而言，它是世界时间的开端。

# 创建日期

您可以使用`new Date()`简单地创建一个日期。您可以将参数传递给`Date`构造函数来创建您选择的日期。给定的参数可以采取不同的形式。

# 传递日期字符串

创建新的`Date`对象时，可以传递一个可接受格式的`date`字符串:

```
const date = new Date("2020-12-31");
```

现在，如果我们打印创建日期，它显示如下:

```
Thu Dec 31 2020 01:00:00 GMT+0100 (Central European Standard Time)
```

除了我们传递的日期之外，`Date`对象还有更多值，包括时间和时区。因为我们在创建对象时没有给这些参数一个特定的值，所以 JavaScript 使用代码系统的本地时间和时区。

如果我们想用参数字符串传递时间或时区，我们可以使用如下格式:

```
YYYY-MM-DDTHH:mm:ss.sssZ
```

*   `YYYY`:年份
*   `MM`:月份(1 到 12)
*   `DD`:日期(1 到 31)
*   `HH`:24 小时制的小时(0 到 23)
*   `mm`:分钟(0 到 59)
*   `ss`:秒(00 到 59)
*   `sss`:毫秒(0 到 999)
*   `T`用于分隔字符串中的日期和时间。
*   如果`Z`存在，则假定时间为 UTC。否则，它采用当地时间。

但是，如果`T`和`Z`不存在，字符串的创建日期可能会在不同的浏览器中给出不同的结果。在这种情况下，要使日期始终具有相同的时区，请在末尾添加`+HH:mm`或`-HH:mm`:

使用`Date.parse`函数可以得到相同的结果，而不是将`date`字符串传递给`Date`构造函数。每当您传递一个`date`字符串时，`Date.parse`就会在构造函数内部被间接调用。

这些字符串中使用的格式是 ISO 8601 日历扩展格式。您可以在 [ECMAScript 规范](https://tc39.es/ecma262/#sec-date-time-string-format)中查看其详细信息。

# 传递日期参数

您可以直接将`Date`参数传递给`Date`构造函数，而不用使用令人困惑的`date`字符串。每年、每月等的顺序和长度。与`date`字符串中的完全相同:

```
let newDate = new Date(1998, 9, 30, 13, 40, 05);
```

当我们检查创建日期的结果时，我们可以注意到最终日期中的一个关键差异:

```
Fri Oct 30 1998 13:40:05 GMT+0100 (Central European Standard Time)
```

什么很奇怪？当我们创建日期时，我们使用`9`表示月份，我们可以假设是九月。然而，当我们打印结果时，月份却是 10 月。这是为什么呢？

JavaScript 使用从零开始的索引来标识一年中的每个月。这意味着，对于 JavaScript，一月由`0`而不是`1`表示。同样，十月用`9`表示，而不是用`10`表示。

在这个创建日期的方法中，我们不能传递一个参数来指示它的时区。因此，它默认为系统的本地时间。但是我们可以使用`Date.UTC`函数在将日期传递给`Date`构造函数之前将其转换为 UTC:

```
newDate = new Date(Date.UTC(1998, 09, 30, 13, 40, 05))
// Fri Oct 30 1998 14:40:05 GMT+0100 (Central European Standard Time)
```

# 传递时间戳

还记得我提到的 JavaScript 在`Date`对象中存储从纪元时间开始的时间吗？我们可以传递这个经过的时间值(称为时间戳)来指示我们正在创建的日期:

```
newDate = new Date(1223727718982)
// Sat Oct 11 2008 14:21:58 GMT+0200 (Central European Summer Time)
```

# 为当前日期和时间创建一个 Date 对象

如果您想为系统的当前日期和时间创建一个`Date`对象，请使用`Date`构造函数，不要传递任何参数:

```
let now = new Date()
// Sat Jan 09 2021 22:06:33 GMT+0100 (Central European Standard Time)
```

您也可以使用`Date.now()`功能完成相同的任务:

```
now = Date.now()
```

# 格式化日期

JavaScript 提供了几个内置函数来格式化日期。但是，这些函数只将日期转换为特定于每个日期的格式。

让我们看看每个格式化函数是如何工作的:

# 国际化 API

ECMAScript 的[国际化 API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl) 允许使用`Intl`对象将日期格式化为特定的语言环境:

您可以向`DateTimeFormat`函数传递一个`options`对象来显示时间值并定制输出:

# 自定义日期格式

如果您想将日期格式化为这些函数提供的格式之外的任何其他格式，您必须通过分别访问日期的每个部分并组合它们来实现。

JavaScript 提供了以下函数来从`Date`对象中检索年、月、日和日:

现在，您可以使用检索到的部分将日期转换为自定义格式。

# 更新日期

JavaScript 提供了几种方法来编辑已经创建的日期:

# 比较日期

如果想知道某个特定日期是否在另一个日期之前，可以直接使用大于号和小于号运算符进行比较:

```
let first = new Date(2010, 3, 19)
let second = new Date(2010, 3, 24)first > second      //false
```

然而，如果你想检查它们是否相等，无论是`==`还是`===`操作符都不能按预期工作:

```
first = new Date(2009, 12, 23)
second = new Date(2009, 12, 23)console.log(first == second)  // false
console.log(first === second) // false
```

相反，您必须检索每个日期的时间戳，并比较它们是否相等:

```
first.getTime() === second.getTime() // true
```

这是因为 JavaScript 中的日期是对象，所以每个日期都有一个不同的类实例。`==`或`===`运算符比较的是内存地址，而不是日期的实际值。

# JavaScript 日期操作库

有几个 JavaScript 日期和时间操作库(开源或其他)。其中一些是为各种日期时间操作而设计的，而另一些则有一组特定的用例。在这一节，我只谈流行的多功能库。

[Moment.js](https://momentjs.com/) 曾经是 JavaScript 开发者中的日期操作库之王。然而，[它的开发者最近宣布](https://momentjs.com/docs/#/-project-status/recommendations/)他们正专注于维护当前的代码基础，而不是增加新的特性。他们建议那些从事新项目的人寻找一个替代方案。

那么，除了 Moment.js，还有哪些库可以让我们作为开发者的生活变得更轻松呢？

## 日期-fns

[Date-fns](https://date-fns.org/) 在一个开源库中，支持日期解析和格式化、区域设置以及像加法和减法这样的日期算法。由于它的多功能性，它被称为枣椰。

```
const datefns = require("date-fns");let date = datefns.format(new Date(2017, 09, 12), "dd MMM yyyy");
date = datefns.format(new Date(2017, 09, 12), "dd.MM.yy");
```

正如您所看到的，通过传递一个简单的格式字符串，您可以很容易地将日期转换成您喜欢的格式。

它还允许我们轻松地添加和减去日期:

## 国际光子

[Luxon](https://github.com/moment/luxon) 是一个日期时间操作库，由 Moment.js 开发人员创建，以满足现代应用程序的需求。与 Date-fns 类似，Luxon 提供数据格式化和解析功能。此外，它有原生的`Intl`支持，并且是可链接的。

```
let date = DateTime.local(2019, 08, 12)console.log(date.toLocaleString(DateTime.DATETIME_FULL))
console.log(date.toLocaleString(DateTime.DATETIME_MED))
```

您还可以测量两个日期之间的时间间隔:

```
let now = DateTime.local()
let later = DateTime.local(2021, 03, 12)console.log(Interval.fromDateTimes(now, later).length('years'))
```

# 摘要

本教程讨论了在有外部库和没有外部库的情况下，如何在 JavaScript 中处理日期和时间。在几乎所有(如果不是全部)编程语言中，处理日期都是痛苦的。幸运的是，JS 和它的库生态系统为我们做了所有繁重的工作，让我们可以专注于构建特性。

感谢阅读！
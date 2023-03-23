# JavaScript 中日期和时间的未来

> 原文：<https://betterprogramming.pub/the-future-of-date-and-time-in-javascript-e1772c7976c>

## 新的时态 API 可以在不久的将来消除对第三方库的需求

![](img/ed28c8e62dfbc8ab4ae06bdeb01d5192.png)

凯文·Ku 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

大多数 JavaScript 开发人员都遇到过一个长期的棘手问题，那就是`Date`对象。尝试做除了最简单的日期操作之外的任何事情都会让你很快接触到第三方库，比如 [Moment.js](https://momentjs.com/) 或一长串为减轻这种痛苦而开发的其他库。

在 Maggie Pint 的博客文章中，她概述了 T1 物体的历史以及它是如何形成的。早在 1995 年，Brendan Eich 有十天的时间来创建 JavaScript 并将其放入 Netscape。有人可能会说十天时间足够了——毕竟，有人说上帝在六天内创造了天地，但在编程领域，在十天内创造一门新语言确实是一项艰巨的任务。

根据命令，布兰登被告知要“让它像 Java 一样”，因此复制了现有的`java.Util.Date`实现。不幸的是，对于 JavaScript 社区来说，这种实现非常糟糕，早在 1997 年的 Java 1.1 中，它的大部分就被弃用和替换了。然而，20 多年后，JavaScript 社区仍然与 API 共存。

多年来，我在需要的时候求助于 Moment.js(双关语)，从 npm 的统计数据来看，它的周下载量超过了 1200 万，和以前一样受欢迎。有一段时间，我切换到 [Day.js](https://day.js.org/en/) 以获得不可变 API 的好处，但是缺乏对时区的内置支持迫使我再次切换，现在 [Luxon](https://moment.github.io/luxon/) 是城里的新库。有了可链接的、不可变的、方便的 API 和对时区的适当支持，在 JavaScript 中处理日期和时间不再像过去那样痛苦。

在过去的几年中，我们已经看到 JavaScript 为以前需要外部库的东西添加了本地支持——例如，使用以前需要像 [bluebird](http://bluebirdjs.com/) 这样的库的承诺。现在看来，在不太遥远的将来，可能也会有一个日期和时间的无图书馆体验。

在一份新的[规范草案](https://tc39.es/proposal-temporal/)中，技术委员会 TC39 已经将重点放在解决`Date`对象的许多问题上。新提案旨在解决时区支持、不可靠的解析器、可变的日期、笨拙的 API 以及当今存在的一系列其他问题。有些问题，比如时区支持，可以通过用新方法扩展`Date`对象来解决。

然而，如果不在 JavaScript 中引入突破性的变化，可变性和解析就无法修复，这是 TC39 努力避免的，因为它可能会破坏现有的网站。他们没有修复现有的`Date`对象，而是提出了一个全新的`Temporal` API，旨在解决`Date`的许多缺陷，同时也引入了许多新的便捷功能。

该提案目前处于第二阶段，根据 [TC39 流程](https://tc39.es/process-document/)，这意味着:“委员会期望该特性得到开发并最终包含在标准中。”

然而，该提案仍然是一个草案，突破性的变化可能会被引入作为正在进行的审查过程的一部分。本文将着眼于截至今天(2020 年 7 月)提案中定义的 API，您应该意识到，从现在到这个 API 最终应用到浏览器之间，事情可能会发生变化。

废话少说，让我们看看代码吧！

这篇文章仅仅触及了新提议的表面，并强调了`Date`的一些缺陷是如何被解决的。这个提议增加了一堆额外的特性，我强烈建议您看一下[文档](https://tc39.es/proposal-temporal/docs/index.html)以获得所有类和方法的详尽列表。

# 日期时间与绝对时间

旧的`Date`对象表示时间上的单个时刻，并在内部存储为一个`Number`，表示从 UTC 的 1970 年 1 月 1 日开始的毫秒数，也称为 [Unix 纪元](https://en.wikipedia.org/wiki/Unix_time)。在新的提议中，一个单独的时刻被称为`Temporal.Absolute`。根据[文档](https://tc39.es/proposal-temporal/docs/absolute.html):

> “A `Temporal.Absolute`是绝对时间点，精度以纳秒为单位。没有时区或日历信息。因此`Temporal.Absolute`没有日、月甚至小时的概念。”

伴随`Temporal.Absolute`的是`Temporal.DateTime`，其在[文档](https://tc39.es/proposal-temporal/docs/datetime.html)中定义为:

> `Temporal.DateTime`代表日历日期和挂钟时间，精确到纳秒，没有任何时区。

如您所见，新的 API 区分了日常使用中时间概念的表达方式和绝对时间点。`Temporal.DateTime`描述日历中的日期和时钟上显示的时间，但是，根据您所处的位置，完全相同的日历日期和时钟时间可能指不同的时间点`Temporal.Absolute`(基于您所在的时区)。这让我们想到了提案中的另一个新概念，即`Temporal.TimeZone`，在[文档](https://tc39.es/proposal-temporal/docs/timezone.html)中描述为:

> `Temporal.TimeZone`是时区的表示:或者是 [IANA 时区](https://www.iana.org/time-zones)，包括关于时区的信息，例如在特定时间本地时间和 UTC 之间的偏差，以及夏令时(DST)的变化；或者只是没有夏令时的特定 UTC 时差。

由于`Temporal.DateTime`和`Temporal.Absolute`都不包含任何时区信息，因此需要一个`Temporal.TimeZone`对象在两者之间进行转换。

```
// Create a DateTime
const datetime = Temporal.DateTime.from('2020-07-20T13:37')// Create a TimeZone to use for convertion
const tz = new Temporal.TimeZone('Europe/Stockholm')// Convert to Absolute
datetime.toAbsolute(tz) // => Temporal.Absolute <2020-07-20T11:37Z>
```

显式定义时区，而不是像`Date`那样隐式使用主机系统的本地时区，应该可以消除`Date`对象的一些不确定性和混乱。将`DateTime`和`Absolute`分开也会使日期的处理和操作变得更加容易，稍后我们会看到更多。

# 日期和时间

在提议中，`Temporal.DateTime`是携带人类可读时间信息的类中最通用和最完整的，因为它包含了精确的日期和时间。然而，当不需要完整信息时，还有一些只携带部分信息的类是有用的。`Temporal.Date`、`Temporal.Time`、`Temporal.YearMonth`和`Temporal.MonthDay`都比`Temporal.DateTime`携带的信息少。

例如，`Temporal.MonthDay`可以用来表示每年重复发生的事件。然后可以通过添加年份部分将其转换为`Temporal.Date`。

```
const christmasEve = new Temporal.MonthDay(12, 24)christmasEve.toDateInYear(2020) // => Temporal.Date <2020-12-24>
christmasEve.toDateInYear(2030) // => Temporal.Date <2030-12-24>
```

# 不变

如果不小心使用，`Date`对象的可变性很容易造成意想不到的意外:

```
const christmas = new Date(’2020–12–24’)
const nextChristmas = new Date(christmas.setYear(2021))// We've modified the initial object by accident
christmas.toISOString() // => 2021-12-24T00:00:00Z
```

我们想通过简单地改变年份来创建一个新的带有 next Christmas 的`Date`对象，但是同时，我们修改了初始实例，因为`Date`对象是可变的。此外，JavaScript 中的对象是通过引用传递的，而作为一个对象，`Date`当您开始将`Date`实例作为参数传递给其他函数时，您可能会非常痛苦。你传递给你的`Date`的任何函数都有可能在你不知道的情况下改变你的日期，这反过来会引入讨厌的难以发现的错误。

新的`Temporal` API 是不可变的，这意味着所有方法都返回一个新的对象，而不是改变现有的对象。使用新的 API 执行上述示例的等效操作是安全的。

```
const christmas = Temporal.DateTime.from('2020–12–24')
const nextChristmas = christmas.with({ year: 2021 })christmas.toString() // => 2020-12-24T00:00:00Z
nextChristmas.toString() // => 2021-12-24T00:00:00Z
```

即使我们使用`christmas`变量作为起点，`.with()`方法也将返回一个新的`DateTime`对象，保持原来的`christmas`变量不变。

# 计算

试图修改一个`Date`对象一直是一个丑陋而繁琐的操作。在今天的日期上增加一天会迫使你这样做:

```
// Create Date
const date = new Date()

// Add a day by setting the date to the current date + 1
date.setDate(date.getDate() + 1) 
```

不太优雅。幸运的是，`Temporal` API 使这变得更容易，可读性更强:

```
const tomorrow = Temporal.now.dateTime().plus({ days: 1 })
```

与`Date`对象的方法相比，新的`.plus()`和`.minus()`方法使得日期和时间的计算更加一致。

`.plus()`和`.minus()`方法都接受一个`Temporal.Duration`对象，这是`Temporal`引入的另一个新概念。[文档](https://tc39.es/proposal-temporal/docs/duration.html)对`Temporal.Duration`的描述如下:

> "一个`Temporal.Duration`代表一个不可变的持续时间，可以用在日期/时间算法中."

一个`Temporal.Duration`可以被声明为一个对象，因此可以包含多个键。在前一个示例的基础上，向今天的日期添加一年零一天也同样简单:

```
const future = Temporal.now.dateTime().plus({ years: 1, days: 1 })
```

新的 API 还提供了一个`.with()`方法，可用于覆盖日期时间的某些部分，而不是加减:

```
const future = Temporal.now.dateTime().with({ year: 2050 })
```

有了这三种方法，日期和时间的计算就方便多了。这个 API 既可读又好记，很像 Moment.js 等流行库的 API。

如果你想试验一下`Temporal` API，你现在可以通过 npm 使用官方的 [polyfill](https://github.com/tc39/proposal-temporal/tree/main/polyfill) 。请注意，它不稳定，不建议用于生产。

```
npm install proposal-temporal
```

并在您的代码中使用它:

```
const { Temporal } = require('proposal-temporal')const now = Temporal.now.dateTime()
```

如果你不想开始一个新项目只是为了试验一下，那么[文档](https://tc39.es/proposal-temporal/docs/index.html)默认情况下也会加载 polyfill，所以直接从你的浏览器的 devtools 控制台尝试代码。

如果你和`Temporal`一起玩，TC39 想要你对提议的反馈，并且有一个[调查](https://docs.google.com/forms/d/e/1FAIpQLSeYdvnDZZS6tKn18jiomfN7u_rq-_-_BqOevTzAcfgE3J4kHA/viewform)给你。你也可以在 GitHub 上为你遇到的任何 bug 打开新的[问题。提案越早进入第 3 阶段，浏览器就能越早开始实现本地支持。](https://github.com/tc39/proposal-temporal/issues)

如果你对这篇文章有想法或者想讨论一下`Temporal` API，我很乐意在评论中听到你的意见。

感谢您的阅读！
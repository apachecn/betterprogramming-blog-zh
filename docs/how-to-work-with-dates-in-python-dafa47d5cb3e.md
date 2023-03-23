# 如何在 Python 中处理日期

> 原文：<https://betterprogramming.pub/how-to-work-with-dates-in-python-dafa47d5cb3e>

## 解析、格式化、时区等等

![](img/6e86e557f21d6cc452b11e202b13a603.png)

照片由[丘特尔斯纳普](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Python 是一种很棒的语言，内置了许多易于使用的特性。对于几乎所有的问题，要么有直接语言支持，要么有一个库。易用性和丰富的生态系统使它成为许多人的首选语言。

对于我们这些有使用 Java 和 JavaScript 经验的人来说，在 Python 中处理日期并不直观或容易。我们认为简单的功能很难实现。但并不像看起来那么糟糕。这篇文章将介绍一些处理日期的有用技巧。

# 1.从语法上分析

第一个用例涵盖了字符串形式的日期。我们想把它解析成一个`datetime`对象。Python 有一个内置的方法来解析日期，`[strptime](https://docs.python.org/3/library/datetime.html#strftime-strptime-behavior)`。

一个例子:

```
>>> from datetime import datetime
>>> datetime.strptime("2020-01-01 14:00", "%Y-%m-%d %H:%M")
datetime.datetime(2020, 1, 1, 14, 0)
```

这个例子获取字符串`“2020–01–01 14:00”`，并将其解析为一个`datetime`对象。`strptime`的文档提供了所有格式字符串选项的概述。

Python 还有许多第三方库，使得日期解析更加容易。 [dateutil](https://dateutil.readthedocs.io/en/stable/) 是一个很棒的库，它扩展了 Python 的`datetime`功能。它可以通过 pip 安装，如下所示:

```
pip install python-dateutil
```

安装后，让我们尝试几个例子:

```
>>> import dateutil.parser as parser>>> parser.parse("2020-01-01 14:00")
datetime.datetime(2020, 1, 1, 14, 0)>>> parser.parse("01-01-2020 2:00pm")
datetime.datetime(2020, 1, 1, 14, 0)>>> parser.parse("2020-01-01T14:00")
datetime.datetime(2020, 1, 1, 14, 0)
```

不算太坏…而且很强大。但是`dateutil`在解析日期时并不总是完美的。例如，2020 年 2 月 1 日可能是 2 月 1 日，也可能是 1 月 2 日。但是对于大多数格式。它做得很好。

`dateutil`在解析各种日期格式的非结构化文本时效果最佳。如果日期格式已知，最好使用`strptime`。

# 2.格式化

在这种情况下，我们从一个`datetime`对象开始，想要构建一个格式化的字符串。Python 为此提供了`[strftime](https://docs.python.org/3/library/datetime.html#strftime-strptime-behavior)`方法。

```
>>> from datetime import datetime
>>> date = datetime.strptime("2020-01-01 14:00", "%Y-%m-%d %H:%M")
>>> datetime.strftime(date, "%a %B %d, %Y %I:%M %p")
'Wed January 01, 2020 02:00 PM'
```

# 3.当前

为了生成当前时间，`datetime`内置了`[now](https://docs.python.org/3/library/datetime.html#datetime.datetime.now)`方法。

```
>>> from datetime import datetime
>>> datetime.now()
datetime.datetime(2020, 2, 6, 10, 50, 48, 814047)
```

该方法为当前本地时间构建一个`datetime`对象。另一个常见的用例是将当前时间生成为自 1970 年 1 月 1 日以来的毫秒数(Unix 纪元)。大多数其他语言对此都有一个简单的内置方法，但是 Python 没有这么简单。

```
>>> import time
>>> int(time.time() * 1000)
1581004507234
```

根据服务器的时区，如果当前时间不是 UTC 时间，则可能需要获取 UTC 时间。要始终生成 UTC `datetime`而不管当地时区，请运行以下命令:

```
>>> from datetime import datetime
>>> datetime.utcnow()
datetime.datetime(2020, 2, 6, 15, 56, 12, 86502)
```

请注意，即使现在是 UTC 时间，也不容易知道——这很好地引导我们进入下一个主题。

# 4.时区

Python 中的时区很复杂。内置功能并不那么简单。但值得庆幸的是，有第三方库来拯救我们。

```
pip install pytz
```

pytz 向现有的`datetime`对象添加时区信息。

上面的示例生成一个 UTC 日期，将 UTC 时区附加到该日期，然后将时间转换为美国/东部时间。Python `datetime`方法具有格式化时区的内置功能。基于这个例子…

```
>>> datetime.strftime(date, "%Z")
'UTC'
>>> datetime.strftime(date.astimezone(eastern), "%Z")
'EST'
```

…现在时区信息已经添加到`datetime`对象中，我们可以格式化时区了。

# 5.日期数学

当我们处理日期时，如果我们想得到一个相对于另一个日期的日期呢？例如，如果我们想建立一个从一天前到现在的搜索查询。日期计算是抵消一个日期并构建另一个日期的常用技术。

例子包括:

Python 有一个内置的`[timedelta](https://docs.python.org/3/library/datetime.html#timedelta-objects)`类来帮助偏移日期。上面的例子分别将当前的`datetime`偏移了 10 分钟、10 小时和 10 天。

# 6.日期回文

你可能听说过 2020 年 2 月 2 日是 909 年来第一个真正的日期回文。换句话说，02/02/2020 是同样的向后和向前。让我们用 Python 来验证一下。

```
>>> from datetime import datetime
>>> datetime.strftime(datetime(2020, 2, 2, 2, 2, 2), "%Y%m%d")
'20200202'
>>> datetime.strftime(datetime(2020, 2, 2, 2, 2, 2), "%Y%m%d")[::-1]
'20200202'
```

故事核实了。

# 结论

虽然不那么简单，Python 具有 Java 和 JavaScript 等语言中的所有相同功能。

我们只需要知道去哪里找。
# 用 DateTimeFormat 对象设置日期格式

> 原文：<https://betterprogramming.pub/formatting-dates-with-the-datetimeformat-object-9c808dc58604>

## 看看如何用 Intl 格式化日期。DateTimeFormat 构造函数

![](img/fc6b3cf949eafffc4dce8f429e33898a.png)

朱莉安娜·科佐斯基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

世界上不同的地方有不同的日期格式。为了处理这个问题，JavaScript 有一个`Intl.DateTimeFormat`构造函数，让我们根据不同的地区将日期格式化成不同的格式。

这意味着我们可以为不同的地方设置日期格式，而无需自己操作日期字符串，这让我们的生活变得更加轻松。`Intl.DateTimeFormat`构造函数接受一个地区字符串作为参数。

有了用构造函数构造的`DateTimeFormat`，我们可以使用`format`实例方法，该方法接受一个`Date`对象返回一个日期字符串，该日期字符串的日期格式符合您在`Intl.DateTimeFormat`构造函数中指定的地区。

# 构造函数和格式化方法

要使用`Intl.DateTimeFormat`构造函数，我们可以像下面的例子一样使用它:

```
const date = new Date(2019, 0, 1, 0, 0, 0);
console.log(new Intl.DateTimeFormat('en-US').format(date));
console.log(new Intl.DateTimeFormat('fr-ca').format(date));
console.log(new Intl.DateTimeFormat(['ban', 'de']).format(date));
```

在上面的例子中，我们创建了`Date`对象，然后我们使用了`Intl.DateTimeFormat`构造函数，将一个或多个地区作为字符串传入。

然后，我们通过传入`Date`对象来调用`format`。第一个例子是 log `1/1/2019`，因为美国使用 MM/DD/YYYY 日期格式。

第二个示例将记录`2019–01–01`，因为法裔加拿大人的日期是 YYYY-MM-DD 格式。第三个示例采用了一个数组，其中有多个区域设置，最右边的区域设置是左边区域设置的备用区域设置。

在我们的例子中，由于数组的第一个条目中有一个无效的区域设置字符串`ban`，我们使用德国的日期格式`de`来格式化`Date`对象，所以我们得到`1.1.2019`，因为德国使用 DD。年月日日期格式。

从上面的例子中我们可以看到，`Intl.DateTimeFormat`构造函数接受一个区域设置字符串或一个区域设置字符串数组。它还接受地区字符串的 Unicode 扩展键，这样我们就可以将它们附加到我们的语言标签中。

支持设置编号系统的扩展键`nu`、设置日期格式的日历的`ca`、小时循环的`hc`。

`nu`的可能值可以是`arab`、`arabext`、`bali`、`beng`、`deva`、`fullwide`、`gujr`、`guru`、`hanidec`、`khmr`、`knda`、`laoo`、`latn`、`limb`、`mlym`、`mong`、`mymr`、`orya`、`tamldec`、`telu`、`thai`、`tibt`。

`ca`的可能值包括`buddhist`、`chinese`、`coptic`、`ethiopia`、`ethiopic`、`gregory`、`hebrew`、`indian`、`islamic`、`iso8601`、`japanese`、`persian`、`roc`。

`hc`的可能值包括`h11`、`h12`、`h23`、`h24`。例如，要根据佛教日历格式化日期，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0);
console.log(new Intl.DateTimeFormat('en-US-u-ca-buddhist').format(date));
```

然后我们在`console.log`中得到`1/1/2562`，因为佛教日历的年份是公元前 545 年。

第二个参数接受一个对象，该对象允许我们在其属性中设置各种选项来格式化日期。

`localeMatcher`选项指定要使用的区域匹配算法。可能的值有`lookup`和`best fit`。查找算法搜索区域设置，直到找到适合正在比较的字符串字符集的区域设置。

`best fit`找到至少比`lookup`算法更适合的语言环境。`timeZone`选项让我们设置格式化日期的时区。

最基本的实现只识别 UTC，但其他实现可能识别 IANA 时区数据库中的 IANA 时区名称，如`Asia/Shanghai`、`Asia/Kolkata`、`America/New_York`。

`hour12`选项指定格式是 12 小时制还是 24 小时制。默认值取决于区域设置。它覆盖了第一个参数中的`hc`语言标签。

这是一个`true`或`false`值，其中`true`表示将日期时间格式化为 12 小时制。`hourCycle`选项有可能的值`h11`、`h12`、`h23`、`h24`，并覆盖`hc`语言标签。`hour12`如果指定，优先于该选项。

`formatMatcher`属性指定要使用的匹配算法。可能的值是`basic`和`best fit`。`best fit`是默认值。

要将日期对象匹配到正确的格式，需要以下日期时间子集:

*   `weekday`、`year`、`month`、`day`、`hour`、`minute`、`second`
*   `weekday`、`year`、`month`、`day`
*   `year`、`month`、`day`
*   `year`，`month`
*   `month`，`day`
*   `hour`、`minute`、`second`
*   `hour`，`minute`

`weekday`代表工作日值，可能的值有:

*   `long`(例如`Friday`)
*   `short`(如`Fri`)
*   `“narrow`(如`T`)。请注意，对于某些地区，两个工作日可能具有相同的窄样式。比如`Tuesday`的窄式也是`T`。

`era`代表时代，可能的值有:

*   `long`(例如`Anno Domini`)
*   `short`(例如`AD`)
*   `narrow`(例如`A`)

`year`代表年份。可能的值有:

*   `numeric`(例如`2019`)
*   `2-digit`(例如`19`)

`month`是月份的表示。可能的值有:

*   `numeric`(如`2`)
*   `2-digit`(如`02`)
*   `long`(如`February`)
*   `short`(如`Feb`)
*   `narrow`(如`M`)。请注意，对于某些地区，两个月可能具有相同的窄样式(例如，`May`的窄样式也是`M`)。

`day`代表日子。可能的值有:

*   `numeric`(如`2`)
*   `2-digit`(如`02`)

`hour`是小时的表示。可能的值有`numeric`、`2-digit`。

`minute`是分钟的表示。可能的值有`numeric`、`2-digit`。

`second`是第二种的表征。可能的值有`numeric`、`2-digit`。

`timeZoneName`是时区名称的表示。可能的值有:

*   `long`(如`Pacific Standard Time`)
*   `short`(例如`GMT-8`)

上面每个组件属性的默认值是`undefined`，但是如果所有组件都是`undefined`，那么`year`、`month`和`day`都被假定为`numeric`。

下面是使用这些选项的示例:

```
const date = new Date(2019, 0, 1, 0, 0, 0);
const options = {
  weekday: 'long',
  year: 'numeric',
  month: 'long',
  day: 'numeric'
};
console.log(new Intl.DateTimeFormat('en-ca', options).format(date));
```

在上面的代码中，我们将区域设置为加拿大英语，在`options`对象中，我们将`weekday`设置为`long`、`year`设置为`numeric`、`month`设置为`long`、`day`设置为`numeric`，这样我们就可以获得英文的完整工作日名称、数字形式的年份、全名形式的月份以及数字形式的`day`。

`console.log`将记录“2019 年 1 月 1 日，星期二”。上面的示例可以添加一个时区，如下例所示:

```
const date = new Date(2019, 0, 1, 0, 0, 0);
const options = {
  weekday: 'long',
  year: 'numeric',
  month: 'long',
  day: 'numeric',
  timeZone: 'America/Vancouver',
  timeZoneName: 'long'
};
console.log(new Intl.DateTimeFormat('en-ca', options).format(date));
```

在上面的示例中，我们添加了`timeZone`和`timeZoneLong`，以便显示时区，从而得到“太平洋标准时间 2019 年 1 月 1 日，星期二”。

`Intl.DateTimeFormat`构造函数同样可以很好地处理非英语格式。例如，如果我们想要将日期格式化为中文，我们可以更改区域设置并保持选项与上面的相同，如下面的代码所示:

```
const date = new Date(2019, 0, 1, 0, 0, 0);
const options = {
  weekday: 'long',
  year: 'numeric',
  month: 'long',
  day: 'numeric',
  timeZone: 'America/Vancouver',
  timeZoneName: 'long'
};
console.log(new Intl.DateTimeFormat('zh-Hant', options).format(date));
```

Then, we get “2019 年 1 月 1 日 星期二 太平洋標準時間”, which is the same as “Tuesday, January 1, 2019, Pacific Standard Time” in traditional Chinese.

# 其他实例方法

除了`format()`方法之外，`Intl.DateTimeFormat`构造函数对象的实例也有一些实例方法。

它还有`formatToParts()`方法来返回表示日期字符串不同部分的对象数组。`resolvedOptions()`方法返回一个新对象，它的属性反映了我们在对象初始化过程中计算的区域设置和格式选项。

`formatRange()`方法接受两个`Date`对象作为参数，并根据我们实例化`DateTimeFormat`对象时提供的语言环境和选项，以最简洁的方式格式化日期范围。

最后，它有一个`formatRangeToParts()`方法，该方法接受两个`Date`对象作为参数，并根据我们实例化`DateTimeFormat`对象时提供的区域设置和选项，以最简洁的方式格式化日期范围，并返回对象数组中的日期时间部分。

例如，如果我们有以下调用`formatRangeToParts()`方法的代码:

```
const startDate = new Date(2019, 0, 1, 0, 0, 0);
const endDate = new Date(2019, 0, 2, 0, 0, 0);
const options = {
  weekday: 'long',
  year: 'numeric',
  month: 'long',
  day: 'numeric',
  timeZone: 'America/Vancouver',
  timeZoneName: 'long'
};
const dateRange = new Intl.DateTimeFormat('zh-Hant', options).formatRangeToParts(startDate, endDate)
console.log(dateRange);
```

然后，我们记录了以下日期和时间部分:

```
[
  {
    "type": "year",
    "value": "2019",
    "source": "startRange"
  },
  {
    "type": "literal",
    "value": "年",
    "source": "startRange"
  },
  {
    "type": "month",
    "value": "1",
    "source": "startRange"
  },
  {
    "type": "literal",
    "value": "月",
    "source": "startRange"
  },
  {
    "type": "day",
    "value": "1",
    "source": "startRange"
  },
  {
    "type": "literal",
    "value": "日 ",
    "source": "startRange"
  },
  {
    "type": "weekday",
    "value": "星期二",
    "source": "startRange"
  },
  {
    "type": "literal",
    "value": " ",
    "source": "startRange"
  },
  {
    "type": "timeZoneName",
    "value": "太平洋標準時間",
    "source": "startRange"
  },
  {
    "type": "literal",
    "value": " – ",
    "source": "shared"
  },
  {
    "type": "year",
    "value": "2019",
    "source": "endRange"
  },
  {
    "type": "literal",
    "value": "年",
    "source": "endRange"
  },
  {
    "type": "month",
    "value": "1",
    "source": "endRange"
  },
  {
    "type": "literal",
    "value": "月",
    "source": "endRange"
  },
  {
    "type": "day",
    "value": "2",
    "source": "endRange"
  },
  {
    "type": "literal",
    "value": "日 ",
    "source": "endRange"
  },
  {
    "type": "weekday",
    "value": "星期三",
    "source": "endRange"
  },
  {
    "type": "literal",
    "value": " ",
    "source": "endRange"
  },
  {
    "type": "timeZoneName",
    "value": "太平洋標準時間",
    "source": "endRange"
  }
]
```

正如我们从上面的控制台输出中看到的，我们在数组的每个条目中获得了日期和时间部分，其中`startDate`部分在前，`endDate`部分在数组的后面。

如果我们调用`formatRange()`方法，如下面的代码所示:

```
const startDate = new Date(2019, 0, 1, 0, 0, 0);
const endDate = new Date(2019, 0, 2, 0, 0, 0);
const options = {
  weekday: 'long',
  year: 'numeric',
  month: 'long',
  day: 'numeric',
  timeZone: 'America/Vancouver',
  timeZoneName: 'long'
};
const dateRange = new Intl.DateTimeFormat('en', options).formatRange(startDate, endDate)
console.log(dateRange);
```

然后我们得到:

```
"Tuesday, January 1, 2019, Pacific Standard Time – Wednesday, January 2, 2019, Pacific Standard Time"
```

从`console.log`开始。

为了调用`resolvedOptions`方法，我们可以编写下面的代码:

```
const date = new Date(2019, 0, 1, 0, 0, 0);
const options = {
  weekday: 'long',
  year: 'numeric',
  month: 'long',
  day: 'numeric',
  timeZone: 'America/Vancouver',
  timeZoneName: 'long'
};
const resolvedOptions = new Intl.DateTimeFormat('en', options).resolvedOptions(date)
console.log(resolvedOptions);
```

然后我们得到我们传入的选项来格式化日期:

```
{
  "locale": "en",
  "calendar": "gregory",
  "numberingSystem": "latn",
  "timeZone": "America/Vancouver",
  "weekday": "long",
  "year": "numeric",
  "month": "long",
  "day": "numeric",
  "timeZoneName": "long"
}
```

在`console.log`中。

要使用`formatToParts()`方法，我们可以像使用`format()`方法一样使用它，如以下代码所示:

```
const date = new Date(2019, 0, 1, 0, 0, 0);
const options = {
  weekday: 'long',
  year: 'numeric',
  month: 'long',
  day: 'numeric',
  timeZone: 'America/Vancouver',
  timeZoneName: 'long'
};
const dateParts = new Intl.DateTimeFormat('en', options).formatToParts(date)
console.log(dateParts);
```

然后我们在`console.log`中得到格式化日期的部分，如下图所示:

```
[
  {
    "type": "weekday",
    "value": "Tuesday"
  },
  {
    "type": "literal",
    "value": ", "
  },
  {
    "type": "month",
    "value": "January"
  },
  {
    "type": "literal",
    "value": " "
  },
  {
    "type": "day",
    "value": "1"
  },
  {
    "type": "literal",
    "value": ", "
  },
  {
    "type": "year",
    "value": "2019"
  },
  {
    "type": "literal",
    "value": ", "
  },
  {
    "type": "timeZoneName",
    "value": "Pacific Standard Time"
  }
]
```

正如我们所见，`Intl.DateTimeFormat`构造函数对于格式化不同地区的日期非常有用。它消除了格式化日期时的许多麻烦。

构造函数将一个或多个区域设置作为第一个参数，将各种选项作为第二个参数。

它可以将日期格式化成一个字符串，也可以将它们格式化并分解成多个部分。这在格式化不同地区的日期时节省了很多麻烦，因为我们不必做任何日期字符串操作。
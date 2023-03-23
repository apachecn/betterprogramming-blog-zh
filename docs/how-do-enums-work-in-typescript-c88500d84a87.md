# 枚举在 TypeScript 中是如何工作的？

> 原文：<https://betterprogramming.pub/how-do-enums-work-in-typescript-c88500d84a87>

## 解密和掌握类型脚本中的枚举

![](img/5422a7f1e78ae1bdc59191476d363442.png)

[迪昂布莱克](https://unsplash.com/@deonblack?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

枚举，也称为枚举，是定义的常数，可以在代码中的任何地方使用。它们相对容易理解。

# 如何定义枚举？

枚举可以在 Typescript 中使用`enum`关键字来定义。这是我们如何定义一个:

```
enum Languages {
    English,
    Spanish,
    French,
    German
}
```

默认情况下，将为其中的每一项分配一个值。所以 ***英文*** 会是 0， ***西班牙文*** 是 1，以此类推。为了在代码中使用这些值，我们可以通过< EnumName 引用它。属性名>:

```
// Before enum
*console.log*(**1**); // 1// After enum
*console.log*(Languages.Spanish); // 1
```

这样，我们可以通过使用枚举列表来添加上下文。

# 为什么使用枚举？

使用枚举并不是强制性的。但是它们为你的代码提供了语义。你只需要记住一个关键点，当你需要一个预定义的值列表来表示某种数据，可以是数字或字符串，你应该使用枚举。通过使用 so，它为我们正在做的事情添加了更多的上下文。因此，它有助于同行开发人员在浏览代码时快速理解代码。

# 数字枚举

枚举不总是以 0 开头。我们可以在初始化时将枚举定义为数字。让我们稍微详细地看看这些。

```
enum Languages {
    English = **3**,
    Spanish,
    French,
    German
}
```

上面我们已经给了 ***英文*** 一个数值 3。之后的每一项都会加 1，所以 ***西班牙语*** 变成 4， ***法语*** 变成 5，以此类推

此外，您也可以随意定义它们:

```
enum Languages {
    English = **3**,
    Spanish = **10**,
    French = **23**,
    German = **33**
}
```

# 字符串枚举

通常，我们不会在枚举中混合使用字符串和数字，以免混淆。但是，我们也可以将枚举完全定义为字符串:

```
enum TimeZones {
    India = "IST",
    UK = "GMT",
    Ukraine = "EET"
}console.log(TimeZones.India) // IST
```

# 将函数用作枚举值

我们可以用 enums 做更多的事情！它们也可以是一个函数的值。但是有一个问题。如果在枚举中只将一个值定义为函数，函数必须放在所有值的末尾。否则，它将抛出一个错误。

```
const *getTimezoneForIndia* = function() {
    return 'IST';
}enum TimeZones {
    India = *getTimezoneForIndia*(),
    UK,
    Ukraine,
}// Error - UK, Ukraine will return *undefined*
```

之所以出现错误，是因为 TypeScript 希望每个属性都是一个函数值，因为我们将它用于第一个条目。这是另一个例子。当你为最后一个枚举定义一个函数时，我们不会得到一个错误:

```
const *getTimezoneForUkraine* = function() {
    return **'EET'**;
}enum TimeZones {
    India,
    UK,
    Ukraine = *getTimezoneForUkraine*(),
}
```

在上面的枚举中， ***印度*** 返回 0， ***英国*** 返回 1， ***乌克兰*** 返回`getTimezoneForUkraine()`的值。

# 计算枚举

这是另一个有趣的用例。枚举也可以计算，(即任何算术运算都可以用作枚举的值):

```
enum Languages {
    English = **1 + 1**,   // Returns "2"
    Spanish = **1 * 5**,   // Returns "5"
    French  // Returns "6"
}
```

# 引用枚举

还可以引用其他枚举的值，或者引用同一枚举的值。你可以这样做:

```
enum IndianLanguages {
    Hindi: **Languages.English**, // returns Languages.English, i.e. "2"
    Tamil: **1** **+ 5**,         // Calculates and returns "6"
    Malayalam: **1** *** 5**,     // Calculates and returns "5"
    Telugu: **Hindi**         // Refers to AnotherEnum.One, returns "2"
}
```

# 结论

在 TypeScript 中，枚举是向您的代码库添加语义的一种非常强大的方式。它有助于同行开发人员轻松理解您的代码，并了解它的功能。这也有助于可维护性，并有助于扩展，以防将来有任何其他的增值。我希望这篇文章是有用的。

编码快乐！
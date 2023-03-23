# 10 个有用的 Swift 字符串扩展

> 原文：<https://betterprogramming.pub/10-useful-swift-string-extensions-e4280e55a554>

## 电子邮件验证、类型转换等等

![](img/09761fbb20b2f8af1041e7e387715d37.png)

照片由 [Zan](https://unsplash.com/@zanilic?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

`string`是所有编程语言中最基本的数据类型之一。这是编码的一个基本部分，无论是对于一个控制台应用程序，web 服务，基于 GUI 的应用程序，还是一个游戏。Swift 有类`String`，它处理大多数常见的文本操作——大部分但不是全部。

我从 2014 年开始在 Swift 写代码，我几乎不记得有哪个项目没有`String`扩展。这里我分享一下我用的最多的扩展。有些是我写的。其他的，我借用了不同的开放网络资源。所有的例子都适用于 Swift 5，但其中大多数也兼容旧版本。

*如果你也是 Android 开发者，你可能会对我的文章* [*10 个有用的 Kotlin 字符串扩展*](https://medium.com/better-programming/10-useful-kotlin-string-extensions-46772b653f71) *感兴趣。*

![](img/8c5c71fc2ae9cd20aceb45d98996cac9.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 1.MD5 哈希计算器

第一个有用的扩展是计算字符串的 MD5 散列。当您使用 web 服务或检查文件是否正确时，它会很有用。该扩展不使用任何外部依赖项，而是使用本机 CommonCrypto 框架，因此它需要一个桥接头。

MD5-桥接-标头. h

扩展本身相当简单。我假设您希望 MD5 作为一个字符串(例如，将其作为一个参数传递给服务器)。如果需要获取数据，可以少走一步，直接返回 hash 变量。

字符串+md5.swift

如果你想把它添加到一个外部框架中，在`var`之前添加`public`。

## 如何使用它

```
**guard** **let** passwordMD5 = password.md5 **else** {
    showError("Can't calculate MD5 of your password")
    **return** }
```

由于在本例中您不太可能得到`nil`，这种用法也是有意义的:

```
**let** passwordMD5 = password.md5!
```

![](img/682feda71658af50ceabbeec840033a5.png)

照片由[弗拉季斯拉夫·克拉平](https://unsplash.com/@lemonvlad?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 2.本地化字符串

如果你开发一个多语言应用程序，你可能需要在代码中嵌入一些文本字符串，这些字符串将以用户语言显示在屏幕上。编辑故事板或 XIB 文件不需要任何编码，但动态字符串需要。

原生方式是使用函数`NSLocalizedString`。例如:

```
NSLocalizedString("string_id", comment: "")
```

它总是工作，通常不会产生任何问题，但它看起来有点丑。这个扩展是一种句法糖。

String+localized.swift

## 如何使用它

```
"string_id".localized
```

![](img/681b003aced005fa3d1c07aa1d77f43e.png)

照片由 [Nery Montenegro](https://unsplash.com/@neryfabiola_?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 3.整数下标

在任何编程语言中，对字符串的典型操作都涉及到获取它的一部分——或者像程序员所说的，一个*片段。*可以是前几个字符，也可以是后几个字符，也可以是字符串中间的某个部分。

对我来说，Swift 中最令人惊讶的事情之一是获取子字符串(和数组切片——但那是另一个话题)总是有困难。你不能像在许多语言中那样只写一些类似于`"Hello, world"[0...4]`的东西。但是会很舒服，对吧？

那么我们想要什么？

```
**let** str = "Hello, world"
print(str[...4]) // "Hello"
print(str[..<5]) // "Hello"
print(str[7...]) // "world"
print(str[3...4] + str[2]) // "lol"
```

这段代码看起来简单易懂。但是如果你试图编译它，你会得到几个错误:

```
'subscript(_:)' is unavailable: cannot subscript String with an integer range, see the documentation comment for discussion
```

传统上，它有一个解决方案，我们的解决方案是一个`String`类扩展。

它的工作方式几乎和上面的例子完全一样，除了它需要一些类型转换。自然，子字符串返回的是`Substring`的实例，而不是`String`。当你索引一个字符时，它返回一个`Character`的实例。两个都可以投`String`:

```
**let** str = "Hello, world"
**let** strHello = String(str[...4])
**let** strWorld = String(str[7...])
```

![](img/ed60a3bcc9dc5a5d77f9192cfa899c44.png)

Mick Haupt 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 4.检查里面有什么

很多时候我需要检查一个`String`是否只有数字。或者只有数字。例如，用户名通常只包含数字和字母，但密码应该包含更多的字符类型。名或姓只包含字母。

在将输入数据提交给服务器之前，几个助手函数(包装在扩展中)对于输入数据的验证非常有帮助。

该文件包含三种常见的扩展名，但是您可以根据自己的需求为其他字符集编写类似的扩展名。一般规则是获得一个包含所有允许字符的字符集(或几个字符集的联合)。然后反转这个字符集，并检查字符串是否包含其中的任何符号。

## 如何使用它

```
**let** a1 = "12345".containsOnlyDigits // true
**let** a2 = "a12345".containsOnlyDigits // false
**let** b1 = "abcde".containsOnlyLetters // true
**let** b2 = "abcde1".containsOnlyLetters // false
**let** c1 = "abcde12345".isAlphanumeric // true
**let** c2 = "abcde.12345".isAlphanumeric // false
```

![](img/e5fee5d47b46b341b0cc2ade0bc63f4a.png)

照片由 [Kon Karampelas](https://unsplash.com/@konkarampelas?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 5.检查字符串是否是有效的电子邮件地址

谈到输入数据验证，我们不能避免检查电子邮件地址。大多数应用程序要求用户提供电子邮件地址，以进行身份验证、订阅、发送收据和许多其他目的。

我在网上找到了很多解决方法。它们背后的思想都是一样的:我们创建一个正则表达式，并计算包含(或不包含)电子邮件地址的字符串。区别在于正则表达式本身。

电子邮件地址总是包含两个部分:`left@right`，其中*左边的*是您的标识符(例如名称)，而*右边的*是完整的域名。哪个域名是允许的是一个选择的问题。例如，不清楚是否允许使用像`user@localhost`这样的电子邮件。对于大多数用例来说，它不是，但是如果您编写一个供本地使用的控制台应用程序，它可以是一个有效的地址。

这种解决方案适用于大多数情况:

备选版本(*建议由* **import** UIKit
**typealias** UniColor = UIColor
#else
**import** Cocoa
**typealias** UniColor = NSColor
#endif

所有工作都将在`UniColor`扩展内完成。`String`扩展会调用它。标签(#)符号是可选的。颜色可以用三个或六个符号来表示，例如，`#ff0000`、`#f00`、`ff0000`和`f00`是相同的。

## 如何使用它

```
**let** strColor = "#ff0000" // Red color
**let** color = strColor.toColor()
**var** red: CGFloat = 0.0
**var** green: CGFloat = 0.0
**var** blue: CGFloat = 0.0
**var** alpha: CGFloat = 0.0
color?.getRed(&red, green: &green, blue: &blue, alpha: &alpha)print(red, green, blue, alpha)
// 1.0 0.0 0.0 1.0
```

![](img/c940798b25e96f63b0790c2d6c706f92.png)

尼克·希利尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 10.将字符分成组

目前最后一个扩展有助于格式化银行卡号和其他需要分组的数据。它也可以用来对大数字进行分组，但是它有一个特殊的类。其他可能的用途是将固定宽度的电子表格转换成逗号分隔的电子表格、游戏机游戏以及许多其他应用。

这种扩展的思想是在原始字符串的每个 *n* 字符后插入一些字符或字符串。比如我们得到了卡号`1234567890123456`，通常要显示为`1234 5678 9012 3456`。

## 如何使用它

```
**var** cardNumber = "1234567890123456"
cardNumber.insert(separator: " ", every: 4)
print(cardNumber)
// 1234 5678 9012 3456**let** pin = "7690"
**let** pinWithDashes = pin.inserting(separator: "-", every: 1)
print(pinWithDashes)
// 7-6-9-0
```

# 结论

我希望这些扩展能节省您的时间，并帮助您使代码更短更好。

下次见。编码快乐！
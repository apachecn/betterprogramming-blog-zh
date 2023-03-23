# 24 个 Swift 扩展，代码更简洁

> 原文：<https://betterprogramming.pub/24-swift-extensions-for-cleaner-code-41e250c9c4c3>

## 更高效地构建您的移动应用

![](img/ce13d1e3e809d8cebfdd661f20af2637.png)

弗朗西斯科·德·托马索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在我看来，Swift 和 Objective-C 最好的特性之一就是扩展。它们使您能够向任何类添加新方法，并且整个项目将能够调用它们，而无需额外的继承和重载。

作为一名移动开发人员，我同时使用 iOS 和 Android，我经常看到 Android 的功能和方法比 Swift 更简短、更简洁、更容易理解。使用扩展，其中一些方法可以移植到 Swift。加上一些新的方法(扩展),我们将在 Swift 中获得一个简短、干净和易于维护的代码。

我会用 Swift，但是所有这些扩展都可以移植到 Objective-C 或者直接和 Objective-C 一起使用，不需要转换。

# String.trim()和 Swift.trimmed

在 99%的情况下，当我在 Swift 中修剪字符串时，我希望删除空格和其他类似符号(例如，新行和制表符)。

这个简单的扩展完成了这个任务:

用法:

```
**var** str1 = "  a b c d e   \n"
**var** str2 = str1.trimmed
str1.trim()
```

# Int.toDouble()和 Double.toInt()

如果您使用选项，这些方法会很有用。如果有非可选的`Int`，可以用`Double(a)`进行转换，其中`a`为整数变量。但如果`a`是可选的，就做不到了。

让我们添加对`Int`和`Double`的扩展:

用法:

```
**let** a = 15.78
**let** b = a.toInt()
```

# String.toDate(…)和 Date.toString(…)

从`String`获取`Date`并格式化`Date`以显示它或发送给 API 是常见的任务。标准的转换方法需要三行代码。让我们看看如何使它更短:

用法:

```
**let** strDate = "2020-08-10 15:00:00"
**let** date = strDate.toDate(format: "yyyy-MM-dd HH:mm:ss")
**let** strDate2 = date?.toString(format: "yyyy-MM-dd HH:mm:ss")
```

# Int.centsToDollars()

一些支付 API(例如 [Stripe](https://stripe.com/) )更喜欢使用货币单位(美分)进行支付处理。它可以避免`Float`和`Double`的不精确。同时，使用这些类型来显示值会更舒服。

该扩展实现了这种转换:

用法:

```
**let** cents = 12350
**let** dollars = cents.centsToDollars()
```

# String.asCoordinates()

地球上一个地方的坐标至少有两个数字——纬度和经度。另一个是高度，但这仅在 3D 空间中有意义，这在软件开发中并不常见。

从 API 中，我们要么得到两个单独的字段，要么得到一个带有逗号分隔值的字段。这个扩展允许将这样的字符串转换成`CLLocationCoordinate2D`。

用法:

```
**let** strCoordinates = "41.6168, 41.6367"
**let** coordinates = strCoordinates.asCoordinates
```

# String.asURL()

iOS 和 macOS 使用`URL`类型来处理链接。它更加灵活，允许获取组件，并且处理不同类型的 URL。同时，我们通常输入它或者从 API `String`获取它。

从一个到另一个的转换非常容易，但是这个扩展允许我们处理可选的或者链接这个转换:

用法:

```
**let** strUrl = "https://medium.com"
**let** url = strUrl.asURL
```

# UIDevice.vibrate()

iPhone 的振动对于按钮敲击和其他来自设备的反馈来说是一种很酷的效果。对于 iPhone 振动，有一种特殊的声音，由`AudioToolbox`框架处理。

包括`AudioToolbox`到所有带振动的`UIViewController`都很烦人，逻辑上振动更多的是设备功能(它不是来自扬声器而是来自设备本身)而不是播放声音。这个扩展允许将其简化为一行:

用法:

```
UIDevice.vibrate()
```

# String.width(…)和 String.height(…)

iOS 可以使用提供的约束自动计算`UILabel`的大小，但有时自己设置大小也很重要。

这个扩展允许我们使用提供的`UIFont`计算`String`的宽度和高度:

用法:

```
**let** text = "Hello, world!"
**let** textHeight = text.height(withConstrainedWidth: 100, font: UIFont.systemFont(ofSize: 16))
```

# String .只包含个数字

当您需要限制用户输入或验证来自 API 的数据时，下面的扩展非常有用。它检查`String`是否只有数字:

用法:

```
**let** digitsOnlyYes = "1234567890".containsOnlyDigits
**let** digitsOnlyNo = "12345+789".containsOnlyDigits
```

# String.isAlphanumeric

和前面的扩展一样，这个扩展检查`String`的内容。如果字符串不为空并且只包含字母数字字符，则返回 true。此扩展的反向版本可用于确认密码包含非字母数字字符。

用法:

```
**let** alphanumericYes = "asd3kJh43saf".isAlphanumeric
**let** alphanumericNo = "Kkncs+_s3mM.".isAlphanumeric
```

# 字符串下标

Swift 5 订阅`String` s 的方式很糟糕。例如，如果你想得到从 5 到 10 的字符，计算索引和偏移量是很烦人的。该扩展允许使用简单的`Int`来实现此目的:

用法:

```
**let** subscript1 = "Hello, world!"[7...]
**let** subscript2 = "Hello, world!"[7...11]
```

# UIImage.squared

当你让用户拍照或选择一张现有的照片作为个人资料照片时，他们很难提供一张正方形的照片。同时，大部分 ui 使用的是正方形或者圆形。

这个扩展裁剪了提供的`UIImage`，使其成为一个完美的正方形:

这个扩展也可以作为一个方法来实现。因为方形图像不是原始图像的属性，而是原始图像的处理版本。如果你觉得 method 是更好的解决方案，就把`var squared: UIImage?`换成`func squared() -> UIImage?`。

用法:

```
**let** img = UIImage() // Must be a real UIImage
**let** imgSquared = img.squared // img.squared() for method
```

# UIImage.resized(…)

在将图片上传到服务器之前，您必须确保它足够小。iPhones 和 iPads 有非常好的相机，它们图库中的图片可能没有大小限制。

要确保`UIImage`不大于给定的尺寸，例如 512 像素或 1024 像素，请使用以下扩展:

用法:

```
**let** img2 = UIImage() // Must be a real UIImage
**let** img2Thumb = img2.resized(maxSize: 512)
```

最后两个扩展名可以链接在一起:

```
**let** img = UIImage() // Must be a real UIImage
**let** imgPrepared = img.squared?.resized(maxSize: 512)
```

# Int.toString()

Java 最有用的特性之一就是`toString()`方法。这绝对是所有类和类型的方法。Swift 允许使用字符串插值做类似的事情:`"\(someVar)"`。但是有一点不同，你的变量是可选的。Swift 会将单词`optional`添加到输出中。Java 只会崩溃，但 Kotlin 会很好地处理 optionals】会返回一个可选字符串，如果`someVar`是`null` ( `nil`)或者包含`var`值的`String`，则返回`null` ( `nil`)。

不幸的是，Swift 不允许扩展`Any`，所以让我们至少给`Int`增加`toString()`方法:

用法:

```
**let** i1 = 15
**let** i1AsString = i1.toString()
```

# Double.toString()

和前面的例子一样，将`Double`转换成`String`非常有用。但在这种情况下，我们将用两位小数限制输出。我不能说这个扩展对所有情况都有用，但是对于大多数情况来说，它会工作得很好:

用法:

```
**let** d1 = 15.67
**let** d1AsString = d1.toString()
```

# Double.toPrice()

用价格生成`String` s 只是格式化`Double` s 的另一种方式，这种算法并不通用，它取决于地区设置。但你可以把它作为一个总体思路，并对你的应用程序进行调整:

用法:

```
**let** dPrice = 16.50
**let** strPrice = dPrice.toPrice(currency: "€")
```

# String.asDict

JSON 是交换或存储结构化数据的流行格式。大多数 API 更喜欢使用 JSON。JSON 是一种 JavaScript 结构。Swift 有完全相同的数据类型— dictionary。

将一个转换成另一个是一个相当简单的技巧:

用法:

```
**let** json = "{\"hello\": \"world\"}"
**let** dictFromJson = json.asDict
```

# String.asArray

这个扩展类似于上一个扩展，但是它将 JSON 数组转换为 Swift 数组:

用法:

```
**let** json2 = "[1, 2, 3]"
**let** arrFromJson2 = json2.asArray
```

# String.asAttributedString

有时我们需要一些简单的独立于平台的文本样式。一种相当常见的方法是使用简单的 HTML 来实现这个目的。

`UILabel`可以显示带有粗体(`<strong>`)部分的文本、带下划线的文本、更大和更小的片段等。你只需要把 HTML 转换成`NSAttributedString`，赋给`UILabel.attributedText`。

这个扩展将帮助您完成第一个任务:

用法:

```
**let** htmlString = "<p>Hello, <strong>world!</string></p>"
**let** attrString = htmlString.asAttributedString
```

# Bundle.appVersion

最后一个扩展名允许从`Info.plist`文件中获取应用程序版本。它可用于:

*   正在将应用版本发送到 API。
*   正在检查可用的更新。
*   在设备屏幕上显示应用程序版本。
*   将应用版本包含在支持电子邮件中。

下面的扩展允许你在一行代码中获得应用版本(或者`nil`，如果不可用的话):

用法:

```
**let** appVersion = Bundle.mainAppVersion
```

# 结论

我希望这些扩展能帮助你使你的代码更短更整洁。请随意修改它们以满足您的需求，并将它们包含到您的项目中。

如果您对更有用的 Swift 字符串扩展感兴趣，您可以阅读我的另一篇文章:

[](https://medium.com/better-programming/10-useful-swift-string-extensions-e4280e55a554) [## 10 个有用的 Swift 字符串扩展

### 电子邮件验证、类型转换等等

medium.com](https://medium.com/better-programming/10-useful-swift-string-extensions-e4280e55a554) 

编码快乐，下次见！
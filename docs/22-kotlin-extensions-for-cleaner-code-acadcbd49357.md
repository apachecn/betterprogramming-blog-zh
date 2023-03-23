# 22 个 Kotlin 扩展，用于清理代码

> 原文：<https://betterprogramming.pub/22-kotlin-extensions-for-cleaner-code-acadcbd49357>

## 构建移动应用程序时更有效率

![](img/bab172d3b0ba786da53dd5db551b1e06.png)

乔纳森·肯珀在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我是一名移动开发人员，在 Kotlin 出现后我立即改用它的原因之一是因为它支持扩展。扩展允许您将方法添加到任何现有的类中，甚至添加到`Any`或可选的类型中(例如，`Int?`)。

如果扩展基类，所有派生类都会自动获得该扩展。您还可以从扩展中覆盖方法，这使得这种机制更加灵活和强大。

我用的是 Android Studio 4.0.1 中的 Android 版 Kotlin 1.4.0。我假设所有的方法都将从 Kotlin 调用，而不是 Java。即便如此，大部分扩展也可以在 Kotlin 的其他版本中工作，其中一些可以在其他环境中工作。

# “Int.toDate()”和“Int.asDate”

我们经常获得时间戳形式的日期和时间。一个*时间戳*是自 1970 年 1 月 1 日以来的秒数(有时是毫秒)。这个时刻被称为*纪元*。

这个简单的扩展将秒转换成一个`Date`对象:

有两个选择:一个`function`或一个`read-only`的财产。它们在功能上是平等的。用哪个是口味问题。

## 使用

```
*val* json = JSONObject();
json.put("date", 1598435781)

*val* date = json.getIntOrNull("date")?.asDate
```

**注意:**在这个例子中，我使用了另一个扩展— `getIntOrNull`。如果它存在于 JSON 中，它返回一个 int 值，否则返回`null`。您可以在这里找到完整的源代码:

[](https://medium.com/better-programming/10-useful-kotlin-string-extensions-46772b653f71) [## 10 个有用的 Kotlin 字符串扩展

### 用于电子邮件验证、JSON 解析等的语法糖

medium.com](https://medium.com/better-programming/10-useful-kotlin-string-extensions-46772b653f71) 

# “String.toDate(…)”和“Date.toString(…)”

另一种常见的对`Date`对象的转换是将其转换成字符串，然后再返回。我说的不是标准的 Java/Kotlin `toString()`方法。在我们的例子中，我们需要指定一种格式。

**性能警告:**在这个例子中，我们每次都创建一个`SimpleDateFormat`对象。如果您在列表中使用这个扩展，或者从 API 中解析一个大的响应，请考虑从扩展代码中移除`val dateFormatter = SimpleDateFormat(format, Locale.US)`,并将其作为全局变量或静态类成员放入。

## 使用

```
*val* format = "yyyy-MM-dd HH:mm:ss"
*val* date = Date()
*val* str = date.*toString*(format)
*val* date2 = str.*toDate*(format)
```

# '和' Int.centsToDollarsFormat()'

如果处理价格，那么`Floats`和`Doubles`可能会有精度问题。代表价格的一种流行方式是`Ints`。但是，您不是用主要货币(例如，美元或欧元)来存储值，而是用货币单位(例如，美分)来表示它们。

您需要在`Int`内进行所有的计算，并且只向用户显示最终值(转换为`Double`或直接转换为`String`)。

## 使用

```
*val* amount = 4999
*val* doubleAmount = amount.*centsToDollars*()
*val* priceTag = amount.*centsToDollarsFormat*("\$")
```

# Double.toPrice()'

处理价格时另一个有用的扩展是分组格式。在大多数情况下，在一个应用程序中，我们只有一套价格格式规则。一个扩展可以在整个应用程序中使用，以显示价格。

在这个扩展中，我们使用两个小数(货币)数字，中间显示一个逗号，并用一个点分隔三个数字中的每一个，以使价格更容易阅读。这次我们用`Double`。将`Int`转换为`Double`可以通过前面章节中的`centsToDollars()`扩展来完成。

## 使用

```
*val* price = 123456789.5.*toPrice*()
```

# ' String.toLocation(…)'

当你使用一个 API 并得到一个对象的坐标时，你可能会得到两个不同的字段。但有时它是一个字段，用逗号分隔纬度和经度。

有了这个扩展，我们可以用一行代码将它们转换到 Android 位置:

## 使用

```
*val* apiLoc = "41.6168, 41.6367".*toLocation*("API")
```

类似地，您可以从 Google Maps 包中创建一个将`String`转换为`LatLng`的扩展。在这种情况下，您甚至不需要指定位置提供者。

# String.containsOnlyDigits '和' String.isAlphanumeric '

先说一个`String`的属性。它们可以为空，也可以不为空，例如，它们可以只包含数字或字母数字字符。这些扩展允许您在一行中验证一个字符串:

## 使用

```
*val* digitsOnly = "12345".*containsDigitOnly
val* notDigitsOnly = "abc12345".*containsDigitOnly
val* alphaNumeric = "abc123".*isAlphanumeric
val* notAlphanumeric = "ab.2a#1".*isAlphanumeric*
```

# “Context.versionNumber”和“Context.versionCode”

在 Android 应用程序中，在“关于”或“支持”屏幕上显示版本号是一个好习惯。它将帮助用户了解他们是否需要更新，并在报告错误时提供有价值的信息。标准的 Android 功能需要几行代码和异常处理。这个扩展将允许您在一行中获得版本号或代码:

用法:

```
*val* vn = *versionName* ?: "Unknown"
*val* vc = *versionCode*?.toString() ?: "Unknown"
*val* appVersion = "App Version: $vn ($vc)"
```

# ' Context.screenSize '

Android 的另一个有用的扩展是`Context`扩展，它允许你获得设备的屏幕尺寸。这个扩展返回以像素为单位的屏幕尺寸:

## 使用

```
Log.d(TAG, "User's screen size: ${*screenSize*.x}x${*screenSize*.y}")
```

# 任何.设备名称

是否应该延长`Any`值得商榷。如果你这样做了，函数会全局出现，所以基本上，这和声明一个全局函数是一样的。

在我们的下一个扩展中，我们将获得一个 Android 设备的名称。由于它不需要任何上下文，我们将把它作为一个`Any`扩展:

## 使用

```
Log.d(TAG, "User's device: $*deviceName*")
```

# T.weak

接下来的情况就有点复杂了。假设我们有一个`Activity`和一个`ListView`，其中有许多单元格。每个细胞都能给出一些反馈。假设它有一个委托接口，我们将活动本身传递给一个单元，因为它实现了单元的接口:

```
*interface CellDelegate* {
  *fun* buttonAClicked()
  *fun* buttonBClicked()
}

*class* Cell(context: Context?) : View(context) {
  *// ...

  var* delegate: *CellDelegate*? = *null

  fun* prepare(arg1: String, arg2: Int, delegate: *CellDelegate*) {
    *this*.delegate = delegate
  }
}

*class* Act: Activity(), *CellDelegate* {
  *// ...
  fun* createCell(): Cell {
    *val* cell = Cell(*this*)
    cell.prepare("Milk", 10, *this*)
    *return* cell
  }

  *override fun* buttonAClicked() {
    *TODO*("Not yet implemented")
  }

  *override fun* buttonBClicked() {
    *TODO*("Not yet implemented")
  }
  *// ...* }
```

我只是展示一下这里的结构。在一个真实的 app 中，我们可以使用`ViewHolder`，重用单元格(这是正确的做法)。

这里的一个问题是`Act`和`Cell`相互引用，这会导致内存泄漏。这里一个好的解决方案是使用一个`WeakReference`。封装在`WeakReference`中的委托变量不会影响我们`Act`的引用计数器，所以一旦我们关闭屏幕，它就会和所有分配的单元格一起被销毁(或者添加到队列中稍后销毁——我们让 Android OS 来决定)。

这个简单的扩展允许您通过向任何对象添加`.weak`来获得弱引用:

## 使用

```
*class* Cell(context: Context?) : View(context) {
  *// ...

  private var* delegate: WeakReference<*CellDelegate*>? = *null

  fun* prepare(arg1: String, arg2: Int, delegate: *CellDelegate*) {
    *this*.delegate = delegate.*weak* }

  *fun* callA() {
    delegate?.get()?.buttonAClicked()
  }

  *fun* callB() {
    delegate?.get()?.buttonBClicked()
  }
}
```

我想强调这个扩展是通用的，它适用于任何类型。

# Context.directionsTo(…)'

从 Android 应用程序打开导航是一个受欢迎的功能。安卓是谷歌的产品，和谷歌地图一样。谷歌地图应用程序预装在大多数安卓手机和平板电脑上。最简单的解决方法是在安卓地图应用中打开导航。如果没有安装，只需在网络浏览器中打开它。

这是`Context`的扩展，从编码的角度来说，还可以。但从逻辑上讲，我会说得更具体些。它可以延长`Activity`或`AppCompatActivity`以避免从`Service`开始使用的副作用。您可以将可扩展的类更改为您在应用程序中使用的任何类型。

# “appcompActivity.callTo(…)”或“activity . call to(…)”

我们使用的逻辑与之前的扩展相同。但是我们不是导航到一个对象，而是试图调用它。这两个扩展可以在同一个应用程序中并排使用。

这种情况的复杂性在于许可(或缺乏许可)。安卓需要权限才能打电话。但与 iPhone 不同的是，它确实可以拨打电话，而不只是打开呼叫者。

该扩展将有两个参数，并直接扩展`Activity`或类似的类。第一个参数是电话号码，第二个是请求代码。如果我们需要许可，但我们没有:

## 用法(下面的代码是“AppCompatActivity”的一部分)

```
*private val* phone: String = "+1234567890"

*private fun* call() {
  *callTo*(phone, callPermissionRequestCode)
}

*override fun* onRequestPermissionsResult(requestCode: Int, permissions: Array<*out* String>, grantResults: IntArray) {
  *if* (requestCode == callPermissionRequestCode) {
    *if* (permissions.*isNotEmpty*() && grantResults.*isNotEmpty*() && grantResults[0] == PackageManager.*PERMISSION_GRANTED*) {
      call()
    }
  } *else* {
    *super*.onRequestPermissionsResult(requestCode, permissions, grantResults)
  }
}

*companion object* {
  *const val* callPermissionRequestCode = 2001
}
```

# ' String.asUri '

我们通常认为互联网地址是一个字符串。我们可以把它打出来并加上引号。比如“https://medium.com。”

但是内部使用，安卓有一个特殊的类型:`Uri`。很容易将一个转换成另一个。下面的扩展允许我们通过验证将一个`String`转换成一个`Uri`。如果它不是一个有效的`Uri`，它返回`null`:

## 使用

```
*val* uri = "invalid_uri".*asUri
val* uri2 = "https://medium.com/@alex_nekrasov".*asUri*
```

# “Uri.open(…)”、“Uri.openInside(…)”和“Uri.openOutside(…)”

现在，当我们有一个`Uri`时，我们可能想在浏览器中打开它。有两种方法可以做到这一点:

*   在应用程序中打开它
*   在外部浏览器中打开它

我们通常希望将用户留在我们的应用程序中，但有些模式无法在内部打开。比如我们不想在应用内浏览器中打开`instagram://`。更有甚者，我们想开的只有`http://`和`[https://](https://.)`T10。

我们将添加三个不同的扩展。一个将在应用程序内部打开`Uri`，另一个将打开外部浏览器，最后一个将根据模式动态决定。

要在应用程序中打开网页，我们需要创建一个单独的活动，或者使用一个库来完成这项工作。为了简单起见，我选择了第二种方式，并包含了 [FinestWebView](https://github.com/TheFinestArtist/FinestWebView-Android) 库。

在你的应用程序 gradle filem 包括这一行:

```
dependencies {
    implementation 'com.thefinestartist:finestwebview:1.2.7'
}
```

清单上写着:

```
<uses-permission android:name="android.permission.INTERNET" />

<activity
    android:name="com.thefinestartist.finestwebview.FinestWebViewActivity"
    android:configChanges="keyboardHidden|orientation|screenSize"
    android:screenOrientation="sensor"
    android:theme="@style/FinestWebViewTheme.Light" />
```

以下是我们的扩展:

## 使用

```
*val* uri2 = "https://medium.com/@alex_nekrasov".*asUri* uri2?.*open*(*this*)
```

# Context.vibrate(…)'

有时，我们希望从手机中获得一些物理反馈。例如，当用户点击某个按钮时，该设备可以振动。我将把关于这是否是一个好的实践或者是否超出了范围的讨论放在一边，最好把注意力集中在功能上。

首先，将该权限添加到您的清单中:

```
<uses-permission *android:name*="android.permission.VIBRATE" />
```

## 延长

## 使用

```
vibrate(500) // 500 ms
// Should be called from Activity or other Context
```

或者

```
context.vibrate(500) // 500 ms
// Can be called from any place having **context** variable
```

# 结论

我希望这些扩展对您有用，并使您的代码更短、更整洁。您可以修改它们以满足您的要求，并将它们包含在您的项目中。不要忘记将所有必要的权限添加到您的清单中。

编码快乐，下次见！
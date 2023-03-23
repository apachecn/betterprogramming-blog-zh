# 10 个有用的 Kotlin 字符串扩展

> 原文：<https://betterprogramming.pub/10-useful-kotlin-string-extensions-46772b653f71>

## 用于电子邮件验证、JSON 解析等的语法糖

![](img/d9db8562cf2a10beb09eb1a2bf5f894b.png)

Kotlin 是一种相对较新的编程语言。它基于 Java，Kotlin 代码在 JVM 中工作。也可以编译成原生应用(Kotlin Native)和 JavaScript 应用(Kotlin JS)。

作为一名 Android 开发者，我很高兴获得比 Java 更现代的语言。2017 年谷歌宣布 Kotlin 为 Android 开发的官方语言；2019 年，他们宣布 Android 将“Kotlin-first”。为什么 Kotlin 比 Java 好？

1.  Kotlin 支持选项。选装件有助于避免许多事故。
2.  科特林更简洁。更少的核心，更容易阅读，更容易维护。
3.  **科特林支持扩展。**

今天我们将谈论`string`扩展，它将使你的代码更短，更安全，总体上更好。

这些扩展适用于 Kotlin 1.3.70，但它们也必须兼容其他版本。我在 Java 环境中工作，所以我不能保证它们在 Kotlin/Native 和 Kotlin/JS 中也能工作。

*如果你也是 iOS 或 Mac 开发者，你可能会对我的文章* [*10 个有用的 Swift 字符串扩展*](https://medium.com/better-programming/10-useful-swift-string-extensions-e4280e55a554) *感兴趣。*

![](img/0e83a8d3235306ae00062fe1cfb3d7c1.png)

Jonathan Chng 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 1.MD5/SHA 哈希计算器

为什么我们要计算一个`string`的 MD5？原因可能有很多。在数据库中保存密码，通过不安全的通道进行一些验证，检查文件是否计算正确。SHA 哈希也是如此。

让我们从 MD5 开始:

`md5`这里是一个计算的属性(也可以作为一个函数)。首先，我们使用 java.security.MessageDigest 计算 MD5 hash 作为字节数组。然后，我们将字节数组转换为十六进制字符串。

SHA-1 是“安全哈希算法— 1”。一般 SHA 是一组算法。从编程的角度来看，这两者没有太大的区别；我们只需要在`MessageDigest.getInstance()`调用中改变算法名称。

## 如何使用它

```
**val** md5Hash = "test".md5 // 098f6bcd4621d373cade4e832627b4f6
**val** sha1Hash = "test".sha1 // a94a8fe5ccb19ba61c4c0873d391e987982fbbd3
```

![](img/d524670e95e5882d1a3940b936ab1e2b.png)

照片由[大卫·巴拉迪](https://unsplash.com/@davideibiza?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 2.检查字符串是否是有效的电子邮件地址

检查电子邮件地址的有效性是一个非常常见的功能。从我的个人经验来看，10 个 Android 应用中有 9 个允许或要求用户认证。10 个有认证的应用程序中有 9 个允许电子邮件/密码作为方法之一。这是一个很好的做法，检查输入的电子邮件地址，然后再发送到服务器，而不是浪费互联网流量，使不成功的注册/登录电话。如果您开发后端，在将电子邮件地址保存到数据库之前对其进行验证是至关重要的。

验证电子邮件地址最简单的方法是使用正则表达式。你可以根据需要调整表达方式。我将展示我为自己选择的内容:

这个扩展提供了一个功能，但是计算属性也是一个选项。

## 如何使用它

```
**val** email = "test@email.com"
if (email.isEmailValid()) {
    *print*("Email is valid. Continue registration")
} else {
    *print*("Email is not validate. Show error")
}
```

![](img/61c2c6e0adfc82e3d3a6aa0ddfcc2a17.png)

照片由[奎诺·阿尔](https://unsplash.com/@quinoal?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 3.验证和格式化电话号码

与电子邮件类似，电话号码可能需要验证，更重要的是，需要格式化。电话号码只有国家代码是唯一的，但用户通常不输入它(他们通常改变国旗的国家名称)。这是与电子邮件的显著区别。

此外，不同国家的电话号码有不同的格式。这就是为什么它只能用外部库来完成。

对于 JVM 代码，有 Google 库 [libphonenumber](https://github.com/google/libphonenumber) 。有一个针对 [libphonenumber-android](https://github.com/MichaelRocks/libphonenumber-android) 的特别改编。他们几乎一模一样。

## JVM 版本

如果有效，这个函数返回一个格式化的电话号码(以“+”和国家代码开头),否则返回`null`。区域是从列表中选择的国家。如果应用程序是针对一个国家，它可以是硬编码的价值。

## 如何使用它

```
**val** phone = "(202)555-0156" // Phone number is fake, but has valid format
**val** formattedPhone = phone.*formatPhoneNumber*("US")
if (formattedPhone == **null**) {
    *println*("Phone number is not valid")
} else {
    *println*("Sending $formattedPhone to API")
}
```

## 安卓版本

如果有效，该函数返回格式化的电话号码(以“+”和国家代码开头),否则返回`null`。首先论证的是安卓`Context`，例如`Activity`。

## 如何使用它

```
**val** phone = "(202)555-0156" // Phone number is fake, but has valid format
**val** formattedPhone = phone.*formatPhoneNumber*(this, "US")
if (formattedPhone == null) {
    *println*("Phone number is not valid")
} else {
    *println*("Sending $formattedPhone to API")
}
```

![](img/ddf25b6320512e0bcb86e0cf8048a40d.png)

欧文·比尔德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 4.检查里面有什么

`string`里面是什么？它可以是数字，可以是单词，也可以是包含特殊字符的密码。有时我们需要找出字符串内部的内容，以便知道下一步该做什么。让我们写一些扩展来实现它。

## 如何使用它

```
**val** cl = "Contains letters".*containsLatinLetter // true* **val** cnl = "12345".*containsLatinLetter // false* **val** cd = "Contains digits 123".*containsDigit // true* **val** istr = "123".*isIntegerNumber // true* **val** dstr = "12.9".*toDecimalNumber // true*
```

假设我们需要检查密码是否有效。这是新密码，不是验证现有密码。新密码需要一个数字、一个拉丁字母和一个非字母数字字符。此外，我们不希望密码中有空格，除了第一个和最后一个字符，我们只是修剪它们。最后一次验证—长度。我们希望我们的密码包含 6 到 20 个字符。

```
**val** password = "yt6Hbb2.s(ma**213"
**val** password2 = "yt6Hbb2sma213"**val** isPasswordValid = !password.*isAlphanumeric* && password.*containsDigit* && password.*containsLatinLetter* && password.length > 6 && password.length < 20 // true**val** isPassword2Valid = !password2.*isAlphanumeric* && password2.*containsDigit* && password2.*containsLatinLetter* && password2.length > 6 && password2.length < 20 // false, doesn't contain non-alphanumeric characters
```

![](img/f8a96488355262acbd54284bac096a43.png)

[Erda Estremera](https://unsplash.com/@erdaest?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 6.存储和检索本地设置

这个扩展是 Android 特有的。Kotlin 是 Android 开发的官方语言。越来越多的 Play Store 应用使用 Kotlin。开发时间更短，质量更好，同时与 Java 有充分的兼容性和互操作性。

Android 应用程序有几种方法来存储本地设置，但最简单和本机的方法是`SharedPreferences`。

第一个论点是`applicationContext: Context`。如果从`Activity`运行函数，它可以传递自身(因为`Activity`是`Context`的子类)或者更好的是`applicationContext`。

第二个参数是`value: Map<String, Any>`。Kotlin 中的地图易于创建和使用。在这个扩展中`Any`可以是任意非空值，但是只保存五种类型:`Int`、`Float`、`Long`、`Boolean`和`String`。

如果不在`value`中的`SharedPreferences`中的所有字段都应该从`SharedPreferences`中移除，则参数`clear`应该为真。

关于`SharedPreferences`的另一个细节是背景保存。保存`SharedPreferences`有两种方法:`commit`和`apply`。不同之处在于`commit`会立即应用更改。`apply`在后台执行，这可以使你的应用程序更快，但不能保证立即保存。在大多数情况下，`apply`是首选，这就是为什么`now`参数几乎总是应该是`false`。

## 如何使用它

这个例子展示了一个应用程序如何存储游戏设置:

```
"com.app.options".*save*(*applicationContext*,
    ***mapOf***(
        "volume" *to* 0.8f,
        "fullscreen" *to* true
    )
)

**val** volume = "options".*load*(*applicationContext*)["volume"] as? Float // 0.8
```

请注意，这里的`SharedPreferences`是用`apply`保存的。同时，可以立即使用。这是因为`SharedPreferences`存储在手机内存中。如果应用程序随后崩溃，有很小的机会价值不会被保存，尤其是如果它很大。

![](img/9c6a110eb7db003f440a4174d28e8e62.png)

由[卡斯帕·卡米尔·鲁宾](https://unsplash.com/@casparrubin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 7.从字符串解析 JSON

Java 和 Kotlin 允许将 JSON 解析成`JSONObject`或`JSONArray`类(取决于内容)。让我们添加一些语法糖，使它更快，没有例外。如果`String`包含有效的 JSON，函数将返回`JSONObject`或`JSONArray`，否则返回`null`。

## 如何使用它

让我们解析 JSON 字符串并再次将其转换为`String`:

```
**val** json = "{\"key\": \"value\"}".*jsonObject * // {"key": "value"}**val** jsonAgain = json?.toString() // "{"key": "value"}"
**val** stringFromJson = json?.getString("key") // "value"
```

![](img/385511b399a0e87a585b15bcb4d5206f.png)

Jonatan Lewczuk 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 8.最后路径组件

苹果基金会有一个很有用的属性`lastPathComponent`。如果`String`包含路径或 URL，则返回最后一个组件(在最后一个“/”之后)。让我们将其复制为 Kotlin 扩展。

但首先，我想做两点说明:

1.  路径可以结尾斜杠结束。扩展函数应该在其前面返回路径组件；否则，它不会处理 URL。
2.  一些系统(例如微软视窗系统)使用反斜杠代替斜杠。我们也应该处理它。

## 如何使用它

```
**val** lpc1 = "https://google.com/chrome/".*lastPathComponent* // chrome
**val** lpc2 = "C:\\Windows\\Fonts\\font.ttf".*lastPathComponent* // font.ttf
**val** lpc3 = "/dev/null".*lastPathComponent* // null
```

![](img/9d552d44d3e87142a871c5a77e16b31f.png)

[毛绒设计工作室](https://unsplash.com/@plushdesignstudio?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 9.字符串中的颜色

颜色在不同的环境中有不同的表现。Android 使用简单的整数。它有`Color`类，但它只包含帮助函数。另一个表示是 AWT `Color`类，它为红色、绿色、蓝色和 alpha 分量封装了四个整数值。

## 将十六进制`String`解析为 AWT `Color`

## 如何使用它

```
**val** c = "#010203".*awtColor
print*(c.*toString*()) // java.awt.Color[r=1,g=2,b=3]
```

## 将十六进制字符串解析为 Android 颜色

这个类返回`Int`，因为 Android 在其视图中使用了`Int`。

## 如何使用它

```
**val** colorHex = "#010203"
**val** color = colorHex.*asColor //* -16711165**val** nonColorHex = "abcdef"
**val** nonColor = nonColorHex.*asColor // null*
```

你可能会问:如果用一行代码替换另一行代码，为什么要使用扩展？

```
**val** color1 = Color.parseColor(hexString)
**val** color2 = hexString.*asColor*
```

有两个不同之处:

1.  `asColor`分机可以连锁。比如:`hexString.asColor?.toString()`
2.  `null`代替例外。不需要用`try-catch`块包起来。如果不是有效的颜色，您将得到`null`。

![](img/5e1ffb8bcbad03879eaf4984c34566de.png)

[粘土堤](https://unsplash.com/@claybanks?utm_source=medium&utm_medium=referral)在[防溅板](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍照

# 10.将字符串格式化为信用卡号

一些 Android 应用程序出售物品或服务，不能通过应用内购买支付。对于 Java server app store，它使用并向用户显示信用卡号。通常应用程序会将其保存为不带空格的`string`。但是没有任何分隔的 16 位(有时更多)数字对用户来说是不舒服的。

这个简单的扩展在字符串的每四个字符后添加空格:

## **如何使用**

```
**val** ccFormatted = "1234567890123456".*creditCardFormatted // "*1234 5678 9012 3456*"*
```

# 额外收获:使用 JSON 安全工作

JSON 和 XML 是数据加载、保存、交换等的两种流行格式。很难想象没有这些格式的 API 调用。

Java 提供了相当舒适的类:`JSONObject`和`JSONArray`。Kotlin 可以使用任何 Java 类，所以这也是 Kotlin 解析和编写 JSON 的一种方式。`JSONObject`和`JSONArray`安卓都有。

为什么要扩展这些类？问题是，如果出现问题，它们会抛出异常。如果出了什么差错。例如，如果您试图获取错误的数据类型，或者如果您引用了不存在的字段。

```
**try** {
    **val** obj = JSONObject()
    obj.put("str", "abc")
    **val strAsInt = obj.getInt("str") //** org.json.JSONException: Value abc at str of type java.lang.String cannot be converted to int**val noKey = obj.getString("iamnothere")** // org.json.JSONException: No value for iamnothere
} catch (e: JSONException) {
    e.printStackTrace()
}
```

例外没什么不好；这是处理这种情况的好方法。但有时我们需要只拉现有字段，忽略(保留为`null`)字段，这些字段不在 API 响应中。

如果没有扩展，可以这样做:

```
**val** firstName: String?
**try** {
    firstName = json.getString("first_name")
} **catch** (e: JSONException) {
    firstName = **null**
}**val** lastName: String?
**try** {
    lastName = json.getString("last_name")
} **catch** (e: JSONException) {
    lastName = **null**
}
```

看起来很长，对吧？现在想象一下，服务器返回的是`"null"`而不是`null`。每个字段的代码都将增长。这可以通过扩展来解决:

这个扩展有一些额外的功能，如字符串修剪。如果你仔细看看会更好，而不是复制到你的项目中。像将`"null"`替换为`null`这样的功能可以这样添加:

```
**fun** JSONObject.getStringOrNull(name: String): String? =
      **try** {
         **val** str = getString(name).*trim*()
         if (str == "null") return **null**
         **return** str
      }
      **catch** (e: JSONException) {
         **null**
      }
```

## 如何使用它

```
**val** firstName = json.getStringOrNull("first_name")
**val** lastName = json.getStringOrNull("last_name")
```

## 注意

解析 JSON 的另一种方法是使用外部库: [GSON](https://github.com/google/gson) 或 [Moshi](https://github.com/square/moshi) 。

# 结论

你可以是 JVM 开发人员、Android 开发人员，或者更奇特的 Kotlin 原生开发人员——我希望这些扩展对你有用。

下次见。编码快乐！
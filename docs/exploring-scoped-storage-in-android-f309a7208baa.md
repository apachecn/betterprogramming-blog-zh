# 探索 Android 中的作用域存储

> 原文：<https://betterprogramming.pub/exploring-scoped-storage-in-android-f309a7208baa>

## 面向 Android 开发者的全新存储系统

![](img/294420c78f2068a738f71f82b9a1f7ea.png)

弗兰克·麦凯纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

如果你看到最近在 Android 操作系统层面上的改进，团队主要关注的是安全性和隐私。例如，为了在 Android 中提供安全的偏好存储，该团队最近发布了 [EncryptedSharedPreferences 库](https://developer.android.com/reference/androidx/security/crypto/EncryptedSharedPreferences)。关于位置和麦克风等敏感数据权限，单次权限——应用程序只能访问该实例——出现了。

最后，由于与存储相关，我们得到了范围存储，其中媒体的整个库访问仅可用于 Play Console 中的白名单应用程序。

# 问题

Android 存储系统有两个问题，即使在发明了十年之后，这两个问题也导致了创建新的存储系统:

1.  任何拥有`READ_EXTERNAL_STORAGE` 和`WRITE_EXTERNAL_STORAGE` 权限的 Android 应用程序都可以很容易地访问和使用你的媒体文件。
2.  大多数请求存储权限的应用程序不需要媒体文件的宽视图。使用一个聊天应用程序，您可以在其中上传媒体或将媒体文件写入系统。该应用程序只需要访问单个媒体文件，但在目前的生态系统中，整个媒体集合都暴露在该应用程序中。

# 解决方案范围的存储

作用域存储的出现扭转了局面。它根据应用程序的要求限制媒体文件的曝光。从 Android 10 开始，如果没有被列入 Play 控制台的白名单，你将无法访问整个媒体收藏。它还提供了一个新的媒体框架，我们可以在其中执行简单的任务，例如在聊天应用程序中访问媒体文件，并将文件保存到系统中，而无需任何存储权限。

并不是所有的公司都有足够的开发人员专注于像范围存储这样的新变化。因此，Android 团队决定在下一个 Android 版本发布之前同时支持新的作用域存储和旧的原生存储系统。也就是说，面向 API 级别 29 的应用程序必须在清单文件中包含`requestLegacyExternalStorage` 标志，以便与旧的存储系统一起工作。看一看:

```
<**application** android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true"
    android:**requestLegacyExternalStorage**="true"
    android:theme="@style/AppTheme">
<**/application>**
```

# 如何使用作用域存储

正如我所说的，有两种方法可以使用作用域存储来访问媒体:

*   只需要将媒体文件保存为个人资料图片或用作电子邮件附件的应用程序。
*   需要访问所有媒体文件的应用程序(例如音乐播放器和文件浏览器)。

# 简单用法

作用域存储使得只需要用户选择的媒体文件(从存储中读取数据)或将媒体文件保存到存储中(写入文件)的应用变得更加容易。

## 读取文件

这里，我们使用一个`Intent`来启动一个新的活动，用户可以在其中选择一个媒体文件，在`onActivityResult`中，我们可以获得文件`URI`。看一看:

`ACTION_OPEN_DOCUMENT` 作为一个动作，启动一个活动，将指定类型的媒体文件呈现给用户，根据用户的选择，数据在`onActivityResult`中可用(如上图)。

`CATEGORY_OPENABLE` 表示该意向只需要可以用`contentResolver`打开的 URIs。

## 写文件

这与读取文件是一样的。我们必须在`onActivityResult`中开始意图并观察结果。看一看:

除了这里的动作类型之外，其他都和上面一样。我们使用`ACTION_CREATE_DOCUMENT` 来指示系统创建文件并恢复它的`URI` ，我们还必须指定文件的类型。我们也可以使用 intent 上的`EXTRA_TITLE` 标志来设置文件的名称。

系统将在应用程序的私有存储下创建这个文件，这样即使没有存储权限，我们也可以访问它们，这是作用域存储的一个显著特点。

# 复杂用法

对于音乐播放器和文件浏览器这样的应用程序来说，访问单个文件是不够的。`READ_EXTERNAL_STORAGE` 权限是强制性的，因为他们需要访问存储中的每个文件。通过使用下面这段代码，我们可以访问文件列表:

# 媒体元数据

作用域存储已经采取措施来保护可能潜在地暴露用户的位置历史的媒体的元数据。因此，如果应用程序具有必要的权限，您通过作用域存储访问的媒体文件仅提供元数据。

感谢您的阅读。
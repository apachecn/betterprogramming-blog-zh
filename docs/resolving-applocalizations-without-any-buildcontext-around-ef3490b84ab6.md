# 在没有任何 BuildContext 的情况下解析应用化

> 原文：<https://betterprogramming.pub/resolving-applocalizations-without-any-buildcontext-around-ef3490b84ab6>

## 在调用 runApp 之前设置 Android 通知通道

![](img/6675dbdd0be7b943d6144943be3494a4.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当你阅读关于[*flutter _ localizations*](https://docs.flutter.dev/development/accessibility-and-localization/internationalization)的文档时，你会注意到，通过`AppLocalizations.of(*context*)`使用本地化相当容易。由于这是在构建树中创建小部件时使用的，您可能想知道为什么这可能是一个问题，但是当您按照示例设置 firebase messaging SDK，特别是[为 *Android* 创建通知通道](https://github.com/FirebaseExtended/flutterfire/blob/0ca85e519b585429d3d38b8d918ee6b132347b3e/packages/firebase_messaging/firebase_messaging/example/lib/main.dart#L53)时，您会问自己:

> *我从*哪里得到 **上下文** *？*

最终`runApp()`还没叫。

```
void **main**() {
  **final** *l10n* = AppLocalizations.of(*???*);
  setupFirebaseMessaging(*l10n*);
  runApp(App());
}
```

# 查看 AppLocalizations 委托

我们将不得不以不同的方式获得`AppLocalizations`实例。让我们看看这些是如何创建的；我们甚至不需要从我们已经知道的本地化本身去寻找。`AppLocalizations.*delegate*`定义了一个简单的函数`load(locale)`，它创建`AppLocalizations`的*区域设置*依赖实例(或者如果提供的*区域设置*不被支持，则抛出一个`FlutterError`)。

```
void **main**() **async** {
  **final** locale = ???;
  **final** *l10n* = **await** AppLocalizations.*delegate*.load(locale);
  ...
}
```

现在我们只需要找到用于本地化的最佳语言环境。

# 解析支持的用户区域设置

要从所有支持的区域设置列表中解析用户区域设置，我们必须找到一个最适合系统定义的首选用户区域设置。这由`WidgetsApp`实现，通常由*颤振*定位实现直接使用:

> *当提供了***localeListResolutionCallback***时，Flutter 将首先尝试用提供的***localeListResolutionCallback***解析区域设置。如果回调或结果为空，将退回到尝试***localeResolutionCallback***。如果***localeResolutionCallback***和***localeListResolutionCallback***都为空或解析失败(返回空)，则使用基本回退算法。* [【颤振】](https://github.com/flutter/flutter/blob/ab89ce285f76a3fd3a9328ec863f5623cc5ac8bc/packages/flutter/lib/src/widgets/app.dart#L828)

在大多数情况下，只使用用`basicLocaleListResolution()`函数实现的基本回退算法就足够了。当您需要一种算法来实现用 [Unicode TR35](https://unicode.org/reports/tr35/#LanguageMatching) 定义的完整的语言环境解析时，您必须提供并使用带有`localeListResolutionCallback`函数的自定义实现。

# 把所有东西放在一起

![](img/6a3bb3b6061e92f3ca7f8c576af1fe51.png)

照片由[艾米丽·莫特](https://unsplash.com/@emilymorter?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

既然我们已经知道了如何创建`AppLocalizations`实例以及如何解析它所支持的用户区域设置，让我们看看最终它们是如何协同工作的。我们在这里将它声明为全局属性`l10n`:

```
Future<AppLocalizations> get **l10n** {
  **final** widgets*Binding* = WidgetsFlutterBinding.*ensureInitialized*();
  **final** *preferred* = widgets*Binding*.*window*.*locales*;
  **const** *supported* = AppLocalizations.*supportedLocales*;
  **final** *locale* = basicLocaleListResolution(*preferred*, *supported*);
  **return** AppLocalizations.*delegate*.load(*locale*);
}
```

只要考虑到这种实现不是有状态的，不应该在存储库或任何其他抽象中使用。请考虑改用全局键或其他可解析的值。

最终，无论用户何时决定更改他们设备的区域设置(即使只有百万分之一)，您都不希望在错误的区域设置中定义字符串。

[](https://docs.flutter.dev/development/accessibility-and-localization/internationalization) [## 国际化 Flutter 应用

### 您将了解如何跟踪设备的区域设置(用户的首选语言)。如何管理特定于语言环境的应用程序…

docs.flutter.dev](https://docs.flutter.dev/development/accessibility-and-localization/internationalization)
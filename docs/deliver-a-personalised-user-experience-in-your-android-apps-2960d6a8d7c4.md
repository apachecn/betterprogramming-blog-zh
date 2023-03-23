# 如何在您的 Android 应用中提供个性化的用户体验

> 原文：<https://betterprogramming.pub/deliver-a-personalised-user-experience-in-your-android-apps-2960d6a8d7c4>

## 了解如何在您的 Android 代码库中使用 WebEngage

![](img/ea3e44a0e7f17e3b08d7b05de05bdeb4.png)

斯蒂芬·菲利普斯-Hostreviews.co.uk 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

WebEngage 是指用户通过移动推送、应用内消息、短信、网络叠加、网络推送、电子邮件、WhatsApp 和脸书等渠道参与相关对话。

我们将探索 Android 应用中 WebEngage 功能的一部分。我们将从集成开始，然后转到分析部分，可以记录事件和属性来跟踪用户行为，最后以 WebEngage 的通知设置结束。

另一方面，WebEngage 在控制台中提供了方便易用的营销策略。这有助于公司营销团队在几分钟内创建直观的露营。WebEngage 可以在几乎所有平台上使用，如 Android、Web、IOS 等。

# 综合

WebEngage 通过托管在`jcenter` Maven 仓库上的`WebEngage` android SDK 提供了在 Android 项目中的快速集成。我们只需要在应用级`build.gradle`文件的依赖标签下添加以下几行:

# 初始化

下一步是启动或初始化`WebEngage`库，就像在 android 开发中使用 Firebase 一样。这里我们需要从 WebEngage 控制台获得的 WebEngage 许可证代码，通常以(`~`)开头。

为了避免多个 inits 并保持全局实现，我们在应用程序类的`onCreate`函数中这样做。看一看:

```
class SampleApplication : Application{ override fun onCreate() {
        super.onCreate() val webEngageConfig = WebEngageConfig.Builder()
            .setWebEngageKey(WEBENGAGE_LICENSE_CODE)
            .setDebugMode(true) 
            .build()
        registerActivityLifecycleCallbacks(
            WebEngageActivityLifeCycleCallbacks(
                this,
                webEngageConfig
            )
        ) }}
```

*注意:确保用 WebEngage 控制台中的实际许可证代码替换* `*WEBENGAGE_LICENSE_CODE*` *。*

# 分析学

我们通常在 android 应用程序中使用 Firebase 进行分析，当我在寻找替代品时，`WebEngage`似乎是完美的替代品，如果不是比 Firebase 更强大的工具的话。记录事件和跟踪属性就像使用 Firebase 库一样简单。

## 用户识别

让我们从识别`WebEngage`中的用户开始。我们可以获得如下所示的 WebEngage:

```
val weUser: User = WebEngage.get().user()
```

一旦用户成功完成登录或注册，您可以通过分配一个唯一的用户 id 在`WebEnagage`中识别它。我更喜欢使用存储在公司服务器中的相同用户 id。看一看:

```
weUser.login("9SBOkLVMWvPX")
```

一旦注册了 Webengage 用户，我们就可以在控制台中跟踪他的一系列详细信息。看一看:

```
weUser.setFirstName("John")
weUser.setEmail("john@doe.com")
weUser.setBirthDate("1998-02-23") weUser.setPhoneNumber("+123456789")
weUser.setGender(Gender.FEMALE)
```

如果您的应用程序具有用户注销功能，请确保在成功注销时调用 WebEngage 用户上的注销。看一看:

```
weUser.logout()
```

## 分析学

为了跟踪事件，`WebEngage`提供了一个分析类。首先，我们需要获得 WebEngage 分析类实例，如下所示:

```
val weAnalytics: Analytics = WebEngage.get().analytics()
```

现在我们需要在分析实例上使用`track`函数来跟踪任何事件。看一看:

```
weAnalytics.track("Event name goes here")
```

我们还可以添加一系列属性，您可以通过事件跟踪这些属性，如下所示:

```
val orderDetails :  HashMap<String, Any> = HashMap()
orderDetails.put("Amount" , 1000)
orderDetails.put("id" , orderId)
weAnalytics.track("Order Placed", orderDetails);
```

# WebEngage 通知

WebEngage 通知只不过是 Firebase 通知。首先，我们需要集成 Firebase 消息库，然后在`WebEnagage`中注册 Firebase 令牌。然后我们需要在 WebEngage 控制台中添加 Firebase 服务器密钥。就这些，现在你可以通过 WebEngage 发送通知了。

## Firebase 令牌注册

通过在应用程序级 Gradle 文件下添加以下行，开始在应用程序中添加 Firebase 消息库。

```
implementation 'com.google.firebase:firebase-messaging:22.0.0'
```

然后我们可以通过应用程序类的`onCreate`函数中的`setRegistrationID`函数在 WebEngage 中注册 Firebase 令牌。看一看:

我们都知道 Firebase 令牌会不时更新，所以最好将更新的令牌注册到来自`FirebaseMessagingService`类的`onNewToken`函数内部的 WebEngage:

不要忘记在清单文件中注册服务:

在`onMessageReceived`函数中，我们需要识别通知源，如果它来自 WebEngage，那么我们需要处理 WebEngage 的通知有效负载，如下所示:

我们可以通过`WebEngageConfig`定制小图标和大图标以及通知的强调色:

```
WebEngageConfig webEngageConfig = new WebEngageConfig.Builder()     .setPushSmallIcon(R.drawable.YOUR_SMALL_ICON)     .setPushLargeIcon(R.drawable.YOUR_LARGE_ICON)     .setPushAccentColor(Color.parseColor("#ff0000"))     
.build();
```

目前就这些。希望你学到了有用的东西。感谢阅读！
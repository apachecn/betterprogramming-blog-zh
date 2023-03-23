# Flutter 如何与平台原生代码通信？

> 原文：<https://betterprogramming.pub/how-does-flutter-communicate-with-platform-native-code-2bb002f64121>

## 探索不同的渠道

![](img/88d0bc2358f54651699b61c6ac781cf8.png)

马文·迈耶在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Flutter 在创建跨平台应用程序方面做得非常出色。有了许多现成的插件，访问本机功能和特性很容易，而且花费的时间很少。

但是当插件不能胜任任务时会发生什么呢，对于你正在做的工作还没有插件，或者你需要有你的自定义实现。

嗯，事情是这样的:你需要为你的目标平台编写本地代码，并在本地代码和 flutter 框架之间来回传递数据。听起来很难？但是不要担心，因为在这篇文章中，你将学会如何无缝地做到这一点。

# **沟通的平台渠道**

Flutter 使用灵活的消息传递方式在 dart 代码和应用程序主机的本机代码之间进行通信。Flutter 应用程序通过平台通道向其主机发送消息。主机监听平台通道并接收消息，执行本机代码，并向 Flutter 代码发回响应。所有这些都是异步发生的，以保持应用程序的响应能力。

> 所有消息在发送之前都被编码成二进制，接收到的二进制结果被解码成 Dart 值。

## **平台渠道 API 的类型**

Flutter 提供了各种通道来与特定于平台的代码进行通信。根据您的需要，以及您正在交互的特定于平台的代码的类型，您可以选择以下任意一种或混合使用。

> 所有通道都需要一个名称传递给它们连接客户端和主机端的构造函数。该名称必须是唯一的，并鼓励命名方案。

1.  [基础消息通道](https://api.flutter.dev/flutter/services/BasicMessageChannel-class.html)
    使用异步消息传递与平台插件通信的命名通道。

它是最简单的平台通道，采用一个[消息编解码器](https://api.flutter.dev/flutter/services/MessageCodec-class.html)对传递的数据进行编码和解码。Flutter 提供了四种编解码器，分别是 [StringCodec](https://api.flutter.dev/flutter/services/StringCodec-class.html) 、 [BinaryCodec](https://api.flutter.dev/flutter/services/BinaryCodec-class.html) 、 [JSONMessageCodec](https://api.flutter.dev/flutter/services/JSONMessageCodec-class.html) 和 [StandardMessageCodec](https://api.flutter.dev/flutter/services/StandardMessageCodec-class.html) 。

```
final messageChannel = BasicMessageChannel<String>(‘platform.testing/sensor_support’, StringCodec()); 
```

2.[方法通道](https://api.flutter.dev/flutter/services/MethodChannel-class.html)
使用异步方法调用与平台插件通信的命名通道。

它是最常见的平台渠道，适合大多数使用案例。它提供了一种在本机代码中执行函数并返回结果的方法。

```
final methodChannel = MethodChannel('platform.testing/method_calls');```
```

3. [EventChannel](https://api.flutter.dev/flutter/services/EventChannel-class.html) 使用事件流与平台插件通信的命名通道。

它适合于监听本地代码的变化，只要发生变化，本地代码就会更新您的 Dart 流。

```
 final eventChannel = EventChannel(‘platform.testing/light_sensor’);
```

有了以上信息，您就可以开始编写一些代码了。我们准备搭建一个 app，可以检测一个地方的光线强度(照度)。使用移动设备上的光传感器，我们可以实现这一点。

> 注意:本文目前只针对 Android 平台

# **设置提示**

为了更好地调试，在编写特定于平台的代码和 flutter 代码时，我建议您执行以下操作:

*   使用 VS 代码打开 Flutter 项目。您可以运行应用程序来查看 flutter 日志和错误。
*   使用 android Studio 打开 Android 文件夹。你可以运行应用程序来查看 android 原生日志以及使用断点。

## **权限**

在主清单文件中添加以下内容，向用户请求权限。

```
<uses-permission android:name="android.permission.BODY_SENSORS" />
```

## **依赖关系**

在`pubsec.yaml`文件中添加这个依赖项来处理权限请求。

```
permission_handler: ^10.0.0
```

## **颤振平台通道实现**

这是 dart 和 flutter 代码实现。请通读评论，了解它是如何工作的。

## **Android Kotlin 实现**

在你的 android 源文件夹中创建一个新文件`SensorActivity.kt`。将以下内容粘贴到其中。

这是处理光传感器变化的代码实现:

接下来，我们编辑`MainActivity.kt`文件的内容。这是配置 FlutterEngine 和注册所有插件的地方。平台通道 API 也在这里处理。

下面的代码包含了关于如何操作的注释:

> 注意:发送的每条消息都包含来自接收方的异步强制回复。Dart 总是希望为其未来的完成而传递的每条消息都有响应。

至此，您已经迈出了将平台本机代码集成到 Flutter 应用程序中的第一步。你也可以编写和发布你的插件。

这是工作应用程序的样子:

今天到此为止，小点。

编码快乐！

再见。

# **资源**

*   [颤振平台文章](https://medium.com/flutter/flutter-platform-channels-ce7f540a104e)作者 [Mikkel Ravn](https://medium.com/u/3568699995b8?source=post_page-----2bb002f64121--------------------------------)
*   [颤振文件](https://docs.flutter.dev/development/platform-integration)
*   [无聊的旋舞秀](https://youtu.be/ht2bDlJd2c4)
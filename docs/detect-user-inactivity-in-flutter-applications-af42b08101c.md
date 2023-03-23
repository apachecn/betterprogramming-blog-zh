# 检测颤振应用中的用户不活动

> 原文：<https://betterprogramming.pub/detect-user-inactivity-in-flutter-applications-af42b08101c>

## 防止数据泄露

![](img/ae09031c81f05333af0cbc15d5b060de.png)

[procyclick 访客管理系统](https://unsplash.com/@proxyclick?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

最常见的功能之一是检测用户的非活动状态，以避免数据泄漏。这些可以是你智能手机上的私人应用程序，比如你的银行账户，或者是在实体店的信息亭设备上运行的应用程序，你可能需要在这些设备上输入一些个人信息，比如你的电子邮件地址。

> 我假设您对依赖注入和阻塞模式有所了解，但是请记住，这些都不是实现您自己解决方案所必需的。反正我会提供一个替代方案。说够了，让我们开始研究吧！

# 使用依赖和阻塞模式

先加上`[injectable](https://pub.dev/packages/injectable)`、`[async](https://pub.dev/packages/async)`、`[flutter_bloc](https://pub.dev/packages/flutter_bloc)`和`build_runner`。那些是一些众所周知的软件包，经常被使用。

然后我们需要一个对象来记录时间，这是主要的特征。为此，让我们创建一个简单的`cubit`并使用`RestartableTimer`。它做它所说的，并由`async`包提供。正是我们需要的。

可以想象，这个想法是在`60 secondes`期间运行某种倒计时，一旦时间过去，它发出一个`TimeoutExpired`状态，以便我们的表示层中的`BlocListener`可以捕捉到它并相应地采取行动。例如，您可能想要显示一个`AlertDialog`来确保用户已经离开或者立即清除任何个人信息。

另一方面，每次与屏幕发生交互时，倒计时将`reseted`到其原始值。这就是下面代码的目的，也是为什么`RestartableTimer`比常规的`Timer`更好。

然后把你的`MaterialApp`包在一个简单的`Listener`里面！`onPointerDown`回调是我们实现的关键。每次你和屏幕互动的时候都会触发。

> 别忘了调用`runner`来生成你需要的一切。

# **没有阻塞或依赖注入替代方案**

用同样的方法创建一个简单的类。唯一的区别是回调函数。每当超时触发时，它将为我们提供处理预期行为的机会。

为了防止任何多次超时运行(如果您需要访问您的实例进行一些扩展使用)，我使用了`factory`构造函数来创建一个`singleton`，就像我使用`@lazySingleton`注释一样。显然`callback`会像`state`一样被触发。

然后，您需要创建它的一个实例，这样就万事俱备了。

不要忘记在`onPointerDown`回调中调用`reset`。

# 处理背景和前景状态

添加`WidgetsBindingObserver` mixin 并覆盖`initState`、`dispose`和`didChangeAppLifecycleState`方法。请确保您的应用程序扩展了`StatefulWidget`来这样做！如果没有，`didChangeAppLifecycleState`就不会被调用。

就是这样。有了这些，您应该能够在应用程序中的任何地方处理用户的不活动。

像往常一样，这是一个提议，如果你知道实现这种特性的更好的方法，请随意分享！

# 了解更多信息…

[](/build-a-collaborative-pixel-art-app-with-flutter-and-firebase-d4a027b4534b) [## 用 Flutter 和 Firebase 构建一个协作像素艺术应用程序

### 创建您自己的休息/场所

better 编程. pub](/build-a-collaborative-pixel-art-app-with-flutter-and-firebase-d4a027b4534b) [](/a-guide-to-handling-user-privacy-to-comply-with-gdpr-in-flutter-applications-9914ab479240) [## 颤振应用中符合 GDPR 的用户隐私处理指南

### 通过在收集数据前询问来保持合规性

better 编程. pub](/a-guide-to-handling-user-privacy-to-comply-with-gdpr-in-flutter-applications-9914ab479240) [](https://medium.com/@julien.duribreux_43255/flutter-dependency-injection-flavors-and-secrets-management-75181705045d) [## 依赖注入和风味的颤振秘密管理

### 软件开发中的一个常见用例是需要处理多种环境和相关的…

medium.com](https://medium.com/@julien.duribreux_43255/flutter-dependency-injection-flavors-and-secrets-management-75181705045d)
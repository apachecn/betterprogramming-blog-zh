# Flutter 中的简单 Firebase 登录流程，现在是 Firebase

> 原文：<https://betterprogramming.pub/simple-firebase-login-flow-in-flutter-now-firebase-79ecfe283dcf>

## 第二部分:添加 Firebase 以及更好的错误检查

![](img/e13c5ec00bc14ab38f697e071b09f51f.png)

照片由 [Fotis Fotopoulos](https://unsplash.com/@ffstop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/apps?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

[在第一部分](https://medium.com/better-programming/simple-firebase-login-flow-in-flutter-6f44c2b5c58a)中，我们用以下组件创建了一个简单的应用程序:

*   默认主应用入口点
*   使用 [FutureBuilder 小部件](https://api.flutter.dev/flutter/widgets/FutureBuilder-class.html)在呈现 UI 之前等待数据，这是整个应用程序使用的概念
*   登录页面
*   主页
*   认证服务
*   演示在 Flutter 文档中讨论的提供者的用法([简单的应用程序状态管理](https://flutter.dev/docs/development/data-and-backend/state-mgmt/simple#accessing-the-state))
*   参见第一部分:[简单的 Firebase 登录流程](https://medium.com/better-programming/simple-firebase-login-flow-in-flutter-6f44c2b5c58a)

现在我们将把 firebase 集成到应用程序中。

> 网上有很多关于为 Flutter 设置 Firebase 的例子，所以我将直接跳到代码中，而不是从头到尾介绍基础知识。参见[Google CodeLabs Flutter for Firebase](https://codelabs.developers.google.com/codelabs/flutter-firebase/index.html?index=..%2F..index#5)获取在 iOS 或 Android 上设置项目的分步说明。

# 在 Firebase 中创建一个测试用户

因为我们只是在构建应用程序，现在还没有在应用程序中创建用户的功能，请登录到你的 [Firebase 控制台](%5Bhttps://console.firebase.google.com/u/0/%5D(https://console.firebase.google.com/u/0/))并添加一个用户到你的项目中。请确保在 Firebase 控制台中更新项目时启用电子邮件身份验证。

# 向项目添加 Firebase 功能的步骤

*   将 Firebase 方法添加到`AuthService`
*   启动时从`AuthService`中访问`getUser`属性，以决定在`main.dart`中加载哪个页面
*   修改`HomePage`以显示登录者的电子邮件地址`FirebaseUser`
*   修改`LoginPage`来调用`AuthService`上的`loginUser`方法，使用 Firebase API 登录一个用户，看看我们是否可以登录一个真实的`FirebaseUser`
*   最后，在登录和启动时查找当前用户时，适当地处理错误。

# 身份验证服务:添加 Firebase API 功能

首先是身份验证服务，这里我们只是包装了一些基本的 Firebase 函数，我们需要这些函数来进行身份验证，并确定是否已经有一个来自上一个会话的用户。

从上面的代码中可以看出，我们仍然有相同的方法来访问我们的`AuthService`；现在唯一的不同是，我们已经用对您设置的 Firebase 后端的实际调用替换了这个调用。

请注意，我们不再需要为当前用户保留一个属性，因为 Firebase 会为我们管理它。我们需要做的就是调用方法`getUser`，如果有一个用户我们就会得到一个对象。否则它将返回 null。

最需要注意的是，当登录状态在登录或注销过程中发生变化时，我们会调用`notifyListeners()`。

# 修改 main.dart

因为我们使用的是相同的外部 API，所以不需要对文件进行真正的修改。唯一的区别是，现在我们返回的是一个`FirebaseUser`对象，所以让我们给代码添加一个特定的类型，并修改一些东西。

我们已经添加了与`AsyncSnapshot`相关联的对象类型`FirebaseUser`，现在我们正在检查错误，以防最初加载 Firebase 时出现问题。

我们还向`HomePage`小部件的构造函数添加了一个新参数，它是从对`AuthService`的`getUser`调用中返回的`FirebaseUser`对象。我们将在下一节看到新参数是如何使用的。

最后，我们添加了一个名为`LoadingCircle`的新部件，以便在应用程序启动并访问`Firebase`来检查新用户时，给我们一个良好的用户体验。参见下面的`LoadingCircle`小部件代码。

> 参见`[CircularProgressIndicator](%5Bhttps://api.flutter.dev/flutter/material/CircularProgressIndicator/CircularProgressIndicator.html%5D(https://api.flutter.dev/flutter/material/CircularProgressIndicator/CircularProgressIndicator.html))`的文档

# 在`home_page.dart`中修改主页小工具

我们需要首先通过添加一个新的构造函数来修改小部件，该构造函数将保存从`main.dart`中的 FutureBuilder 传入的 Firebase 用户

现在，我们可以通过小部件访问当前用户的信息；我们可以在渲染`HomePage`时通过如下所示的修改来访问它。我们将在构建方法中添加一些小部件:

# 在`login_page.dart`中修改 LoginPage 小工具

因为 API 签名没有改变，所以我们只需要对这个函数做很少的修改就可以得到想要的结果。然而，最好做一些更好的错误检查。

使用`Future`时，我们需要用`try` `catch`块包装调用，因为 Firebase 发生的任何错误都将作为异常抛出。然后，我们将在对话框中显示错误消息。参见方法`_buildErrorDialog`的代码和下面的其他更改。

为错误异常添加新导入:

对登录按钮的`onPressed`方法进行适当的修改。

为新的私有`_buildErrorDialog`方法添加代码，该方法将显示调用`AuthService`登录方法时出现的错误。

# 结论

此时，您应该有一个具有基本登录流的正常运行的应用程序，用户将使用您在 Firebase 控制台中添加的测试用户的凭据登录到 Firebase。

尝试输入无效的密码凭据和不完整的电子邮件地址，错误应该会适当地显示出来。

# 资源

源代码可以在这里找到:[https://github.com/aaronksaunders/simple_firebase_auth](https://github.com/aaronksaunders/simple_firebase_auth)
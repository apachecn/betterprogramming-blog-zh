# iOS 应用程序如何启动

> 原文：<https://betterprogramming.pub/how-an-ios-app-launches-ae62bbd4ae8e>

## 了解 iOS 生命周期的最初时刻

![](img/e1a8d36cbc00db5d7d3946435be6a501.png)

照片由[丹尼尔·麦金尼斯](https://unsplash.com/@dsmacinnes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/begin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

你有没有想过在点击应用图标和`[application:didFinishLaunchingWithOptions](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1622921-application?language=objc):`之间会发生什么？

我真的很喜欢深入探讨这样的主题，这些主题起初看起来微不足道，但可以极大地增进你对 iPhone 操作系统的理解。

今天存在的每一个 iPhone 应用程序在技术上都可以被描述为对`[UIApplicationMain(_:_:_:_:)](https://developer.apple.com/documentation/uikit/1622933-uiapplicationmain)`函数的一次调用。

等等，什么？是的，你没看错。请继续阅读，找出原因。

# UIApplication

如果您检查`UIApplicationMain(_:_:_:_:)`函数，您可以看到它有一个返回类型`Int32`。然而，这个函数实际上从不返回。只要你的应用存在，它就存在。

这个方法做的第一件事是创建一个名为`[UIApplication](https://developer.apple.com/documentation/uikit/uiapplication)`的类。这门课很重要。`UIApplication`是在 iOS 中运行的应用程序的集中控制和协调点。

每个 iOS 应用程序都有这个对象的一个实例。由此，您可以正确地推断出`UIApplication`确实是作为[单例](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/Singleton.html#//apple_ref/doc/uid/TP40008195-CH49)实现的。singleton 设计模式背后的基本原理是确保在任何给定的时间，一个类只有一个实例是活动的，所以它是这里的完美选择。

当你写`UIApplication.shared`的时候，你正在访问这个单例对象。

# UIApplicationDelegate

该函数做的下一件事是创建`AppDelegate`，它基本上是一个符合`[UIApplicationDelegate](https://developer.apple.com/documentation/uikit/uiapplicationdelegate)`协议的类。你可能已经熟悉了应用代理。它实际上是你可以与之交互的应用程序的根对象。

`UIApplicationDelegate`是一套用于管理应用共享行为的方法。

注意到类声明上面的`@UIApplicationMain`标记了吗？它的存在是为了让`UIApplicationMain(_:_:_:_:)`函数知道哪个类是应用程序的委托。这个类也会被保留，并在应用程序的生命周期中持续存在。然后被指定为`UIApplication`的代表。

application 对象通知委托重要的运行时事件，例如，应用程序启动、内存不足警告和应用程序终止，使其有机会做出适当的响应。

在 iOS 13 中，`AppDelegate`的部分职责交给了`SceneDelegate`。`AppDelegate`仍然负责应用的生命周期，但是`SceenDelegate`现在负责屏幕上显示的内容。它处理像屏幕和窗口这样的东西。

# 故事板？

此时，我们的应用程序的基础已经就绪。`UIApplicationMain(_:_:_:_:)`现在可以开始构造用户界面了。

这个过程的第一步是找出我们的应用程序是否使用主要的故事板。这是通过检查`Info.plist` 文件来完成的，看看它是否包含一个名为“主要故事板文件基本名称”的键

如果应用程序使用故事板，`UIApplicationMain(_:_:_:_:)`将为我们处理实例化[应用程序的窗口](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623056-window)。窗口是`UIWindow`的实例，它是`UIView`的子类。窗口基本上就是包含用户界面的东西。

一旦被实例化，窗口就被赋给了`SceneDelegate`的 window 属性(iOS 13 之前的`AppDelegate`用来包含这个属性)。

如果您查看一下您的`SceneDelegate`的`[scene(_:willConnectTo:options:)](https://developer.apple.com/documentation/uikit/uiscenedelegate/3197914-scene)`方法，您会看到下面的注释:

"如果使用故事板，`window`属性会自动初始化并附加到场景中."

`window`属性将被保留，并在应用程序的生命周期内持续存在。

# 初始视图控制器

用户不会看到应用程序的窗口，你也不经常需要直接操作它。用户将看到的是窗口的`[rootViewController](https://developer.apple.com/documentation/uikit/uiwindow/1621581-rootviewcontroller)`属性。

`rootViewController`包含应用的初始视图控制器。您可以设置初始视图控制器，方法是在故事板中选择一个视图控制器，打开属性检查器，并勾选“是初始视图控制器”复选框。

一旦找到，它就被分配给并保留在`rootViewController`属性中。它被称为根视图，因为它是窗口中唯一的直接子视图。所有其他视图都将是根视图的子视图。

这时`UIApplicationMain(_:_:_:_:)`决定是时候调用我们以前的`application:didFinishLaunchingWithOptions:`函数了。

窗口还不可见。要做到这一点，最后一步是调用`[makeKeyAndVisible()](https://developer.apple.com/documentation/uikit/uiwindow/1621601-makekeyandvisible)`实例方法。

此时，您的 iOS 应用程序已经准备好，乐趣可以开始了。

# 没有故事板？

然而，你可能知道，使用故事板并不是强制性的。在这种情况下，您必须在代码中做`UIApplicationMain`为您做的事情。

在 iOS 13 之前，这样做的地方应该是`didFinishLaunchingWithOptions`。然而，现在您有了`SceneDelegate`的`(_willConnectTo:options:)`方法来处理这个问题。

你要做的是:

*   实例化`UIWindow`并将其分配给`SceneDelegate`的`window`属性。
*   实例化您的初始视图控制器，并将其分配给`window`的`rootViewController`属性。
*   调用`makeKeyAndVisible()`将界面呈现给用户。

上述内容的基本实现可能如下所示:

# 结论

我上面描述的过程发生在眨眼之间，我们大多数人在开发应用程序时并没有真正考虑这一点。然而，它仍然是应用程序生命周期的精华部分。

感谢您花时间阅读这篇文章。如果你有任何想法、建议或问题，请在下面的评论中留下。

如果你喜欢阅读这篇文章，那么你可能会从我的两篇关于 iOS 开发和 Swift 的文章中获得一些价值:

[](https://medium.com/better-programming/understand-copy-on-write-in-swift-5-52a4716165a3) [## 了解 Swift 5 中的写入时复制

### 结构、值类型和黑魔法

medium.com](https://medium.com/better-programming/understand-copy-on-write-in-swift-5-52a4716165a3) [](https://medium.com/better-programming/befriend-objective-c-as-a-swift-developer-7d529c685299) [## 作为 Swift 开发人员与 Objective-C 友好相处

### 学会驯服野兽

medium.com](https://medium.com/better-programming/befriend-objective-c-as-a-swift-developer-7d529c685299)
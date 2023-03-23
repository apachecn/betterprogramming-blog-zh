# 使用 Combine 观察您的 iOS 应用程序的网络连接

> 原文：<https://betterprogramming.pub/observe-your-ios-apps-network-connection-using-combine-d96864111b83>

## SwiftUI 和 UIKit 示例

![](img/c2d855180326152b893637b244da58b5.png)

照片由 [Anh Nhat](https://unsplash.com/@anhnhat1205?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在本文中，我们将学习如何使用[网络](https://developer.apple.com/documentation/network)框架和[组合](https://developer.apple.com/documentation/combine)发布器来观察网络状态变化并做出反应。

简而言之，这就是我们将在本教程中掌握的内容:

*   如何为`NWPathMonitor`创建一个自定义的可重用的联合发布器，一个会观察网络状态的组件。
*   如何将网络状态更新绑定到 UIKit 和 SwiftUI 中的 UI 元素。

该项目的源代码可以在文章的底部找到。

# 我们开始吧

首先，让我们导入网络和组合框架，并在`NWPathMonitor`上定义一个扩展:

现在我们需要实施一个`Subscription`:

1.  我们依赖于接受`NWPath.Status`作为输入的`Subscriber`。
2.  我们还创建了`monitor`和`queue`依赖项，这样我们可以在以后启动网络监控过程。
3.  为了符合`Subscription`协议，我们实现了`request(_ demand:)`和`cancel()`方法。在`request`方法中，我们将启动监控过程。在`cancel`里面，我们会在必要的时候取消进程。

让我们更新`request`和`cancel`方法如下:

1.  我们使用`pathUpdateHandler`属性来接收包含当前网络状态的`NWPath`。一旦状态改变，`Subscriber`将接收到一个`NWPath.Status`值。
2.  我们开始监控过程，以便能够获得`pathUpdateHandler`闭包内部的状态变化。
3.  在`cancel()`方法中，一旦订阅被取消，我们就相应地禁用监控过程。

完成`Subscription`后，现在我们将再次扩展`NWPathMonitor`来创建一个自定义的`Publisher`:

下面是我们用这段代码实现的结果:

1.  我们定义了一个符合`Publisher`协议的定制`NetworkStatusPublisher`。
2.  发布器*发出* `NWPath.Status`值，从不出错。
3.  与我们之前定义的订阅非常相似，我们也依赖于`NWPathMonitor`和`DispatchQueue`。
4.  在所需的`receive<S>(subscriber:)`方法中，我们将很快创建之前创建的`NetworkStatusSubscription`，并为`NetworkStatusPublisher`附加一个订阅者。
5.  我们定义了一个`publisher(queue:)`方法，它将允许我们稍后从一个`NWPathMonitor`属性创建一个`Publisher`，并观察例如`UIViewController`中的网络状态变化。

现在让我们完成`receive<S>(subscriber:)`方法:

1.  我们用提供的订阅者`NWPathMonitor`和一个`DispatchQueue`初始化`NetworkStatusSubscription`。
2.  我们告诉订阅者我们已经成功地订阅了一个发布者。

最后，我们准备在我们的应用程序中使用`NetworkStatusPublisher`。

# SwiftUI 实施

下面是我们如何在使用 MVVM 设计模式的 SwiftUI 应用程序中使用它:

1.  我们创建了一个带有名为`networkStatus`的`Published`属性的`ObservableObject`视图模型。
2.  在初始化器内部，我们启动订阅并观察`.sink`操作符内部的状态变化。一旦我们得到一个值，我们就把它赋给我们之前定义的`Published` `networkStatus`属性。
3.  在`ContentView`里面，我们只是根据当前网络状态显示不同的`Text`。一旦状态改变，`Text`会自动更新。

# UIKit 实现

a `UIViewController`内部的实现非常相似:

这里，我们有一个`observeNetworkStatus()`方法，我们在其中观察状态并相应地更新`textLabel`。

太好了！我们已经成功地实现了一个可重用的网络状态发布器，我们可以轻松地在我们的应用程序中使用。

注意，测试网络变化最好在真实设备上进行，因为模拟器不能准确显示应用程序中的网络变化。

# 资源

源代码可在 [GitHub](https://github.com/zafarivaev/networkconnectivity-combine) 上获得。

# 包扎

要了解更多关于`NWPathMonitor`的信息，请随意查阅[罗斯·巴特勒](https://medium.com/@rwbutler/nwpathmonitor-the-new-reachability-de101a5a8835)的这篇文章。

我希望你觉得这个教程有用。感谢阅读！
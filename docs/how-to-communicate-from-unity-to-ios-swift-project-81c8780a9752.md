# 如何从 Unity 与原生 iOS Swift 项目进行沟通

> 原文：<https://betterprogramming.pub/how-to-communicate-from-unity-to-ios-swift-project-81c8780a9752>

## 将字符串从 Unity 传递到 iOS

![](img/0005d4072e93284a24b011ee1244b063.png)

我想在一个 iOS 项目中嵌入 Unity，并通过点击 Unity 中的一个按钮将一个字符串从 Unity 传递到 iOS 项目。

# 第 1 部分:Unity 脚本

1.  在您的 Unity 场景的 C#脚本中，在您的`public Class{}`中添加以下行。在我的例子中，我将它添加到我的`SimpleCloudHandler`类中。

添加内部 Unity C#脚本

这样，我们告诉 Unity，我们有一个名为`unityToIOS`的函数在 Unity 之外被定义。

2.我想通过点击 Unity 中一个名为`More Details`的按钮将字符串`name`从 Unity 传递到 iOS。在`void OnGUI(){}`内，添加:

创建一个按钮并在 Unity 中被点击时调用函数

我们已经完成了团结的部分。

# 第 2 部分:将 Unity 嵌入 iOS Swift 项目

请看我之前关于将 Unity 嵌入 iOS Swift 项目的[作品](https://medium.com/better-programming/how-to-embed-unity-with-vuforia-in-a-native-ios-swift-project-e9c82dfe53c)。在本文中，我将重点讨论通信部分。

# 第 3 部分:iOS Swift 项目

如果你按照[我的教程](https://medium.com/better-programming/how-to-embed-unity-with-vuforia-in-a-native-ios-swift-project-e9c82dfe53c)在 iOS 项目内部嵌入 Unity，你会在`Unity`文件夹下有`UnityUtils.mm`。

1.  在`UnityUtils.mm`的底部声明`unityToIOS`函数，在里面我们发布一个通知来传递字符串:

声明我们从 Unity 调用的函数

对上面的代码做一点解释:

**NSNotificationCenter** 支持向注册观察员广播信息。

**post notification name:object:userInfo**用给定的名称、发送者和信息创建一个通知，并将它发布到通知中心。

2.在显示 Unity 场景的视图控制器中，我们在`viewDidLoad()`中添加了一个观察者来接收我们在上面步骤中发布的通知:

在视图控制器中添加通知观察者

更多关于`NotificationCenter`的信息，请看[这里](https://developer.apple.com/documentation/foundation/notificationcenter)。

3.然后我们添加一个名为`isDataGet`的函数，在这里我们指定我们想要执行的活动。例如，这里我打印了我从 Unity 发送的字符串:

指定我们要执行的活动

现在，您应该能够将 Unity 中的数据打印到您的 iOS 项目中。
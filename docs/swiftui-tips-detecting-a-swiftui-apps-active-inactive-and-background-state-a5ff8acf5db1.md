# SwiftUI 提示:检测 SwiftUI 应用的活动、非活动和后台状态

> 原文：<https://betterprogramming.pub/swiftui-tips-detecting-a-swiftui-apps-active-inactive-and-background-state-a5ff8acf5db1>

## 跟踪 SwiftUI 应用的前台和后台状态很容易

![](img/c85ee03b4a11a9efbab02b92f78d8666.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[David vihovec](https://unsplash.com/@iamdavid_10?utm_source=medium&utm_medium=referral)拍摄的照片。

迟早，你需要知道你的应用程序什么时候在前台，什么时候在后台，或者在两者之间转换。您可能已经知道在 UIKit 中通过使用`UIApplicationDelegate`或`UISceneDelegate`可以做到这一点，但是在 SwiftUI 中是如何做到的呢？

事实证明，处理这项任务的方法不止一种。

# 通知中心

如果你以前开发过 iOS 应用程序，通知中心可能对你来说很熟悉。Apple 已经对其进行了更新，以提供一个组合发布器来发出可用于观察活动和非活动状态的事件。如果您不熟悉 Combine 的详细信息，请不要担心。观察这些状态真的很简单。

通过将一个`onReceive`动作附加到一个视图，您可以轻松地监控`didBecomeActiveNotification`和`willResignActiveNotification`:

在默认的`App`结构中增加了两行:

1.  在这里，我们观察`didBecomeActiveNotification`并通过打印到控制台对其做出反应。
2.  在这一行，我们观察到`willResignActiveNotification`并且也打印到控制台。

尽管很容易，但这不是我喜欢的做这件事的方法。我喜欢使用场景阶段，因为它提供了更多的灵活性。

# 场景相

观察活动和非活动状态的另一种方法是使用 SwiftUI 环境的`scenePhase`。场景阶段是具有值`active`、`inactive`和`background`的枚举。

通过从`@main` `App`结构(应用程序的主入口点)观察场景阶段，您可以观察应用程序中所有场景的总体状态。换句话说，一旦 app 中的所有场景都处于后台状态，app 的`scenePhase`就变成了`background`，这是 App 进入后台的标志。您可以在`WindowGroup`上附加一个`onChange`动作，以观察场景相位何时变化:

事情是这样的:

1.  我们将`scenePhase`环境值分配给一个名为`scenePhase`的新应用程序属性。
2.  我们通过使用场景的`onChange`动作来观察新`scenePhase`属性的变化。对`scenePhase`属性的任何修改都会导致闭包运行。在我们的例子中，我们将把当前场景阶段打印到控制台。

重要的是要明白，除了从一个`App`结构中观察场景相位，你也可以从`Scene`和`View`结构中观察它。这就是它如此灵活的原因，但这些表明了非常不同的事情。从`View`观察为包含视图的场景提供相位。从`Scene`观察为场景提供相位。只有从`@main` `App`观察，就像我们上面的例子一样，它才会为应用程序中的所有场景提供聚合阶段。这意味着——不管你的应用有多少场景——一旦第一个场景进入前景，最后一个场景进入背景，那么`App`的场景相位就会更新。

这就是全部，除了…

# 非活动状态

监控阶段的`active`和`background`状态可能就是您所需要的。然而，如果您想利用`inactive`状态，还有一点更好的方法。进入背景的场景的 SwiftUI 生命周期将从`active`到`inactive`再到`background`。从后台出来的时候是反过来的(`background`到`inactive`到`active`)。随着`inactive`在两个方向上被使用，你如何在进入后台之前利用不活动状态来清理？还是在再次激活之前刷新数据？

检测非活动状态是进入活动状态还是后台状态只需要很小的变化。由于 SwiftUI 基于 struct 的架构，这不像给`ScenePhaseApp`添加一个属性来跟踪之前的场景阶段那么容易，但仍然非常容易。同样，有许多不同的方法来处理这个问题，但是我将向您展示一种使用名为`AppManager`的 Swift 类来存储这些数据的方法。

该类是单例的，包含一个属性`isActive`。该属性将跟踪应用程序是否处于活动状态，我们可以使用它来确定`inactive`的场景阶段是进入背景还是前景。回到`ScenePhaseApp`结构，我们将场景的`onChange`事件改为:

添加或修改了三行代码来实现这一点:

1.  在`active`的例子中，我们将应用程序管理器的`isActive`属性设置为`true`。
2.  在`background`案例中，我们将应用程序管理器的`isActive`属性设置为`false`。
3.  最后，我们更改`inactive`案例的`print`语句，打印应用程序是进入活动状态(前台)还是后台状态。这是你放置代码的地方，为应用程序进入前台和/或后台做准备。

运行上面的代码时，控制台输出将显示:

```
Active
```

应用程序的背景将显示:

```
Inactive: entering background
Background
```

将应用程序带回前台会显示:

```
Inactive: entering foreground
Active
```

要查看所有这些代码，您可以在 [GitHub](https://github.com/CoreyWDavis/ScenePhase) 上下载我的`ScenePhase`项目。

# 继续阅读

*   苹果的[场景阶段文档](https://developer.apple.com/documentation/swiftui/scenephase)
*   苹果的[通知中心文档](https://developer.apple.com/documentation/foundation/notificationcenter)
# 当协同程序满足 Android 权限时

> 原文：<https://betterprogramming.pub/when-coroutines-meet-android-permissions-a1f048e70f74>

## 用协程处理单行运行时权限

![](img/1f0eda2db1c93a68cc7dbf2b766b6998.png)

照片由 [Fotis Fotopoulos](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在本文中，您将了解如何使用协程从 Android Marshmallow 处理 Android 运行时权限。这种方法可以用最少的代码轻松处理 Android 组件中的权限。你将不再需要处理回调和`onResult`函数。

# 概观

我们将要创建的过程非常简单。我们需要创建一个基础片段来处理系统所需的所有权限处理。因此，核心部分将与代码的其余部分隔离开来，附带的好处是可重用性。

然后，我们必须将这个片段声明为一个抽象类，以便它可以扩展。之后，创建另一个片段，我们用基本片段扩展它。这里，我们将使用协程来观察基础中权限执行的状态，并将其更新到调用站点。

# 我们开始吧

为了使状态流不那么模糊，我们必须创建一个密封的类，并包含来自系统的所有可能的权限回调和必要的信息，如结果代码。

通常，有四种类型的结果:

*   的确;因为
*   拒绝
*   展示理性的信息
*   永久拒绝。

看看覆盖所有结果类型的密封类:

处理权限的自定义密封类

# BasePermissionController

作为计划的一部分，我们必须创建一个名为`BasePermissionController`的抽象类，并用`Fragment`扩展它。看一看:

我们必须创建一个抽象函数，可以导入到扩展基础的片段中，以便快速传递结果。看一看:

我们需要创建一个函数来处理与系统和用例的交互，比如向用户显示一个 rational 消息。然后我们必须将数据转换成我们的密封类。别说了。看一下代码:

处理系统权限

我们所做的很简单:首先，我们维护了一个`hashmap`请求，我们需要用结果代码作为键来执行这些请求。然后，我们必须检查所请求的权限是否已经被授予，或者我们是否必须显示一条合理的消息。如果是这样，我们用适当的类型创建一个密封的类对象，并将其传递回去。

另一方面，在我们必须请求访问的地方，我们已经将结果代码添加到映射中，并再次调用该函数。当第二次执行时(因为代码已经存在于映射中)，流进入`hashmap`索引块并触发实际的权限执行。

在用户与权限对话框交互之后，我们将在`onRequestPermissionsResult`上收到结果。我们需要创建一个密封类的新实例，并将数据传递回调用站点。看一看:

最后，我们完成了核心权限处理。当我们把所有的部分放在一起时，看看基类:

# 许可控制器

然后我们需要创建另一个名为`PermissionController`的类，并用`BasePermissionController`扩展它。接下来，我们要导入抽象函数`onPermissionResult`。看一看:

现在，是时候使用协程编写实际的逻辑了。一旦从基本控制器调用了`onPermissionResult`，我们需要将`permissionResult`传递回调用站点。为了用协程做到这一点，我们使用了`CompletableDeferred`:

> “一个可以通过公共函数`*complete*`或`*cancel*`完成的`*Deferred*`。
> 
> …
> 
> 这个接口上的所有函数[以及从它派生的所有接口]都是线程安全的，可以在没有外部同步的情况下从并发协同例程中安全地调用。”—[GitHub 上的 kot Lin](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-completable-deferred/)

所以我们必须创建一个类型为`PermissionResult`的`CompletableDeferred`实例，并在`onPermissionResult`函数中调用它。看一看:

# 快速存取

为了使调用点的权限处理更加流畅，我们可以在`PermissionController`的伴随对象上创建一个可公开访问的函数，并编写样板代码。看一看:

# 呼叫站点

在调用点——无论是活动还是片段——我们必须从一个挂起函数或带有`Dispatcher.Main`的协程作用域中调用`requestPermissions`。看一看:

一旦我们收到数据，我们就可以使用`when`关键字处理它，并根据状态进行导航。看一看:

# 借鉴与启发

目前就这些。希望你学到了有用的东西。感谢阅读！

我们在这里使用的所有代码都是从 sagar-viradiya/eazypermissions 示例中提取的。如果您感兴趣，请随时查看以下资源库:

[](https://github.com/sagar-viradiya/eazypermissions) [## Sagar-vira diya/eazy 权限

### 一个轻量级的 Android 库，它包装了运行时权限的样板代码，并允许你请求权限…

github.com](https://github.com/sagar-viradiya/eazypermissions)
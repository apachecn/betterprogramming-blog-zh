# 如何检测 Android 应用程序更新

> 原文：<https://betterprogramming.pub/how-to-detect-an-android-app-update-7ded8560af31>

## 优雅地在两个版本之间执行迁移代码

![](img/649960302a2f03ce0668053d9c14e44d.png)

照片由[张家瑜](https://unsplash.com/@danielkcheung?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

假设您正在重新访问应用程序中的一个现有特性。有时，它与以前的版本有很大的不同，你会想就此进行交流。您可能想让您的用户拥有这一更新的功能。

您希望在发布此功能时触发这样的事件。然而，通知升级你的应用的用户才是最重要的。它不会给机上新用户带来重温体验的价值。

Android SDK 没有提供区分用户如何获得应用程序的机制。他们可能通过以下方式做到了这一点:

*   从头开始下载您的应用程序。
*   从现有版本更新。

如果你有一个比较两个版本的机制，那会很有帮助。让我们看看如何实现这一点。

# 比较两个不同的版本

不同公司的命名版本可能有所不同。我假设你赞同`MAJOR.MINOR.PATCH`命名约定。如果是这样，我推荐使用下面的[库](https://github.com/G00fY2/version-compare)。

将这个依赖项添加到您的`build.gradle`文件中:

```
dependencies {
  implementation 'com.g00fy2:versioncompare:1.3.4'// latest version
}
```

这个库嵌入了版本比较。它们处理`MAJOR.MINOR.PATCH`命名约定以及预发布后缀。

根据您的版本命名约定，您可以自由设置您的版本比较机制。

# 检测更新

使用您的版本比较机制，您可以创建一个`UpdateDetector`类。

这个类将负责存储当前版本。对于这样的数据，`SharedPreferences`似乎足够了，尽管`[DataStore](https://android-developers.googleblog.com/2020/09/prefer-storing-data-with-jetpack.html)`的到来可能会很快成为更好的候选。

您还需要将当前版本保存在 RAM 中。这样，您可以在整个用户会话期间处理迁移。

最后，该类应该公开一个回调来执行迁移。

下面是您可以想到的实现。出于方便的原因，接下来的类依赖于带有[句柄](https://developer.android.com/training/dependency-injection/hilt-android)的依赖注入。

并且处理版本的类存储:

# 处理迁移

既然您已经实现了更新检测器，那么您就拥有了运行迁移的所有工具。你可以触发任何你想要的代码给所有来自任何版本的用户到一个专用版本。

确保您在您的`Application`类中确认了版本更新。您仍然可以访问以前的版本，并将其与当前版本进行比较，因为检测器将它存储在 RAM 中。

最后，您可以在代码中的任何地方注入检测器(这是一个单例)。以您想要的方式处理从一个版本到另一个版本的迁移。

# 关于应用程序更新检测的思考

这种更新机制在概念上仍然是基本的。更好的存储系统将防止更新事件的丢失。到目前为止，您的用户可能永远不会触及您的迁移处理。

想象一下，在到达我们想让他们上车的地方之前，他们决定退出应用程序。下次您的用户启动应用程序时，应用程序会将您的用户视为已更新。

此外，你可能已经意识到，只要你预料到这一点，这一机制就能很好地发挥作用。越早发布，越多的用户会在他们的设备上存储以前的版本。

如果你在开始使用它之前没有交付很长时间，你将无法区分新用户和现有用户。

如果你想长期发展，我强烈建议尽早加入这样的机制。
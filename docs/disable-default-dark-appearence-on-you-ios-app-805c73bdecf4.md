# 禁用 iOS 应用程序的默认黑色外观

> 原文：<https://betterprogramming.pub/disable-default-dark-appearence-on-you-ios-app-805c73bdecf4>

## 不想要黑暗模式怎么办？

![](img/2d9cf0d768296b29f02d22fb22bb1eb2.png)

威廉·卡尔森在 [Unsplash](https://unsplash.com/s/photos/dark-to-light?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

自从苹果发布其新的 SDK 和 Xcode 的第十一次发布以来，应用程序都被鼓励支持新的 iOS 13 黑暗模式。但是如果我们不想用呢？

如果你运行一个为 iOS 12 开发的项目，你的应用程序只会在 iOS 13 上显示暗的 UI 视图，导致你的标签不能正确显示，破坏了你对 UI 应该如何显示的光模式概念。

有几种方法可以禁用黑暗模式，我们将在下面介绍。

# 1.在 Info.plist 中设置 UIUserInterfaceStyle

强制你的应用程序显示轻量级界面的最简单的方法是在 Info.plist 中设置`UIUserInterfaceStyle`，指定首选的界面样式。

```
<key>**UIUserInterfaceStyle**</key>
<string>**Light**</string>
```

只要重新构建应用程序，你会在整个应用程序中看到一个轻便的界面。

# 2.设置 overrideUserInterfaceStyle 属性

每个 UIViewController、UIView，甚至 UIWindow 都可能对该属性的设置作出响应——显然使 UIView、整个控制器或主窗口显示 light 界面。

您应该将设置包装到一个条件`#available`中，以避免编译问题:

```
if #available(iOS 13.0, *) {
        // prefer a light interface style with this:
        **overrideUserInterfaceStyle** = .light
}
```

这个属性可以设置到`viewDidLoad()`上的 UIViewController 中，也可以设置到 AppDelegate.swift 的应用程序的`didFinishLaunchingWithOptions`方法中，访问可选的`window?`属性。

这完全取决于你的应用架构和你想要的工作方式。您可以将整个主窗口设置为采用 light 接口，或者在特定的 UIViewController 实例中使用它。

这就是为什么你必须在…

```
window?.overrideUserInterfaceStyle = .light
```

…在 AppDelegate 中。或者您可以使用:

```
view.overrideUserInterfaceStyle = .light
```

…在您的 UIViewController 中。可以看到，属性名总是相同的:`overrideUserInterfaceStyle`。

# 最后的想法

在你的应用中避免黑暗模式可能是个错误的想法。你的应用应该支持新的黑暗模式，以提高 iOS 13 的 UX，因为你的应用应该在首选风格的背景下。

大多数专业和流行的应用程序发布了支持黑暗模式的更新，我建议只有当你的应用程序*必须*符合光明接口时才强制使用光明模式。
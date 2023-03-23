# URLSession:后台下载和上传的常见陷阱

> 原文：<https://betterprogramming.pub/urlsession-common-pitfalls-with-background-download-upload-tasks-8d479a1698fe>

## 是小事造成了不同

![](img/17a6279ac8d9deb66a62e8770dcf9533.png)

泰勒·尼克斯在 [Unsplash](https://unsplash.com/search/photos/escalator?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

URLSession 使您能够从后台下载和上传。虽然基础看起来很简单，但要做对却相当困难。从可用的资源和文档来看，有相当多的小事情对于后台上传工作非常重要。

对于我的日常工作，我正在为 WeTransfer 的[收集](https://collect.wetransfer.com/)应用程序开发新功能。其中一个功能是后台下载和上传，这两个功能都可以通过应用程序扩展来触发。尽管第一个版本很容易安装，但它有很多缺陷。

# 常见陷阱

在这篇文章中，我将讨论一些常见的陷阱。如果你正在寻找设置后台下载和上传的方法，看看苹果的这个[文档](https://developer.apple.com/documentation/foundation/url_loading_system/downloading_files_in_the_background)来启动你的实现。当然，在那之后，回头读一读我的经验，让自己远离常见的陷阱。

## 1.每个 URLSessionConfiguration 都需要一个唯一的标识符

当您的主机应用程序通过以下方法传递的标识符重新启动时，如果您从应用程序扩展触发后台下载或上传，这主要是一个问题:

```
func application(_ application: UIApplication, handleEventsForBackgroundURLSession identifier: String, completionHandler: @escaping () -> Void)
```

您可以忽略传递的标识符，并像对您的主机应用程序一样重新构建 URLSession。然而，使用应用程序扩展，您需要处理多个 URLSession 实例。其中每个都需要自己的自定义标识符，并且在重建会话时也需要使用该标识符。

我是在阅读了苹果公司的[文档](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html#//apple_ref/doc/uid/TP40014214-CH21-SW1)后得出这个结论的，该文档指出:

> 因为一次只有一个进程可以使用后台会话，所以您需要为包含它的应用及其每个应用扩展创建不同的后台会话。(每个后台会话应该有一个唯一的标识符。)当应用程序在后台启动时，建议您的包含应用程序仅使用由其一个扩展创建的后台会话来处理该扩展的事件。如果您需要在包含您的应用程序中执行其他网络相关任务，请为它们创建不同的 URL 会话。”

对我们来说，这基本上意味着您需要为任何传入的标识符创建一个新的 URLSession，并缓存该实例。

```
/// Contains any `URLSession` instances associated with app extensions.
private lazy var appExtensionSessions: [URLSession] = []

/// Creates an identical `URLSession` for the given identifier or returns an existing `URLSession` if it was already registered.
///
/// - Parameter identifier: The `URLSessionConfiguration` identifier to use for recreating the `URLSession`.
/// - Returns: A newly created or existing `URLSession` instance matching the given identifier.
private func session(for identifier: String) -> URLSession {
    if let existingSession = appExtensionSessions.first(where: { $0.configuration.identifier == identifier }) {
        return existingSession
    } else {
        let configuration = URLSessionConfiguration.background(withIdentifier: identifier)
        configuration.sharedContainerIdentifier = appGroup
        let appExtensionSession = URLSession(configuration: configuration, delegate: self, delegateQueue: sessionOperationQueue)
        appExtensionSessions.append(appExtensionSession)
        return appExtensionSession
    }
}
```

## 2.基于包的标识符

为了安全起见，您可以使用以下代码来确保您的 URLSessionConfiguration 标识符始终是唯一的:

```
let appBundleName = Bundle.main.bundleURL.lastPathComponent.lowercased().replacingOccurrences(of: " ", with: ".")
let sessionIdentifier: String = "com.wetransfer.networking.\(appBundleName)"
let configuration = URLSessionConfiguration.background(withIdentifier: sessionIdentifier)
```

## 3.不要忘记共享容器标识符

如果你在应用扩展中实现后台下载和上传，你需要设置共享容器标识符。由于应用程序扩展很快被终止，你需要确保你正在后台 URLSession 上执行这些数据任务。关于设置共享容器的指导，您可以参考[与您的包含应用](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html#//apple_ref/doc/uid/TP40014214-CH21-SW1)共享数据。相同的链接包含处理应用程序扩展中常见场景的信息，还包括下载和上传。

您可以按如下方式设置标识符:

```
let configuration = URLSessionConfiguration.background(withIdentifier: "swiftlee.background.url.session")
configuration.sharedContainerIdentifier = "group.swiftlee.apps"
```

## 4.仅支持从文件上传任务

这意味着你必须从一个文件上传。首先在本地保存文件，然后从该文件位置开始上传。应用程序退出后，从数据实例或流上传会直接失败。因此，请密切注意这里，因为它可能看起来第一次工作。然而，我们在后台上传，这必须工作！

## 5.这个参数真的很伤人

我记得自己坐在书桌前，盯着屏幕。直到我插上手机充电器，下载才开始。在阅读了几乎所有方法和参数的文档后，我遇到了`isDiscretionary`属性:

> 对于使用`background(withIdentifier:)`方法创建的配置对象，使用此属性让系统控制何时应该进行传输

这让我开始思考。继续阅读:

> “当传输大量数据时，建议您将该属性的值设置为`true`。这样做可以让系统将这些传输安排在最适合设备的时间进行。例如，系统可能会延迟传输大文件，直到设备插上电源并通过 Wi-Fi 连接到网络。”

这完全说得通！

要明确的是，在某些情况下将这个属性设置为`true`是很好的。然而，对于 Collect 应用程序的用例，我们需要即时下载和上传。因此，我们必须将该属性设置回其默认值`false`。

# 结论

就是这样！我的很多心得都与你分享。希望它能节省你的时间和一些麻烦。让我知道你的缺点，这样我就可以继续改进这篇文章。

谢谢！
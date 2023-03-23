# 创建 SwiftUI 循环背景视频

> 原文：<https://betterprogramming.pub/creating-a-swiftui-looping-background-video-f6fc3acd01b8>

## 仔细看看在 SwiftUI 中实现平滑的视频背景

![](img/78cada85e4fb34a09cc4c634cf9f55da.png)

由[萨姆·穆卡达姆](https://unsplash.com/@sammoqadam?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们都见过很多平滑背景的应用程序循环播放视频，这让我们感到惊讶。

让我们深入研究一下，看看我们如何为我们的应用程序做同样的事情。

在`UIKit`中，我们通过利用`AVFoundation`框架和`UIView`来做到这一点。既然苹果有强大的机制与`UIKit`沟通，为什么不在`SwiftUI`中做同样的事情呢？

此外，如果您过去使用`UIKit`与类似的任务进行过交互，您可能会注意到，为了创建流畅的视频体验，我们还需要考虑一些特定的状态:

1.  推动/弹出下一个`View`或`UIViewController`
2.  应用程序进入后台
3.  应用程序重回前台

这些特殊的突变可能会产生奇怪的副作用和故障，比如视频停止，不再继续，甚至快进到未来的某个特定点。

作为软件工程师，我们的目标是不遗余力。

让我们从一个简单的`UIView`开始，就像我们在`UIKit`时代一样，利用`AVFoundation`框架及其组件。代码如下:

现在是时候通过使用`UIViewRepresentable`来桥接`UIKit`和`SwiftUI`了。

拼图的最后一块是将上面的所有代码组装到一个`SwiftUI` `View`中，如下所示:

所以我们从`UIKit` → `UIViewRepresentable`(两个世界之间的桥梁)→ `View` ( `SwiftUI`)

注意我们是如何通过控制播放器和在不同的状态变化时暂停/恢复来处理和控制我们之前提到的所有四种状态的？

还有，`AVQueuePlayer`需要有状态；否则，它将被无效，并在每次屏幕更新和重绘时重置为视频的开始。

现在，如果您想在代码中的任何地方使用这种新的`View`,使用下面的命令调用它:

```
BgdFullScreenVideoView(videoName: "dashboard_video")
```

关于完整的演示和完整的源代码，GitHub 上什么都有。

[](https://github.com/catalinpatrascu/SwiftUIBackgroundVideoPlayer) [## GitHub-catalinpatrascu/SwiftUIBackgroundVideoPlayer:swift ui 中的全屏视频背景

### SwiftUI 使用中的全屏视频背景:只需将 BgdFullScreenVideoView 添加到任何视图的主体中，然后…

github.com](https://github.com/catalinpatrascu/SwiftUIBackgroundVideoPlayer)
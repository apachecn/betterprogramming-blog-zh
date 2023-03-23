# 使用洛蒂动画

> 原文：<https://betterprogramming.pub/using-lottie-animations-7670b7682e38>

## 向 Swift 应用程序添加动画的分步指南

![](img/eee3f60e8d36a95b1a35752d56d534bd.png)

随着 [Lottie](https://airbnb.io/lottie/#/) 的发布，Airbnb 让将动画融入 Swift 项目变得如此简单。在这篇文章中，我将一步一步地向你展示如何在你的下一个应用程序中添加漂亮的动画。

# 进口洛蒂

首先，我们需要引进洛蒂。我们可以通过终端做到这一点；如果您尚未设置 pod，请通过键入以下命令来设置:

```
pod init
```

该命令完成后，您可以使用 Vim 或 TextEdit 打开 Podfile。打开后，将以下代码复制到 Podfile 中:

保存完 Podfile 后，使用以下命令安装 Lottie 框架:

```
pod install
```

现在我们准备在我们的应用程序中使用 Lottie。从现在开始，当你打开应用程序时，你需要打开`xcworkspace`文件。

# 添加动画

让我们开始在 Xcode 中设置我们的故事板。

1.  将视图拖到画布上。
2.  为视图的自定义类键入`AnimationView`。
3.  按住 Control 键将视图拖到`ViewController`文件中，建立一个 IBOutlet 连接，并将其命名为`AVView`。

现在你可以使用现有的来自 https://lottiefiles.com 的动画(我将在这篇文章中使用)或者你可以使用你自己的特效。找到想要的动画后，下载它并从 finder 中将 JSON 文件拖到项目中。我们现在需要导航到我们的`ViewController`文件，并在文件顶部导入`Lottie`。在`ViewDidLoad()`中，您需要输入以下代码:

在上面的代码中，我们实际上引用了 Lottie JSON 文件，并将其内容模式设置为我们想要的。从那里，它将动画作为子视图添加到我们在故事板中创建的视图中，然后我们将它的循环模式设置为连续循环动画或只播放一次。现在，您可以运行您的应用程序，并看到您的洛蒂动画活过来！
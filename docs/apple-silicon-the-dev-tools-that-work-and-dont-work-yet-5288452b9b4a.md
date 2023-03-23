# 苹果芯片:可行和不可行的开发工具

> 原文：<https://betterprogramming.pub/apple-silicon-the-dev-tools-that-work-and-dont-work-yet-5288452b9b4a>

## macOS 上软件工程师的有用清单

![](img/71effe2d37a563095b6aef652a5f1a97.png)

图片来自[苹果 2020 年 11 月活动](https://www.apple.com/apple-events/november-2020/)。

新款苹果硅苹果电脑已经上市有一段时间了。在相同的价位上，这些机器显示出了巨大的处理能力和电池改进。因此，大多数科技评论者毫不犹豫地向人们推荐它们，供日常使用。

然而，对于软件开发人员来说，情况就不同了。

目前新阵容有两个主要问题。首先，外部显示器支持有限。新的 M1 MacBook Air 和 MacBook Pro 仅通过雷电 3 支持一个外部显示器。Mac mini 没有内置显示器，通过一个雷电 3 端口和一个 HDMI 2.0 端口支持两个外部显示器。对于需要更多外部显示器进行开发的人来说，这可能是一个障碍。

其次，也是更重要的，人们关心基于 ARM 的新机器上开发工具的可用性和性能。我个人并不担心他们的表现。苹果工具，如 Xcode、[运行惊人](https://youtu.be/XQ6vX6nmboU?t=154)，但即使是运行在 Rosetta 2 [上的基于 x86 的应用程序也被证明](https://www.youtube.com/watch?v=VXgLBa5jgr8)优于当前基于英特尔的 MacBooks。我真正担心的是，许多流行的开发工具根本无法在新款 MacBooks 上运行。

为了帮助你决定是否应该做出改变，我整理了一份工具列表及其状态如下:

*免责声明:我目前没有 M1 MacBook。这只是我在网上收集的信息。*

# 虚拟化软件

*   码头工人:不起作用。由于 Golang 依赖性，原生支持在 2021 年 2 月之前不会准备好。见[官方博文](https://www.docker.com/blog/apple-silicon-m1-chips-and-docker/)。
*   VMWare:不起作用。[原生支持即将推出，但还没有时间表](https://twitter.com/VMwareFusion/status/1326229094648832000)。
*   Parallels Desktop:不工作。[原生支持即将推出，但还没有时间表](https://www.parallels.com/blogs/parallels-desktop-apple-silicon-mac/)。
*   VirtualBox:在不知道它是否可用的情况下不工作。

# 工具

*   自制软件:在接下来的几个月里将不会有本地支持。它可以通过 Rosetta 2 安装，但会有“非常糟糕的体验”

# 5 月

*   Eclipse: [作用于 Rosetta 2](https://www.youtube.com/watch?v=9NnxtAX1ROA) 。
*   Visual Studio 代码:[在罗塞塔 2](https://www.youtube.com/watch?v=1l21xUwy5d0) 上工作。苹果硅[的实验产品已经上市](https://code.visualstudio.com/insiders/#osx)。问题可以在 GitHub 上追踪[。](https://github.com/microsoft/vscode/labels/%3Aapple%3A%20si)
*   JetBrains IDEs:应该都能在 Rosetta 2 上工作(比如 [IntelliJ](https://www.youtube.com/watch?v=vo0Mo0jr7dM) ， [PyCharm](https://www.youtube.com/watch?v=E6JDzg6-_JM&t=9s) ， [WebStorm](https://www.youtube.com/watch?v=l6iOCaXFGyA) )，但不是原生的。查看 JetBrains 上的状态[。](https://youtrack.jetbrains.com/issue/JBR-2526)
*   Android Studio:IDE 可以工作，但是 Android 模拟器不行([来源](https://stackoverflow.com/a/64907261)、[来源](https://youtu.be/vc4dwGF3jlU?t=697))。

# 语言

*   Java: [原生工作](https://www.azul.com/downloads/zulu-community/?os=macos&architecture=arm-64-bit&package=jdk)。
*   Node.js:在 Rosetta 2 上工作，但不是本地的。在 GitHub 上跟踪状态[。](https://github.com/nodejs/TSC/issues/886)
*   Python:应该通过[这个](https://github.com/python/cpython/pull/22855)问题的外观自然地工作。
*   r:在 Rosetta 2 上工作，但不是本地的。见[官博](https://developer.r-project.org/Blog/public/2020/11/02/will-r-work-on-apple-silicon/index.html)。
*   Golang: [在 Rosetta 2 上似乎有缺陷](https://github.com/golang/go/issues?q=is%3Aissue+is%3Aopen+rosetta)，但是[原生支持将在 2021 年 2 月](https://blog.golang.org/11years)到来。
*   Rust: [在 Rosetta 2 上工作，但是没有原生支持的时间表](https://github.com/rust-lang/rust/issues/73908)。

# 结构

*   。NET /。网芯:[。NET 5 可以在 Rosetta 2 上运行，而。NET 6 会原生工作](https://github.com/dotnet/runtime/issues/43313)。
*   Flutter:适用于 Rosetta 2，但是你需要在物理设备上开发。参见[官方维基](https://github.com/flutter/flutter/wiki/Developing-with-Flutter-on-Apple-Silicon)和 GitHub 上的问题[。](https://github.com/flutter/flutter/issues/60118)
*   电子:在 11.0.0 中被原生支持。参见[发行说明](https://www.electronjs.org/blog/electron-11-0)。

# 图书馆

*   Tensorflow:由苹果[私有分叉版 Tensorflow](https://github.com/apple/tensorflow_macos) 原生支持。

# 结论

总而言之，我很高兴地报告，大多数代码编辑器、编程语言和框架在新款 MacBooks 上运行良好。然而，在对虚拟化软件的支持方面有一个重大缺陷，这种情况可能会持续几个月。如果您的开发工作流程依赖于这些工具，那么您绝对应该推迟切换。

感谢所有在网上分享他们开发经验的好心人，包括 [DevOps Directive](https://www.youtube.com/watch?v=ji9j-JWbgAo) 、 [Attila Vágó](https://levelup.gitconnected.com/hello-new-world-living-with-and-developing-on-apple-silicon-m1-6bbc3617c9be) 、 [David Fekke](https://github.com/davidfekke/appleM1SECompatibility) 以及我上面链接的许多其他人。

如果你有任何信息或更正，请在评论中分享。我很乐意修改列表。希望这有所帮助！
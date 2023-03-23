# 在 Swift 包中添加核心数据

> 原文：<https://betterprogramming.pub/core-data-in-a-swift-package-86bf759c3b6>

## 让我们为 Swift 产品包添加一个核心数据管理器和模型

![](img/0102c5e562a2aa9bf314e05bcd0f98fa.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com/s/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

当创建一个应用程序时，它的架构应该是模块化的，以便可以清楚地识别和管理依赖关系，并且尽可能简单。

Swift 包是一种分离关注点的便捷方式，例如，通过将持久存储提取到包中。然而，当谈到核心数据存储时，这并不像看起来那么简单。

本文描述了一种方法，可以避免类似“没有找到模型”这样的错误。

# 基本要求

应该有一个包含核心数据管理器和核心数据模型的“持久性”包。

其他软件包应该能够使用该软件包，以便访问管理对象以及管理器本身。

此外，应可以设置不同的经理:

*   临时存储的管理器，用于测试或其他目的。
*   本地商店的经理(如果没有活跃的 iCloud)。
*   云商店的经理，如果 iCloud 可用的话。

最后，不应该有单例实现。

# 实施

在 Xcode 中创建新的包“Persistence”后，我们需要一个包配置:

接下来，我们添加一个协议，管理器变量可以符合该协议:

该配置有助于我们提供基本设置:

# 子类化容器

实际上，我们现在只需要使用一个`NSPersistentContainer`来设置商店。然而，根据 Apple 的说法，这个类应该被子类化，以便更容易地控制商店应该存储在哪里。

因此，我们创建了两个子类(本地或临时存储，以及云存储)。在这些子类中，我们覆盖了`defaultDirectoryURL`方法，以便将商店放在适当的位置:

# 符合协议

现在我们创建我们定义的协议的第一个实现:

在这里，我们为一个给定的配置设置了一个临时存储，它提供了模型的名称。有一个重要的部分隐藏在`?TemporaryPersistenceManager.model(for:)`函数中:

这里有两件事是相关的:

*   首先，必须从包中检索 URL，因此必须使用`Bundle.module.url(forResource:withExtension:)`。
*   第二，虽然通常使用扩展名`momd`，但这里有必要只使用`mom`。否则，将无法正确找到模型。

你现在可能想以类似的方式实现一个`LocalPersistenceManager`和一个`CloudPersistenceManager`，我不会在这里指出这一点。

# 使用我们的经理

现在，将包添加到我们的应用程序后，我们可以使用那里的管理器。在我们的例子中，我们使用[解析器](https://github.com/hmlongco/Resolver)来注册使用管理器的`MetaDataService<>` 服务:

这些`MetaDataService<>`服务被定义在一个包`Meta`中。该包只是引用了`Persistence`包:

# 结论

我与运行时(不是构建时)出现的错误做了很多斗争，因为没有找到模型。这里给出的解决方案现在可以正常构建和运行了。

然而，还有一件事:尽管 Xcode 控制台在第一次设置存储时显示一个错误(类似于“无法创建文件”)，但是存储设置正确。如果你知道为什么，请留下评论。

感谢您阅读本文，希望它能在类似情况下为您节省一些故障排除时间。
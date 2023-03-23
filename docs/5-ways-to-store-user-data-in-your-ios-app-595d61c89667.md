# 在 iOS 应用程序中存储用户数据的 5 种方法

> 原文：<https://betterprogramming.pub/5-ways-to-store-user-data-in-your-ios-app-595d61c89667>

## 根据您的需求选择合适的存储

![](img/2730f240cac4ab3076952fd6db353d91.png)

[张彦宏](https://unsplash.com/@alexcpl?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

在考虑跑步应用的功能时，我们会考虑的主要功能有:

*   开始跑步。
*   追踪用户的位置。
*   显示跑步的实时统计数据。
*   分享用户的表现。

这些都是优秀运行应用的关键功能，它们属于[功能需求](https://en.wikipedia.org/wiki/Functional_requirement) 类别:

> “在[软件工程](https://en.wikipedia.org/wiki/Software_engineering)和[系统工程](https://en.wikipedia.org/wiki/Systems_engineering)中，**功能需求**定义了[系统](https://en.wikipedia.org/wiki/System)或其组件的功能，其中功能被描述为输出和输入之间的行为规范。”— [维基百科](https://en.wikipedia.org/wiki/Functional_requirement)

这些需求依赖于其他功能才能正常工作。例如，要在他们喜欢的社交媒体上分享用户的表现或登录系统，应用程序需要执行网络调用。网络是一种较低层次的功能，它支持其他功能的开发。

在这些低级功能中，有在本地存储数据的功能。数据存储是应用程序开发中的一个关键决策。它允许我们:

*   通过缓存数据减少加载时间。
*   保留登录信息。
*   使我们的应用程序能够脱机工作。
*   记住用户设置。

在本文中，我将介绍五种最常见的存储和检索数据的方法。

# 1.用户默认值

让我们从数据存储中最简单的开始，`[UserDefaults](https://developer.apple.com/documentation/foundation/userdefaults)`。

这是存储和检索信息的最常见和最舒适的方式。`UserDefaults`作为键值存储，用`Strings`作为键。检索大部分是打字的。有具体的方法来检索精确的数据类型(`bool(forKey:)`、`int(forKey:)`等)。)和一个更通用的`object(forKey)`来检索`Any`。然而，使用`set(_ value: Any, for key: String)`，我们无法存储所有内容。

来自[苹果开发者](https://developer.apple.com/documentation/foundation/userdefaults/1414067-set):

> “`value`参数只能是属性列表对象:`[NSData](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/PropertyLists/OldStylePlists/OldStylePLists.html#//apple_ref/doc/uid/20001012-47169)`、`[NSString](https://developer.apple.com/documentation/foundation/nsstring)`、`[NSNumber](https://developer.apple.com/documentation/foundation/nsnumber)`、`[NSDate](https://developer.apple.com/documentation/foundation/nsdate)`、`[NSArray](https://developer.apple.com/documentation/foundation/nsarray)`或`[NSDictionary](https://developer.apple.com/documentation/foundation/nsdictionary)`。对于`NSArray`和`NSDictionary`对象，它们的内容必须是属性列表对象。更多信息，请参见[什么是属性列表？](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/PropertyLists/AboutPropertyLists/AboutPropertyLists.html#//apple_ref/doc/uid/10000048i-CH3-54303)[房产列表编程指南](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/PropertyLists/Introduction/Introduction.html#//apple_ref/doc/uid/10000048i)中的

尽管如此，创建一个小的扩展来支持任何`Codable`类型的存储和检索操作还是相当容易的。

*注意:我们不能用* `*set*` *作为* `*setCodable*` *方法的名称。如果我们这样做，第 6 行的* `*self.set*` *函数调用将无限递归地调用我们正在定义的方法，在你的应用程序中造成崩溃。记住* `*Data*` *就是* `*Codable*` *！*

`UserDefaults`可以通过使用`standard`静态属性来检索，这是一个单例属性，可以方便地访问这个存储设备。

## 何时使用它

顾名思义，我们要用`UserDefaults`来存储用户的喜好。我们应该使用它来存储小块信息(例如，用户是喜欢亮模式还是暗模式，他们想在什么时候收到每日提醒，他们是否真的想收到通知，等等。).

根据经验，如果你的应用程序中有一个设置屏幕，`UserDefaults` 是一个很好的选择。

# 2.钥匙链

一些应用程序需要存储我们想要保护的私人信息或数据:密码、证书和类似的项目。

对于这个特定的用例，苹果提供了钥匙串和[安全框架](https://developer.apple.com/documentation/security)。钥匙串是磁盘上的一个特殊文件。它是硬件加密的，有一堆非常低级的 API 来访问它。

要使用它，我们需要:

*   定义查询字典。
*   从安全框架中调用一个全局函数(根据我们的需要，它可以`SecItemCopyMatching`检索一些数据、`SecItemAdd`添加一个新项、`SecItemUpdate`更新或`SecItemDelete`删除它)。
*   分析全局函数返回的`OSStatus`代码。
*   如果我们正在检索某个东西，我们需要将检索到的元素转换为`Data`。

如果你真的需要在你的应用程序中使用钥匙串，搜索一个好的库，让它为你做脏活。这会节省你的时间和头痛。下面是 GitHub 上明星最多的三个钥匙扣库。选择你的毒药！

*   [钥匙串访问](https://github.com/kishikawakatsumi/KeychainAccess) (6.2K 星)
*   [代客](https://github.com/square/Valet)来自四方(3.6K 星)
*   [锁匠](https://github.com/matthewpalmer/Locksmith) (2.9K 星星)

## 何时使用它

每当我们需要存储必须加密并保持安全的小块信息时(例如密码、凭据、令牌等)。).

考虑到一切都必须存储为`Data`。因此，我们只需要传递可以序列化为该类型的对象和值。

# 3.文件系统

当您需要存储的内容不属于上述情况时，它必须转到[文件系统](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)。

iOS 世界中的文件系统与笔记本电脑上的文件系统完全一样:它使用路径和 URL 来标识磁盘上的资源。但是，在 iOS 开发中，有一些所有开发人员都应该知道的首选文件夹:

1.  `Documents`:这是主文件夹，可以存放用户生成的数据。
2.  `Documents/Inbox`:这是每当另一个 app 要求我们的 app 打开文件时，系统都会创建的一个特殊文件夹。从应用程序的角度来看，此文件夹是只读的。例如，邮件程序会将与您的应用程序相关的电子邮件附件放在此目录中。
3.  `Library`:这是一个文件夹，我们作为开发者，可以在里面存放 app 需要的文件。例如，我们可以存储一个二进制文件，该文件可以向应用程序提供数据。
4.  `Library/Caches`:这是一个特殊的文件夹。在这里，我们可以保存我们可能很快就需要的文件，但不在乎丢失。当我们停止使用这些文件时，系统会删除它们。我们为了更快地加载应用程序而下载的图像应该位于此文件夹中。
5.  `tmp` : 另一个特殊文件夹。存储在此处的文件不会在应用程序启动时保持不变。该目录可能会被系统清除。

应用程序中的文件系统是沙箱化的。您的应用程序(并且只有您的应用程序)可以在运行时访问文件系统。然而，有办法从备份中访问我们存储在磁盘中的文件。

对于需要加密但无法存储在钥匙串中的大文件，考虑在将它们写入文件时使用适当的[加密选项](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/encrypting_your_app_s_files)。在 iOS 中读写极其简单:

读写文本和二进制文件的例子。

## 何时使用它

文件系统是存储我们从网上下载的图像的一个很好的选择，并希望保持方便以减少加载时间。在这种情况下，我们可以使用`Library/Caches`文件夹。

我们可以在`Library`文件夹中创建一个文件来存储我们 app 的状态，以免丢失用户的进度。

如果应用程序允许用户创建内容，那么`Documents`文件夹是存储内容的一个很好的选择。例如，这是用户可以导出的 CSV 文件的位置。

允许用户编辑视频和照片的应用除外。在这种情况下，`CameraRoll`是更好的选择。然而，与`CameraRoll`合作更复杂，需要一篇独立的文章。

# 4.Sqlite

SQLite 不仅仅是一个存储数据的地方，它还是内部数据库事实上的标准。

SQL 数据库要求我们在表中组织模型。然后，它会以最有效的方式在磁盘上存储和检索它们。

SQLite 支持不是现成的。您可以利用不同的库来设置它并在您的应用程序中使用它。这里有几个建议:

*   [SQLite](https://github.com/stephencelis/SQLite.swift) (7.4K 星星)
*   [GRDB](https://github.com/groue/GRDB.swift) (3.7K 明星)

SQLite 框架让我们在文件系统中传递文件路径。他们使用这个文件来创建 SQLite 数据库，然后打开一个到它的连接，以便应用程序与之交互。`Library`文件夹通常是存储 SQLite 数据库的最佳位置。

## **何时使用**

SQLite 是一个数据库。它的最佳用例是当我们拥有大量结构良好的数据时。想象一下用户在使用计步应用程序时执行的所有步骤。

除了存储能力之外，拥有一个 SQL 数据库可以实现高效的检索，并且可以编写复杂的查询来提取有趣的见解。

# 5.核心数据

[核心数据](https://developer.apple.com/documentation/coredata)是 iOS 自带的[对象关系映射(ORM)](https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping) 框架。与 SQLite 一样，核心数据不是存储数据的位置，而是操纵数据的一种方式。你可以把它看作是 SQLite 的进化。

有了这个框架，我们可以利用 Xcode 从一个直观的 GUI 为我们的应用程序创建一个内聚的数据模型。该框架还负责执行数据迁移(或者至少帮助我们完成迁移)，以及将数据与磁盘和多线程同步。

使用核心数据相当复杂。最好按照教程来掌握它:

*   [雷·万德利希](https://www.raywenderlich.com/7569-getting-started-with-core-data-tutorial)(原文)
*   雷·万德利希 (YouTube)

在核心数据中，我们创建一个由`Context`管理的`DataModel`。然后，`Context`与负责从磁盘存储和检索数据的`StorePersistor`协调。

在引擎盖下，核心数据使用存储在`Library/Application Support`文件夹中的 SQLite 数据库。但是，可以传递一个自定义 URL 来从默认位置移动数据库。

## 何时使用它

当我们需要跟踪具有多个实体和关系的复杂对象模型时，核心数据大放异彩。它拥有对上下文和多线程的高级控制，这使得模块化数据访问变得很容易。

如果您正在构建一个已经划分成模块的大型复杂应用程序，核心数据可能是您工具箱中的一个不错的补充。

# 结论

在今天的文章中，我们探讨了在 iOS 中使用数据存储的一些替代方法。我们从基础的`UserDefaults`开始，导航到相当复杂的概念，比如`CoreData`。

我希望这个简短的介绍可以指导你们中的一些人选择正确的位置和正确的技术来存储和检索 iOS 应用程序中的数据。
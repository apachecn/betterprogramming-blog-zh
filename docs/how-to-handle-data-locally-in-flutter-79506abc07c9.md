# 如何在 Flutter 中本地处理数据

> 原文：<https://betterprogramming.pub/how-to-handle-data-locally-in-flutter-79506abc07c9>

## 了解如何在 Flutter 中本地存储数据和管理嵌套数据模型

![](img/3b20d6ab8ccb3f9cb0a404637a9d25d6.png)

照片由[诺德伍德主题](https://unsplash.com/@nordwood?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

不需要在应用程序中存储某种数据的情况很少见。Flutter 提供了多种方法来实现这一点。

对于一些应用程序，我们只需要存储一些键值对，这可以使用`[shared_preferences](https://pub.dev/packages/shared_preferences)`包来完成。对于其他一些 app，我们可能需要存储和查询大量的数据。

在这种情况下，我们可以通过`pub.dev`上的`[sqflite](https://pub.dev/packages/sqflite)`插件使用 SQLite 数据库。另一种保存数据的方法是读写光盘上的文件。

本文专门讨论在文件中存储数据。菜谱还将处理存储嵌套数据模型的情况，这使用`sqflite`插件不容易实现。

这本食谱将带你完成以下步骤:

1.  添加所需的依赖项。
2.  创建`FileHandler`类。
3.  找到正确的本地路径，并创建对文件位置的引用。
4.  实现读取、写入、更新和删除方法。

下面是将用于此示例的嵌套的`User` 数据模型:

# 添加所需的依赖项

在您的`pubspec.yaml`文件中添加`path_provider`依赖项。

# 创建`FileHandler`类

我们需要创建一个单例类，因为我们在整个应用程序中只需要该类的一个实例。现在让我们找到本地路径并创建一个对文件的引用。

# 找到正确的本地路径并创建对文件位置的引用

`path_provider`插件提供了一种平台无关的方式来访问设备文件系统中常用的位置。该插件目前支持对两个文件系统位置的访问:

*   临时目录:系统可以随时清除的临时目录(缓存)。
*   文档目录:存储只有应用程序才能访问的文件的位置。只有在删除应用程序时，系统才会清除目录。

为了持久化数据，我们将文件存储在 documents 目录中，并创建对它的引用。您可以使用`dart:io`库来实现这一点。

# 实现读取、写入、更新和删除方法

为了将数据模型读写到文件中，您需要使用`dart:convert`库中可用的`jsonEncode()`方法对数据进行编码。这将给定的对象转换为字符串。然后，为了读取文件，您可以使用`jsonDecode()`方法将字符串转换为 JSON 对象。然后，您可以轻松地从地图中检索该对象。

要将数据写入文件，需要维护一个`Users`的`Set`。当添加一个新的`User`时，首先将其添加到这个`Set`中，然后使用它写入文件。使用`Set`确保一个元素在文件中只添加一次。此外，您需要用`[equatable](https://pub.dev/packages/equatable)`包中可用的`Equatable`类来扩展您的类，以便比较`User`类的实例。`Equatable`有助于实现基于值的对象比较，而无需显式覆盖`==`和`hashcode`。你可以查看[这篇](https://medium.com/flutter-community/what-is-equatable-package-in-dart-and-flutter-81bbb62e73b9)文章，了解更多关于`Equatable`的内容。

您还需要为类实现`toJson()`和`fromJson()`方法，以便在类对象和 JSON 映射之间进行转换。查看来自 Flutter docs 的这个页面，了解更多关于 JSON 序列化的信息。

现在要读取文件，使用`readAsString()`方法，然后使用`jsonDecode()`方法将字符串转换成一个 JSON 对象(这里是一个`Map<String, dynamic>`列表)。

类似地，实现`update()`和`delete()`方法。

现在一切都设置好了，只需使用`FileHandler.instance`创建一个`FileHandler`类的实例，并使用这些方法来读写文件中的数据。

# 结论

是的，在 Flutter 中通过读写一个文件来处理本地数据就是这么简单！

在 Flutter 中有很多本地持久化数据的方法。使用的方法取决于数据的类型和比例。就嵌套数据模型(如上所述)而言，存储此类数据的最简单方法是将其存储在文件中。

然而，仍然可以有一些更好的方法，请留下评论或联系我[这里](mailto:bharat.sharma1809@gmail.com)如果你知道一个！。

查看由 Flutter 团队编写的关于处理数据的[用 SQLite](https://flutter.dev/docs/cookbook/persistence/sqlite) 持久化数据和[将键值数据存储在光盘上](https://flutter.dev/docs/cookbook/persistence/key-value)食谱，了解更多关于其他方法的信息。

上述项目的回购可以在[这里](https://github.com/bharat-1809/data_persistence_flutter)找到。您还可以查看`database_handler.dart`文件中的`DatabaseHandler`类，看看如何用 SQLite 持久化数据。

就这样，伙计们！感谢阅读。你可以在推特上找到我。
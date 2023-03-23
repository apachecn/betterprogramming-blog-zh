# 一个简单的带 Flutter 的笔记应用程序—第 2 部分

> 原文：<https://betterprogramming.pub/simple-notes-app-with-flutter-part-2-5c982b8473bc>

## 完成我们的 Flutter 应用程序数据库结构。

![](img/66639c74dc8600c893555c78876218da.png)

欢迎回来！

让我们开始我们的应用程序。到目前为止，我们已经取得了良好的进展。这是第二部分，请看这里的[第一部分](https://medium.com/better-programming/simple-notes-app-with-flutter-part-1-ab66e15c8635)。

在这一块，我将尝试完成整个数据库结构。让我们开始吧。

# **统计所有行**

这个方法将帮助您获得所有行的计数。

这是什么意思？假设你从你的应用程序中输入了两个音符，它将返回两个整数，如果什么都没有，则返回零。有道理？

这些方法有时会在编码中帮助你，例如，如果你想显示一个类似“没有注释要显示”的文本，你可以使用这个方法。如果它将返回零，这意味着什么都没有，所以你可以显示文本，反之亦然。

```
Future<int> getCount() **async** {
  **var** dbClient = **await** db;
  int count = Sqflite.*firstIntValue*(**await** dbClient
      .rawQuery("SELECT COUNT(*) FROM ${Constants.*TABLE_NAME*}"));
  **return** count;
}
```

# 从数据库中获取单个项目或单个注释

我们将通过其`id` 检索单个音符，借助于将在数据库中迭代的`whereclause`，找到该特定`id`的音符`Map`，并将其返回给我们。

查看我们方法的返回类型。

```
Future<Note> getSingleItem(int id) **async** {
  **var** dbClient = **await** db;
  **var** result = **await** dbClient.rawQuery(
      "SELECT * FROM ${Constants.*TABLE_NAME*} WHERE ${Constants.*COLUM_ID*} = $id");
  **if** (result == **null**) **return null**;
  **return new** Note.fromMap(result.first);
}
```

现在，让我在这里定义我们之前创建的`Note.fromMap()`构造函数。

```
Note.fromMap(Map<String, **dynamic**> map) {
  **this**._text = map['name'];
  **this**._id = map['id'];
  **this**._date = map['date'];
}
```

如您所知，我们的数据库已经将所有内容存储在了`Map`对象中。

当它遍历并找到我们的数据时，显然该数据必须在`Map`对象中，而该对象对我们来说是无用的，因为我们无法读取它。

因此，我们必须将值从`Map`中提取到可读的格式，`Note.fromMap()`将为我们完成这项工作。

正如你所看到的，它将`Map<String,dynamic>`作为一个参数，由此，映射值将存储在类`note.dart` *的实例变量中。这样，我们将有一个返回给我们的对象。*

# **从数据库中删除特定项目或便笺**

从 db 中删除值类似于从 db 中获取单个项目，这一点我们刚刚讨论过。

正如您所看到的，这里有一点变化，因为我们使用了 sqflite 的`delete()`方法，该方法将表名、子句作为 ID，并将 where 参数。

```
Future<int> deleteItem(int id) **async** {
  **var** dbClient = **await** db;
  int count = **await** dbClient.delete(Constants.*TABLE_NAME*,
      where: "${Constants.*COLUM_ID*} = ?", whereArgs: [id]);
  **return** count;
}
```

# **更新注释**

为了更新注释，我传递了一个单注释对象作为 update 方法的参数，因为在注释对象中，我们有文本、日期和 ID。因此，当我们想要更新一个注释时，我们可能需要更改文本，因此，日期也会相应地更改。

`Id`将用于从数据库中获取特定的注释来更新它。

```
Future<int> updateItem(Note note) **async** {
  **var** dbClient = **await** db;
  int count = **await** dbClient.update(Constants.*TABLE_NAME*, note.toMap(),
      where: "${Constants.*COLUM_ID*} = ?", whereArgs: [note.id]);

  **return** count;
}
```

我希望这对你有用。我们现在已经完成了一个完整的数据库结构。我不会将 UI 部分集成到本文中。我将在下一篇文章中重点讨论这个问题。感谢阅读。
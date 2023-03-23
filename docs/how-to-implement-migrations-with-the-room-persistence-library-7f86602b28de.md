# 如何用 Room 持久性库实现迁移

> 原文：<https://betterprogramming.pub/how-to-implement-migrations-with-the-room-persistence-library-7f86602b28de>

## 更好的移动数据库迁移

![](img/4e0858cbfc2fbef2a3f9b4158caf32c2.png)

林赛·亨伍德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

在移动应用程序中使用本地数据库执行迁移总是让我觉得自己在拆除一个炸弹。但是[房间库](https://developer.android.com/topic/libraries/architecture/room)使得处理数据库迁移变得更加简单。

本地数据库是根据自己的业务逻辑设计的，遇到必须处理不同类型迁移的情况只是时间问题。

与其寻找解决方案，不如尝试了解房间数据库中的迁移是如何工作的，这样您就可以在正确的位置打开正确的开关。

使用 Room，如果您更改数据库模式或添加新表，并且不升级数据库版本，您的应用程序将会崩溃。如果您已经升级了版本，但没有提供任何迁移规则，您的应用程序将会崩溃。

如果正确的迁移规则不合适，数据库表将会丢失，用户将会丢失他们的数据。

我知道，这需要处理很多事情，但是一旦你了解了迁移是如何工作的，我相信你不会有同样的感觉。那么，事不宜迟，我们开始吧。

# 历史

房间是 SQLite 数据库的抽象层，这意味着房间必须在幕后处理 SQLite。

因此，如果您知道 SQLite 迁移是如何工作的，您就会理解为什么您需要对房间数据库做某些事情，比如升级版本、迁移规则等等，这些您将在本文的下一节中看到。

SQLite 数据库处理数据库版本的模式变化。更具体地说，每次添加、删除或更改数据库中的任何表或模式时，都必须增加数据库版本。

然后，当你打开应用程序时，SQLite 首先处理版本迁移，只有在这之后，它才会打开数据库。

# 我们必须在房间里处理的事情

让我们考虑一个简单的例子。我们有一个数据库，其中有一个名为`Movies`的表，表中有三个列 id(主键)、名称和封面图片，数据类型分别为`Int`、`String`和`String`。

现在我们有了一个新需求，要在`Movies`表中添加一个`rating`列，所以我们在`Movies`数据类中添加了一个新变量，如下所示。

# 案例一。版本号未升级

现在，您已经完成了数据库中的更改并运行了应用程序，当您尝试访问 Room 时，将会看到以下异常。

```
java.lang.**IllegalStateException**: Room cannot verify the data integrity. Looks like you've changed schema but forgot to update the version number. You can simply fix this by increasing the version number.
```

这里，Room 试图通过比较当前版本的身份散列和保存在`room_master_table`中的身份散列来检查数据库的身份。但是，没有身份哈希，应用程序会因`IllegalStateException`而崩溃。

现在你知道了第一次崩溃的原因，让我们把它放在一个检查表中，以便在应用程序的每个版本中进行验证。

我们可以通过升级应用程序数据库类或用`RoomDatabase()`扩展的类中的数据库版本来解决这个问题，如下所示。

```
@Database(entities = arrayOf(DashboardCircularCategory::class,
                             **version = 2**)
@TypeConverters(Converters::class)
abstract class ApplicationDatabase : **RoomDatabase**()  {
```

# 案例二。未提供任何迁移

现在我们已经升级了数据库版本，您尝试访问数据库，应用程序将崩溃，并出现以下异常。

```
java.lang.**IllegalStateException**: A migration from 1 to 2 was required but not found. Please provide the necessary Migration path via RoomDatabase.Builder.addMigration(Migration ...) or allow for destructive migrations via one of the RoomDatabase.Builder.fallbackToDestructiveMigration* methods.
```

房间有一个名为`Migration`的类，它处理模式中的任何变化；它负责触发 SQLite 升级。因此，如果模式中有任何更改并且没有提供迁移，应用程序将会崩溃。

# 案例三。破坏性迁移

例外本身是显而易见的，我们需要在房间数据库构建器中实现迁移或使用`fallbackToDestructiveMigration`。为了简单起见，我们使用如下图所示的`fallbackToDestructiveMigration`。

```
**Room**.databaseBuilder(context.getApplicationContext(),
        ApplicationDatabase::class.java, "applicationDatabase.db")
        .**fallbackToDestructiveMigration**()
        .build()
```

现在，当您尝试访问房间数据库时，由于您已经升级了版本，房间将检查迁移。它找不到任何表，所以它将退回到销毁模式，删除数据库中的所有表，然后插入一个身份散列。

因此，应用程序不会崩溃，但用户仍然会丢失所有数据。

# 带有模式更改的迁移

假设我们有这样一种情况，我们需要保留数据并插入新的模式更改。这就是优秀的迁移概念出现的地方。

让我们一步一步地看看我们需要做的事情。

1.  将数据库版本增加到 3。

```
@Database(entities = arrayOf(DashboardCircularCategory::class,
                             **version = 2**)
@TypeConverters(Converters::class)
abstract class ApplicationDatabase : **RoomDatabase**() 
```

2.创建从版本 2 到版本 3 的迁移，包括版本 2 发布到生产环境后所做的所有更改。

3.如下所示，将迁移添加到房间数据库构建器中。

现在，当你试图访问房间数据库时:

1.  它会改变表格中的变化。
2.  它更新了`identity_hash`。
3.  然后，尝试打开数据库。因为当前版本和保存的版本的身份 hash 是一样的，不会有什么问题。

# 奖金

要了解有关房间数据库的更多信息，请阅读以下文章。

*   [Android 房间持久化库，带有 Rxjava2 和鲜为人知的房间数据库特性](https://medium.com/mindorks/android-room-persistence-library-with-rxjava2-and-lesser-known-features-of-room-database-8b968261f9f0)
*   [Android Room 持久性库— Kotlin 协同程序](https://medium.com/@sgkantamani/android-room-persistence-library-kotlin-coroutines-26d53a09d384)

感谢您的阅读。
# 使用 Room Persistence 库将数据保存在本地数据库中

> 原文：<https://betterprogramming.pub/save-data-in-your-local-database-using-the-room-persistence-library-a9630c977234>

## 直接使用 SQLite API 的更好的替代方法

![](img/d432f217f2955a92768c87e8bf8d737a.png)

图片由 [Android 开发者](https://developer.android.com/training/data-storage/room)提供。

通过阅读本文，您将了解如何使用 [Room persistence 库](https://developer.android.com/topic/libraries/architecture/room)在 Android Studio 中实现自己的本地数据库。这个库允许您执行创建、读取、更新和删除操作，就像您在 SQLite API 中执行操作一样，而且更简洁。我强烈建议使用 Room 而不是 SQLite，原因如下:

*   Room 在 SQLite 上提供了一个抽象层，允许流畅的数据库访问，同时利用 SQLite 的全部功能。
*   该库可帮助您在运行应用程序的设备上创建应用程序数据的缓存。这个缓存作为应用程序的唯一真实来源，允许用户查看应用程序中关键信息的一致副本，而不管用户是否有互联网连接。

房间中有三个主要组件:

*   `Database` —包含数据库容器，并作为应用程序持久关系数据底层连接的主要访问点。
*   `Entity` —表示数据库中的一个表。
*   `DAO` —包含用于访问数据库的方法。

继续下一节，开始安装必要的依赖项。

# 设置

确保您已经安装了 Android Studio 并设置了必要的 SDK。在这篇文章中，我将使用 Java。如果您打算使用 Kotlin，请查阅官方文档并进行相应的修改。在`build.gradle (Module: app)`文件中，将以下代码添加到依赖项中。注意，有两个`build.gradle`文件。第一个是`Project`的刻度，第二个是`Module`的刻度。

```
dependencies {
  def room_version = "2.2.5"

  implementation "androidx.room:room-runtime:$room_version"
  annotationProcessor "androidx.room:room-compiler:$room_version" // For Kotlin use kapt instead of annotationProcessor

  // optional - Kotlin Extensions and Coroutines support for Room
  implementation "androidx.room:room-ktx:$room_version"

  // optional - RxJava support for Room
  implementation "androidx.room:room-rxjava2:$room_version"

  // optional - Guava support for Room, including Optional and ListenableFuture
  implementation "androidx.room:room-guava:$room_version"

  // Test helpers
  testImplementation "androidx.room:room-testing:$room_version"
}
```

记得同步 gradle，以确保依赖关系已注册。转到下一节，开始创建必要的文件。

# 履行

## 用户

首先，让我们创建一个名为`User.java`的新 Java 文件。添加以下进口声明。`Date`将用于在每次插入时生成时间戳。

```
**import** androidx.room.ColumnInfo;
**import** androidx.room.Entity;
**import** androidx.room.PrimaryKey;**import** java.text.SimpleDateFormat;
**import** java.util.Date;
```

我们将创建一个非常简单的表，其中包含以下数据:

*   `UID` —数据的唯一 id。用作主键，并在每次插入时自动递增。
*   `Name` —字符串列。你可以把它设置成你喜欢的任何东西。
*   `Timestamp` —插入期间的完整时间戳。

在类内部，添加以下构造函数。构造函数是可选的，但是我创建它们是为了以后更容易实例化`User`对象。即使您不打算使用默认值，也需要一个空的构造函数。

添加以下变量:

```
@PrimaryKey(autoGenerate = **true**)
**public int uid**;

@ColumnInfo(name = **"timestamp"**)
**public long timestamp**;

@ColumnInfo(name = **"name"**)
**public String name**;
```

*   `PrimaryKey` —这个装饰器将当前变量设置为表格的主键。
*   `autoGenerate` —确定该值是否会在每次插入数据时自动递增。
*   `ColumnInfo` —将列的名称设置为另一个名称。默认情况下，它将使用变量名作为列名。

我添加了另外两个可选函数，用于调试目的:

`User.java`的完整代码如后的[所示:](https://gist.github.com/wfng92/1d27eefd3acaf6eeef9dc46226a04574)

## 用户岛

创建另一个名为`UserDao.java`的文件。这包含访问数据库的用户访问对象。让我们从进口申报开始:

```
**import** java.util.List;

**import** androidx.room.Dao;
**import** androidx.room.Delete;
**import** androidx.room.Insert;
**import** androidx.room.Query;
```

将类更改为接口，并添加以下代码。我创建了更多的函数来:

*   从表中获取所有数据。
*   根据 UID 从表中获取所有行。
*   将数据行插入表格。
*   从表中删除一行。
*   清除表格中的所有数据。

*   `Query` —这个装饰器充当您打算在执行函数时运行的 SQL 查询。您可以在其中进行任何读/写查询，包括更新、插入和删除。如果您打算为条件语句传递参数，则必须在查询中在它前面添加一个冒号。
*   `Delete` —一个用于从表中删除数据的装饰器。
*   `Insert` —用于将数据插入表格的装饰器。

`UserDao.java`的完整代码如后的[所示:](https://gist.github.com/wfng92/c1ee7f09bf8f37ab9f773a210040ad74)

## 应用数据库

您需要创建一个数据库实例。建议使用单例模式，因为这个数据库实例是资源密集型的。创建一次，然后在整个应用程序中共享。创建一个名为`AppDatabase.java`的新文件，并添加以下导入声明:

使其成为扩展`RoomDatabase`的抽象类:

```
@Database(entities = {User.**class**}, version = 1)
**public abstract class** AppDatabase **extends** RoomDatabase {

}
```

创建一个变量作为实例，并用您喜欢的线程数初始化`ExecutorService`:

```
**private static volatile** AppDatabase *INSTANCE*;
**private static final int *NUMBER_OF_THREADS*** = 1;
**static final** ExecutorService ***databaseWriteExecutor*** =
        Executors.*newFixedThreadPool*(***NUMBER_OF_THREADS***);
```

创建一个返回实例的静态函数。在函数内部，添加`databaseBuilder`语句来初始化数据库。我将使用`user_database`作为数据库的名称。请随意使用您喜欢的任何名称:

`AppDatabase.java`的完整代码如后的[所示:](https://gist.github.com/wfng92/f43e62639d22570866c66a60d8d7448c)

## 主要活动

在`MainActivity`类中，获取`AppDatabase`的单例实例，并调用适当的函数。请注意，您必须在主线程之外完成它——类似于您如何使用`Runnable`更新 UI。在这种情况下，我将使用`AsyncTask`助手。以下示例从表中选择所有数据，并将其存储在用户列表中:

让我们再看一个如何插入数据的例子:

只需将所有更新 UI 的代码放入`run`函数中，就万事大吉了。

在最新的更新中，Android 中的文档建议使用`java.util.concurrent`来代替，因为`AsyncTask`在 API 级别 r 中已被否决。

![](img/ca6b445d1ec5564f166038ccea861a17.png)

图片取自[安卓](https://developer.android.com/reference/android/os/AsyncTask)

记得我们上次其实已经声明了一个叫做`databaseWriteExecutor`的`ExecutorService`。我们可以用它来代替`AsyncTask`。

```
private static final int *NUMBER_OF_THREADS* = 1;
static final ExecutorService *databaseWriteExecutor* =
        Executors.*newFixedThreadPool*(*NUMBER_OF_THREADS*);
```

只需调用执行函数并传递一个可运行的对象

```
AppDatabase.*databaseWriteExecutor*.execute(new Runnable() {
    @Override
    public void run() {
        List<User> users = AppDatabase.*getDatabase*(getApplicationContext()).userDao().getAll();
        for(User user : users) {
            Log.*d*("User", u.getDebugString());
        }
    }
});
```

# 结论

让我们回顾一下今天所学的内容。我们首先简要介绍了房间持久性库。

之后，我们在`build.gradle(Module: app)`文件的依赖项中编写了必要的代码。

一旦 gradle 被同步，我们就开始实现三个类。`User`类用于定义表格的结构。`UserDao`包含与数据库交互的必要方法。`AppDatabase`保存将在应用程序的其他部分调用的单例实例。

最后，我们通过将函数包装在`AsyncTask`的`Runnable`函数中来执行`MainActivity`中的代码。

感谢阅读这篇文章。希望下一部能再见到你！
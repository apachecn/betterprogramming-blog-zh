# Android 开发中的 SQLDelight 入门

> 原文：<https://betterprogramming.pub/getting-started-with-sqldelight-in-android-development-eecd0ae9bbdd>

## 启动应用程序的数据库

![](img/b3d217a3c8a18a6a782cfac6aa8f4c0e.png)

[博伊图梅洛·菲特拉](https://unsplash.com/@writecodenow?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 介绍

SQLDelight 是一个跨平台的数据库库。这与传统的注释过程和反射生成代码的方法完全相反。

它的独特之处，甚至超过了它支持跨平台的事实，是它采用 SQL 代码并将其转换为 Kotlin/Java/Native 代码，支持不同的平台，如 Android、iOS、web。

对于传统的特定于平台的库，如 Room database，它们使创建数据库表和操作变得容易，因为您是用同一种语言编写代码的。

但是说到 SQLDelight，你需要能够自己编写 SQL 查询。在我看来，这既是图书馆的优势，也是它的弱点。作为一个 Android 开发者，我不喜欢写 Kotlin/Java 之外的代码，但另一方面，这也给了我支持多平台的机会。

坦率地说，当你开发一个移动应用程序时，你不需要太多关于 SQL、基本编码和语法知识的深入知识来使它工作。

所以，没有任何延迟，让我们开始吧:

# 综合

在项目级 Gradle 文件中添加 SQLDelight 插件，如下所示:

第二步，我们需要在 app 级 Gradle 文件上应用 SQLDelight 插件来支持代码生成。SQL delight 不会基于像`kapt`这样的注释处理器来生成代码。

生成特定于平台的代码的过程是可行的，因为最终您将执行 SQL 查询。与特定于平台的库(如房间数据库)相比，这在某种程度上是相同的行为。看看下面的代码:

```
plugins **{** id 'com.android.application'
    id 'kotlin-android'
    **id 'com.squareup.sqldelight'**
**}**
```

到目前为止，我们已经集成了通用 SQL Delight 支持，现在我们需要添加 Android 支持库。`AndroidSqliteDriver`是我们这里需要的。我们需要在应用程序级梯度文件的依赖关系节点下添加以下行:

```
implementation "com.squareup.sqldelight:android-driver:1.3.0"
```

# 编写 SQL 代码

作为第一个创建数据库的人，我们应该编写 SQL 代码。问题是应该在哪里定位(*。sq)文件。我们需要在主目录下创建一个名为`sqldelight`的单独目录，类似于 java 和 Kotlin，并托管我们所有的(*。sq)文件。

```
src/main/sqldelight
```

现在，没有任何进一步的延迟，让我们创建我们的 MovieItem.sql 文件。在文件中添加以下代码:

添加上述代码后，将会向您显示一个安装 SQLDelight Android Studio 插件的建议。这不是强制性的，但是可以使理解语法变得容易。我建议安装插件。

# Android 代码

正如我前面说过的，我们需要使用`AndroidSqliteDriver`将数据写入 android 数据库，该数据库在应用程序启动时保持不变。首先，创建如下所示的`AndroidSqliteDriver`实例:

```
val androidSqlDriver = AndroidSqliteDriver(
    schema = Database.Schema,
    context = applicationContext,
    name = "movies.db"
)
```

然后我们需要获得我们在 movieitem.sql 文件中创建的查询。看一下代码:

```
val queries = Database(androidSqlDriver).movieItemQueries
```

然后，我们可以直接执行我们在 movieItem SQL 文件中创建的所有功能。看一下代码:

```
val movies: List = queries.selectAll().executeAsList()
Log.d("MovieDatabase", "Movies : $movies")
```

# 协程**支持**

jetpack Room 数据库库成功的主要原因之一是它易于使用，并且与流行的框架如协程和分页兼容**。**

SQL Delight 也有这样的好处；我们只需要在应用程序级 Gradle 文件中的 dependencies 节点下添加下面一行代码就可以让它工作了。

```
implementation "com.squareup.sqldelight:coroutines-extensions-jvm:1.5.0"
```

现在，它就像对 Room library 使用协程一样简单。代码如下:

```
val players: Flow<List<MoveItem>> = 
  queries.selectAll()
    .asFlow()
    .mapToList()
```

如果您还在使用 RxJava，那么您可以添加下面一行来将 Rxjava 支持集成到 SQL Delight 中:

```
implementation "com.squareup.sqldelight:rxjava3-extensions:1.5.0"
```

现在，为了观察查询，我们可以使用 RxJava 扩展构件，如下所示:

```
val players: Flow<List<MoveItem>> = 
  queries.selectAll()
    .asObservable()
    .mapToList()
```

# 想要更多吗？

我最近看到了由 [Satya Pavan Kantamani](https://medium.com/u/d255c4fc9d76?source=post_page-----4c9f44d04e68--------------------------------) 发表的这篇关于 Jetpack Compose 的优秀文章，强烈推荐:

[](/jetpack-compose-androids-modern-toolkit-for-building-ui-82e67d384c26) [## Jetpack Compose 简介:Android 的未来 UI 工具包

### 告别 XML 布局，采用现代技术

better 编程. pub](/jetpack-compose-androids-modern-toolkit-for-building-ui-82e67d384c26) 

目前就这些。希望你学到了有用的东西。

感谢阅读。
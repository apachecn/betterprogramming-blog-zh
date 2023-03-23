# 查看 GomorraSQL——一个用那不勒斯语编写查询的库

> 原文：<https://betterprogramming.pub/check-out-gomorrasql-a-library-to-write-queries-in-neapolitan-3e85568dddb4>

## 像那不勒斯人一样用 GomorraSQL 查询数据库

![](img/4dc6c580d5404b55bb0205471f30b6a5.png)

乔纳森·比恩在 [Unsplash](https://unsplash.com/s/photos/italy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

在过去的几天里， [GomorraSQL](https://github.com/aurasphere/gomorra-sql) 在意大利 IT 社区掀起了波澜，成为了潮流。我已经在 Twitter 和 LinkedIn 上看到了数千篇提到这个项目的帖子，所以我忍不住去研究一下。GomorraSQL 由[多纳托·里门蒂](https://github.com/aurasphere)创造，灵感来自意大利电视连续剧[*Gomorrah*](https://en.wikipedia.org/wiki/Gomorrah_(TV_series))**允许你在[那不勒斯](https://en.wikipedia.org/wiki/Neapolitan_language)查询数据库。**

**尽管这类项目不应该太认真，但它们在 IT 社区中确实扮演着重要的角色。让我们找出原因。**

# **什么是 GomorraSQL？**

**![](img/77b95f922635fdb0fc1e28714138b70a.png)**

**来自 [GitHub](https://github.com/aurasphere/gomorra-sql) 的 GomorraSQL 的 logo。**

**首先，让我们试着理解一下 GomorraSQL 是什么:**

> **“GomorraSQL 是一种简单明了的 SQL 方言，允许您用那不勒斯语编写更简单、更容易理解的查询。”— [GomorraSQL 的 GitHub 页面](https://github.com/aurasphere/gomorra-sql)**

**正如官方文档中所述，GomorraSQL 既可以用作 Java 库，也可以用作独立的 SQL 数据库客户端。在撰写本文时，该项目官方仅支持 [MySQL](https://www.mysql.com/) 和 [H2](https://www.h2database.com/html/main.html) ，但其他 [RDBMS](https://en.wikipedia.org/wiki/Relational_database#RDBMS) 技术可能会在未来添加到列表中。**

**[](https://github.com/aurasphere/gomorra-sql) [## 光环/蛾摩拉-sql

### GomorraSQL 是一种简单明了的 SQL 方言，允许您编写更简单、更…

github.com](https://github.com/aurasphere/gomorra-sql)** 

# **如何使用 GomorraSQL**

**如前所述，GomorraSQL 有两种用法。让我们两个都看看。**

## **1.Java 库**

**首先从 GitHub 下载最新的 *GomorraSQL.jar* [。然后，将其导入到您的项目中。您还需要一个数据库驱动程序。现在，您可以开始使用 GomorraSQL，采用由`GomorraSqlInterpreter`类公开的方法，这需要实例化一个有效的](https://github.com/aurasphere/gomorra-sql/releases)`[Connection](https://docs.oracle.com/en/java/javase/14/docs/api/java.sql/java/sql/Connection.html)`对象。**

**`GomorraSqlInterpreter`允许您对数据库执行 GomorraSQL 查询，或者将用那不勒斯语编写的查询翻译成普通的 SQL。请注意，每当出现错误时，都会抛出一个提供有用调试信息的`CaggiaFaException`(字面意思为*whatsouldoidexception*)。**

## **2.数据库客户端**

**首先从 GitHub 下载最新的 *GomorraSQL.jar* [。然后使用以下命令启动它:](https://github.com/aurasphere/gomorra-sql/releases)**

```
java -cp gomorra-sql-1.0.0.jar;<path_to_your_db_driver> co.aurasphere.gomorrasql.GomorraSqlShell
```

**客户端将要求一个代表要连接的数据库的 JDBC 字符串(包括凭证)。一旦建立了连接，就可以开始执行 GomorraSQL 命令了。**

# **GomorraSQL 在行动**

**在看到 GomorraSQL 运行之前，您应该先看看这些规则:**

*   **括号在 GomorraSQL 查询中是无效字符。**
*   **GomorraSQL 不允许多行查询。**
*   **使用运算符时需要空格(例如，包含`a = 5`的查询可以工作，但包含`a= 5`或`a =5`或`a=5`的查询则不行)。**

**虽然我本人不是那不勒斯人，但我发现在 GomorraSQL 中编写查询是一种有趣而有益的体验。另外，如果你忘记了 GomorraSQL 要求的一些单词或表达，你可以随时查看下表:**

**GomorraSQL 语言参考表**

**现在，我们来看看一些 GomorraSQL 查询与 SQL 查询的对比:**

## **GomorraSQL**

```
1) retrieving all users' data
ripigliammo tutto chillo ch'era 'o nuostro mmiez 'a users2) retrieving all the emails of the users with id 6 or null name
ripigliammo email mmiez 'a users arò id = 6 o name è nisciun3) deleting all users' data 
facimm na' strage mmiez 'a users4) setting the name "Ciro" for all the users
rifacimm users accunza name accussì "Ciro"5) inserting a new user by setting only their name
nzipp 'ngoppa users name chist "Ciro"
```

## **结构化查询语言**

```
1) retrieving all users' data
SELECT * FROM users2) retrieving all the emails of the users with id 6 or null name
SELECT * FROM users WHERE id = 6 OR name IS NULL3) deleting all users' data 
DELETE FROM users4) setting the name "Ciro" for all the users
UPDATE users SET name = "Ciro"5) inserting a new user by setting only their name
INSERT INTO users(name) VALUES ("Ciro")
```

**瞧啊！你现在可以像*蛾摩拉*上的角色一样开始查询你的数据库了！**

# **为什么这样的项目很重要**

**虽然您不应该期望在现实世界的应用程序中使用这样的库，但是这些项目比它们看起来更重要。首先，它们容易像病毒一样传播。事实上，任何人都愿意重新分享它们——不仅仅是开发者。因此，与其他任何 IT 项目相比，他们可以接触到更多的人。其次，它们是为了乐趣而创建和维护的，证明了编码的娱乐性。**

**这两点都表明了这种基于乐趣的项目是如何帮助好奇的人们对 IT 社区产生兴趣并参与其中的。它们可能不会彻底改变世界，或者是令人难以置信的前瞻性技术，但它们确实在向发展世界介绍新人方面发挥了作用。**

**这就是为什么支持他们可能比预期的更有意义。**

# **结论**

**在本文中，我们研究了 GomorraSQL，这是一种解释的 SQL 方言，允许您用那不勒斯语编写查询。虽然这不能太认真，但类似的项目在帮助好奇的人接近和进入编码世界方面发挥了重要作用。这使得这些项目比看起来更有意义——尤其是考虑到它们的最终用途。**

**感谢阅读！我希望这篇文章对你有所帮助。如果有任何问题、意见或建议，请随时联系我。**
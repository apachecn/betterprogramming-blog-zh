# 在 Laravel 中使用数据库事务

> 原文：<https://betterprogramming.pub/using-database-transactions-in-laravel-8b62cd2f06a5>

## 数据库事务帮助我们永远不会出现记录被半插入数据库的情况。了解如何使用它们

![](img/78f4bc029030ee0fdda213038b5d30f6.png)

[un splash 上的活动创建者](https://unsplash.com/photos/IKHvOlZFCOg)

在这篇短文中，我们将介绍什么是数据库事务，为什么您想要使用它们，以及我们如何在 Laravel 中利用数据库事务。事不宜迟，让我们直入主题吧！

# 问题是

我正在开发一个类，它允许我从多个(但相关的 CSV 文件)导入数据，并将这些数据组合起来，在数据库上创建一堆相互关联的模型。

在解析大约五个不同的 CSV 文件时，我必须为不同 CSV 文件中的每一行创建大约九个不同的相互依赖的模型集。

我注意到，在测试时，不可避免地会有导入中途失败的时候，这可能是因为诸如打字错误之类的验证错误、诸如重复的唯一键约束之类的数据库错误或者其他类似的问题。当出现这种错误时，我会让数据库中的记录占一半。

这在我的本地机器上不是问题，因为我可以只做一个`php artisan migrate:fresh`但是如果它在服务器上失败了(甚至在临时服务器上)，那将是一个严重的灾难。想象一下，如果 CSV 文件有超过 1000 行，并且代码在第 589 行中途失败，我们如何手动开始回滚记录？

我需要一种方法使解析类[原子化](https://www.techopedia.com/definition/3466/atomic-operation)——它要么完全失败，要么完全通过(零或一的情况)，没有中间或中途通过。

# 我的强力解决方案

我回到绘图板，我的第一个想法是创建一个单独的表，`track_parses`来跟踪所有的 csv 导入尝试。每一行代表一个解析会话，它将存储一个 JSON 序列化对象，该对象将跟踪我为每个解析会话成功创建的所有记录。

使用这种方法，我将在每个解析会话开始时创建一个`track_parses`对象，并在创建任何记录时更新该对象。我将把整个代码包装在一个 try-catch 块中，如果代码中途失败，我可以遍历对象并删除我在函数崩溃前创建的所有记录。
当函数成功时，我将对象序列化到 JSON，并将其持久化到`track_parses`表中，我可以拥有每个解析会话创建的所有记录的记录，并且如果我愿意，仍然可以回滚任何解析会话。这将给我一个原子解析算法。

在我遇到数据库事务之前，我正要实现它。

# 拯救交易！！！

首先让我们谈一谈什么是数据库事务。数据库事务是您对数据库进行的一组操作或查询，它将这些操作或查询组合在一起，并将其视为一个工作容器。这意味着您可以发送一堆查询，数据库会将所有这些查询视为一个动作。

然后您可以`**rollback**` 这个动作——这将撤销该容器中的所有查询。您还可以`**commit**` 执行此操作，这实际上会将所有这些操作保存到存储中。我把事务理解为在内存中存储你所有的更改，然后当你提交更改时实际上把它们写到一个磁盘上，或者当你回滚更改时把它们从内存中删除。

数据库事务在大多数 SQL 数据库中都是可用的，尽管在健壮程度、实现方式上有所不同。然而，大家伙是 MySQL、PostgreSQL、SQLite、Oracle 和 SQL Server 支持事务，所以用您最喜欢的 SQL 数据库实现它们应该没有任何问题。

有了这个解释，您一定已经看到了事务是如何帮助我节省大量代码的，我所要做的就是将我的整个解析逻辑放入一个数据库事务中，将所有内容包装在一个 try-catch 块中，然后在代码工作时提交事务，或者在 catch 块中回滚事务。

虽然这不允许为每个解析会话保留一个添加记录的记录，也不允许我在成功导入后回滚，但这是一个值得的权衡，因为它们不是必需的特性，它节省了我处理其他特性的时间。另外，请注意，如果需要的话，事务不会阻止我保留记录——这不是一种非此即彼的情况，除了数据库事务之外，我还可以添加`track_parses`对象。

# 拉勒韦尔的交易

在 Laravel 中使用数据库事务非常容易。它只需要几行代码就可以使用。使用事务有两种方式:

## 关闭

Laravel 在`DB` facade 上提供了一个闭包，您可以将整个代码封装在其中。代码如下所示:

正如您在这里看到的，我们可以将整个代码包装在事务方法的闭包中，Laravel 会自动为我们处理事务。

这种方法适用于像这样的简单查询，但是我个人不喜欢将这种方法用于更复杂的代码，原因很简单——闭包有不同的作用域。这意味着我必须将闭包需要的依赖项与`use()`构造联系起来，随着依赖项的增加，这需要做大量的工作。

## 事务方法— beginTransaction()，commit()，rollBack()

Laravel 在`DB` facade 上提供了三个静态方法，允许我们完全控制如何处理事务。这种方法提供了更大的灵活性，并允许我们准确地定义何时应该提交或回滚事务。

下面是我们如何通过同一个例子来使用这些方法:

正如您在这里看到的，我们可以更好地控制何时回滚。我们也不必为我们使用的所有变量使用任何`use()`构造。

*注意——请注意这是显示数据库事务的一个非常基本的片段，在一个真正的应用程序中，我们会更加谨慎地做事情，比如验证和净化输入，确保我们的* `*$fillable*` *数组中没有不必要的字段，等等。*

# MySQL 用户注意

如果您正在使用 MySQL，并且尝试了这段代码，但没有成功，这可能是因为您的数据库正在使用 MyISAM 引擎。MySQL 基本上有两个引擎用于管理它的表，MyISAM 和 InnoDB。InnoDB 是现代的，比 MyISAM 更快，并且支持事务和外键。总的来说，这是比 MyISAM 更好的选择。你可以查看[这篇文章](https://hevodata.com/learn/myisam-vs-innodb/)对两款引擎进行简单的对比。

基于这一点，我认为 MySQL 数据库会使用 InnoDB 作为默认的表引擎，但不幸的是，情况并非总是如此。创建迁移时，您可以指定要用于迁移文件中的表的引擎。它看起来像这样:

您还可以通过在`config/database.php`文件中更新数据库配置文件中的引擎值来自动更改所有未来表的设置。完成此操作后，您的配置文件将如下所示:

如果您意识到您已经创建了您的数据库表，并且它们使用 MyISAM 引擎*(和我一样，您正在处理一个有超过 50 个表的数据库)*，您可以很容易地创建一个迁移，将数据库中的所有表自动转换到您喜欢的引擎。迁移将如下所示:

# 结论

我知道我在第一段说过这篇文章很短，老实说，当我开始写作的时候，我以为它会很短——对不起。不管怎样，交易很棒。它们是你不知道自己需要的食物的绝佳调味料之一。它们可以应用在许多场景中，以确保您的代码是原子的。
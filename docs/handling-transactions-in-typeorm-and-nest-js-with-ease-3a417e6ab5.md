# 轻松处理 TypeORM 和 Nest.js 中的事务

> 原文：<https://betterprogramming.pub/handling-transactions-in-typeorm-and-nest-js-with-ease-3a417e6ab5>

## 在 TypeORM 和 Nest.js 中处理数据库事务的一种方便而简单的方法

![](img/75c3c5b9ac688bf66fcb38be06980f73.png)

照片由 [panumas nikhomkhai](https://www.pexels.com/photo/close-up-photo-of-mining-rig-1148820/) 拍摄

在许多情况下，必须将几个数据块作为一个逻辑单元同时创建/更新(即创建一个实体没有意义，并且在不更新另一个实体的情况下不应该发生，因为它们相互依赖)。PostgreSQL 等数据库为我们提供了适合这种情况的工具:事务(这类数据库称为“事务数据库”)。

> 根据[维基百科](https://en.wikipedia.org/wiki/Database_transaction)的说法，事务本质上是被视为具有“全有或全无”效果的单个工作单元的多个操作。它开始进行一些数据库更改，然后要么提交，要么在任何失败的情况下回滚。通过这种方式，数据库要么具有应用了所有更改的新状态，要么返回到其原始状态，就像什么都没发生一样。

# 交易类型

TypeORM 支持数据库事务，它的[文档](https://typeorm.io/transactions)很好地解释了如何使用它们:

> 正如文档中所提到的，使用所提供的`EntityManager`实例是非常重要的，千万不要使用全局管理器，否则将会出现错误和/或不可预知的结果。

## 直接使用 TypeORM 的缺点

我们看到 TypeORM 使我们的生活变得更加容易，因为我们不必处理 SQL 事务本身，但是在业务代码中使用它仍然有一些缺点:

1.  如果我们遵循`DataSource` / `EntityManager`方法，我们必须将所有的数据库操作放入这个回调中，不管它们有多复杂，也不管有多少个回调。
2.  如果我们遵循`QueryRunner`方法，我们必须写很多样板代码，并确保我们没有遗漏任何东西。
3.  在这两种情况下，我们的代码都变得难以阅读、维护和重用。

那么我们还有什么其他选择呢(考虑到我们使用的是 Nest.js 框架)？

1.  使用 decorators 来控制事务(`@Transaction()`和`@TransactionManager()`)，这在 [Nest.js 文档](https://docs.nestjs.com/techniques/database#:~:text=Using%20decorators%20to%20control%20the%20transaction%20(%40Transaction()%20and%20%40TransactionManager())%20is%20not%20recommended.)中并不推荐。
2.  包括一些第三方库，如[type ORM-transactional-cls-hooked](https://github.com/odavid/typeorm-transactional-cls-hooked)(基于这个很酷的[“延续本地存储”](https://github.com/typeorm/typeorm/issues/1895)概念)或 [nest_transact](https://github.com/alphamikle/nest_transact) 。
3.  自己构建一些相对简单的东西来满足我们的需求，并抽象出使用 TypeORM 事务接口的工作。

你可能已经猜到了，我将在这篇文章中讨论第三种选择。

# 抽象事务类

我的朋友( [Anton Pavlov](https://medium.com/u/a3ba5a2f62f5?source=post_page-----3a417e6ab5--------------------------------) )和我开始思考如何在我们的 Nest.js 应用程序中隐藏事务的所有内部工作方式，使其远离业务逻辑。我们提出了一个抽象的 transaction 类，它处理所有的事务，并为我们提供了一种简单的方法来编写事务，并在代码库中的任何地方重用它们的代码:

如您所见，这个类包含了`QueryRunner`的样板代码，因此我们不必再处理它，而将所有与实际操作相关的事情留给这个类的后代。如果我们需要创建一个事务，我们从这个基类继承，然后实现`execute`函数，把我们所有的逻辑放在那里。

# 最简单的用法示例

现在让我们看看如何使用我们已经创建的这个事务类。请记住，这段代码只有一个目的——展示如何处理事务，我们并不关心某些实现细节(或适当的变量名)。

1.  首先我们创建从我们的`BaseTransaction`继承而来的`CreateUserTransaction`类，为事务的输入和输出提供类型化的参数。
2.  事务本身非常简单:我们只需创建一个`User`，然后使用在基类的幕后创建的管理器创建一个连接到我们的`User`的`Balance`实体。
3.  然后，在我们的`UserService`中，我们只是注入`CreateUserTransaction`并调用它的`run`方法。
4.  只有在`run`方法返回后，事务才会被提交。我们可以 100%肯定，我们的用户将只与平衡，而不是以任何其他方式创建。如果有任何错误，它将被捕获，事务将被回滚，这意味着数据库中没有任何更改。

# 更高级的例子

比方说，我们有两个场景:我们可以创建一个余额为空的简单用户，或者创建一个有奖金的高级用户。

此外，假设我们的项目中有某种抽象层，它与数据库一起工作并处理关于实体的所有细节。

为了简洁起见，我将只提供一个这种抽象实现的表面例子，其余的应该非常简单和相似。

在本例中:

1.  我们的`UserService`启动“大”主`CreateUserTransaction`来创建一个用户。
2.  在主事务内部，我们首先调用我们的`CreateBasicUserTransaction`的`runWithinTransaction`方法，传递由外部事务创建的管理器。这是非常重要的，因为这个经理将是把所有事情联系在一起的一部分。
3.  然后，我们调用我们的数据库抽象层类，如`DbUserService`、`DbBalanceService`，它们处理我们需要的一切(与实体相关)，无论是对 TypeORM 存储库的简单调用、几个这样的调用、一些获取和转换数据，还是您认为适合这一层的任何东西。再次强调，对每个数据库操作使用传递的`EntityManager`是至关重要的。
4.  在`CreateBasicUserTransaction`完成用户创建之后，我们的主事务继续检查用户是否是高级用户，如果需要的话做一些额外的工作，提交对数据库的更改(通过`BaseTransaction`类)，并为我们的`UserService`返回`CreatedUserData`。
5.  和前面的例子一样，如果在上面的任何步骤中出现任何错误，事务将被回滚，我们的数据库将不会受到任何“部分”更改的影响。

# 结论

所描述的使用 TypeORM 和 Nest.js 处理事务的方式允许我们将任何连接的业务相关的数据库逻辑分成可管理和可重用的部分，并且我们不必关心事务处理本身。

这种方法的唯一缺点是必须沿着与数据库交互的方法到处“拖动”事务性`EntityManager`。

我们找不到一个简单的方法来解决它，因为我们不想让我们的代码过于复杂，例如，通过加入前面提到的“延续本地存储”的概念，或者为管理人员发明一些智能容器。如果你想到了一个，请给这篇文章留下评论，我们会很高兴地看看你的任何建议。

## 文章的附加阅读和链接

1.  [维基百科中的交易](https://en.wikipedia.org/wiki/Database_transaction)
2.  [交易表格文件](https://typeorm.io/transactions)
3.  [Nest.js 关于使用数据库的文档](https://docs.nestjs.com/techniques/database)
4.  [利用延续本地存储概念构建的 Nest.js 事务库](https://github.com/odavid/typeorm-transactional-cls-hooked)
5.  G [reat 文章作者](https://hackernoon.com/the-most-convenient-ways-of-writing-transactions-within-the-nestjs-typeorm-stack-3q3q33jd) [Mikhail Alfa](https://medium.com/u/d364fcad7a5c?source=post_page-----3a417e6ab5--------------------------------) [用 TypeORM 和 Nest.js](https://hackernoon.com/the-most-convenient-ways-of-writing-transactions-within-the-nestjs-typeorm-stack-3q3q33jd) 和 [his 库](https://github.com/alphamikle/nest_transact)提供不同的处理事务的方法
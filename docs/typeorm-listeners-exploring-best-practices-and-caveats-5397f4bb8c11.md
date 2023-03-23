# 类型实体侦听器—探索最佳实践和注意事项

> 原文：<https://betterprogramming.pub/typeorm-listeners-exploring-best-practices-and-caveats-5397f4bb8c11>

## 用钩子监听 TypeORM 实体中的事件

![](img/8befd520952ff4746a6cc3b48e601841.png)

照片由[加布里埃尔·海因策](https://unsplash.com/@6heinz3r?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

TypeORM(以及许多其他 ORM)中的一个基本概念是[实体](https://typeorm.io/entities)。一个`Entity`代表你的数据模型，它是“一个映射到数据库表(或者使用 MongoDB 时的集合)的类”。

基本上，一切都围绕着它，我们以许多不同的方式与实体互动:我们创建、更新、编辑、删除它们。通常，在我们执行其中一个操作之后(或之前)，我们需要执行一些其他操作，添加一些自定义逻辑。

它可以是像记录日志或设置一些默认值这样简单的事情，也可以是像以某种方式更新其他实体这样的事情。我们可以在我们的类(存储库、服务等)中手工编写。)这可能有点乏味。为了帮助我们完成这项任务，TypeORM 为我们提供了一个非常有用的工具——实体监听器(或者我们可以称之为钩子)。

# TypeORM 中的实体侦听器

正如 TypeORM 文档所述，“实体侦听器是带有定制逻辑的方法，用于侦听特定的实体事件。”实现这些监听器最简单、最直接的方法是给在`Entity`上定义的方法添加一个特殊的装饰器。这里有一个例子:

有了这个装饰器，每次在我们创建一个新的`Author`之前，我们都会在上面生成一个电子邮件字段，以防没有提供

但是如果需要添加比这更复杂的东西呢？例如，添加一些其他数据库调用。当我们刚刚开始使用这些钩子时(之前没有使用 TypeORM 的经验)，我们只是像这样在钩子内部添加了这个逻辑(不要介意实体和属性名称，它们与本文并不相关):

显然，这是一种错误的方法。

我们注意到，只有当我们试图在事务中使用这个实体并不断得到错误时(TypeORM 文档告诉我们，我们不应该在侦听器中进行任何数据库调用，但是谁会读取文档呢，对吗？).

我最近写了一篇关于在 TypeORM 中使用事务的文章，其中最重要的一点是——当你在事务内部做一些事情时，你需要使用`Transactional Manager`。

正如您在上面的例子中看到的，那些侦听器不知道任何关于我们的事务及其事务管理器的事情，所以所有的`Insert`和`Update`操作都失败了。在这一点上，我们意识到我们做错了什么，并且发现了另一种当涉及到数据库时使用实体监听器的正确方法— `Subscribers`。

# 实体订户

> 订户是用@EventSubscriber() decorator 标记为事件订户的类，它可以侦听特定的实体事件或任何实体事件。使用`QueryBuilder`和存储库/管理器方法触发事件。

要创建一个`Subscriber`，您需要:

1.  用`@EventSubscriber`装饰器标记这个类。
2.  让您的类实现将您的`Entity`类作为其类型参数的`EntitySubscriberInterface<T>`。
3.  添加`listenTo()`方法，该方法应该简单地返回您的实体类，以指定该订阅者将只监听您的实体的事件。
4.  实现您需要的事件方法。
5.  在`ConnectionOptions`(或从 TypeORM v 0.3.0 开始的`DataSourceOptions`)选项中指定您的订户，如下所示:`subscribers: [ MyEntitySubscriber ]`。
6.  如果你需要实现一个订阅者来监听所有实体的事件，不要添加`listenTo()`方法，也不要在`EntitySubscriberInterface`中指定类型参数。

你可以在这里阅读关于订户和事件的信息(包括他们的完整列表)。所以，让我们修正我们的代码，看看这个例子:

如您所见，事件方法中的大部分代码保持不变，只有一个关键的区别——我们使用了`event`对象的`entity`和`manager`属性。`entity`将拥有包含我们数据的实体对象，而`manager`(或者`queryRunner`如果需要的话)为我们提供了一种与数据库交互的方式。

事件监听器中的所有数据库操作都是使用事件对象的`queryRunner`或`manager`实例来执行的，这一点非常重要。这消除了前面提到的事务问题，因为这里我们接收到了事务管理器的一个实例，并且我们没有超出事务的范围。

我希望这篇文章对您有所帮助，让您更好地理解如何使用 TypeORM 实体钩子(侦听器)。一如既往，我感谢您的反馈。编码快乐！

## 更多阅读

1.  [实体类型文件](https://typeorm.io/entities)
2.  [实体监听器和订阅者的类型文档](https://typeorm.io/listeners-and-subscribers)
3.  [如何处理类型表单中的事务](/handling-transactions-in-typeorm-and-nest-js-with-ease-3a417e6ab5)
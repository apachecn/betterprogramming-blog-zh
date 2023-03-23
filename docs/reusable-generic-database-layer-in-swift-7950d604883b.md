# 在 Swift 5 中创建可重复使用的通用数据库层

> 原文：<https://betterprogramming.pub/reusable-generic-database-layer-in-swift-7950d604883b>

## 学习使用 Mapper，神奇的课堂

![](img/4f8d85c656cd5b969e5f0d52e367afc3.png)

照片由[法比奥](https://unsplash.com/@fabioha?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

这篇文章的重点是创建一个可重用的通用数据库(DB)层，并解释如何执行一个特定的实现，在本例中是[核心数据](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreData/index.html)。

# 初步说明

一些黄金法则/建议:

1.  DB 层不应该暴露给视图层(视图控制器)。
2.  只有服务应该能够调用 DB 层来执行创建、读取、更新和删除(CRUD)操作。
3.  DB 层的内部工作决不能暴露给外层。
4.  DB 层必须以这样一种方式编写，即如果需要，可以在以后更改 DB 客户端，而不需要进行太多的更改，例如，如果您最初使用核心数据，然后想要使用[领域](https://realm.io/)来代替。
5.  数据库实体不应该暴露在外层。视图控制器或服务不应该知道实体/模型类，比如`NSManagedObject`子类。这将减少层的耦合，并且 DB 实现可以在以后容易地切换。

这篇文章将涵盖的内容:

1.  准备通用数据库层，
2.  创建 DB 层的特定实现，
3.  在第二步中为数据库实现提供映射器层。

# 第一步:准备一个通用数据库层

我们将从创建一个通用数据库层开始。

任何需要保存到数据库的实体都应该实现`Storable`协议。

StorageContext 由几乎任何数据库实施都需要的通用数据库操作组成

本教程的命名约定:

1.  DBEntity:实现`Storable`接口的实体。这个实体可以保存到数据库中。在我们的例子中，`NSManagedObject`子类是 DBEntities。例如，`StoryEntity`就是一个 DBEntity。
2.  DomainEntity:实现`Mappable`接口的实体。这个实体可以用在视图层和业务层。这无法保存到数据库。域名实体的一个例子是`Story`。

`Sorted`类需要给出一个因子来对结果进行排序。

最常见的数据库有具体的和内存中的实现。核心数据具有可用于单元测试的内存类型。`Enum` `ConfigurationType`支持这种需求。

# 第二步:创建 DB 层的具体实现

我将在这里使用核心数据为我在第一步中创建的 DB 层提供一个实现。核心数据有一些大多数情况下都需要的函数，所以让我们添加这些方法作为`StorageContext`的扩展。`CoreData`实体由`NSManagedObjectID`识别；当通过 ID 从数据库中获取现有对象时，我们将需要这个方法。您还可以根据需要添加其他类似的方法。

`CoreDataStorageContext`是`StorageContext`的实现。这是我前面讲的具体实现。

是的，`CoreDataStorageContext`实现了`StorageContext`协议中所有需要的方法。这里需要注意的是，所有的方法都需要`Storable`类型的实体。

`CoreDataStoreCoordinator`是负责数据库初始化和设置所有先决条件的类。我在文章中包含了这个类，这样您就可以理解 DB 层的端到端实现。

所有核心数据实体都继承`NSManagedObject`，默认情况下`NSManagedObject`不实现`Storable`协议。要将`NSManagedObject`标记为可存储，我们需要使其符合`Storable`。

这是一个样本`StoryEntity`的样子。记住`StoryEntity`是一个 DBEntity，也就是说，它可以持久化到 DB 中。

# 步骤三:为数据库实现准备一个映射层

正如我在文章开始时提到的，领域实体和数据库实体应该是不同的。视图控制器和业务(服务)层不应该知道数据库实体；视图控制器应该不知道什么是`NSManagedObject`。所以这一步就是专门为此服务的。如果您不想创建模型类的不同版本，您可能不需要这一步。

我们之前已经看到，我们所有的 DBEntities 都应该实现`Storable`协议。类似地，我们所有的域实体都应该实现`Mappable`协议。

您可能已经注意到，我在`Mappable`中声明了一个名为`NSManagedObjectID` 的属性。我这样做是因为我们正在处理由`NSManagedObjectID`定义的核心数据和`CoreData`实体。将域实体映射到数据库实体时，这是必需的。如果您已经有了实体的自定义 ID，例如 story number，则可能不需要它。

我没有在我们的每个域实体中添加属性`NSManagedObjectID`，而是将它添加到了`Mappable`协议中。

我为我所有的域实体创建了一个基本实体。所有的域实体都应该继承这个`DomainBaseEntity`。来自`Mappable`协议的`NSManagedObjectID`属性在`DomainBaseEntity`中定义。所以没有一个模型类需要提供这个属性。

这是示例域实体的外观。实现`Codable`接口的原因是为了从 JSON 到对象的自动映射。我已经在我的文章 [**这里**](https://medium.com/better-programming/highly-customizable-network-layer-in-swift-5-1e5c1e163674) 中详细解释过了

现在，我们已经准备好创建存储上下文到业务层的映射层，反之亦然。

`BaseDao`是所有数据访问对象(DAO)类的父类。它有可以在`StorageContext`上执行的方法。它将`StorageContext`声明为依赖项。你可以在这里传递`StorageContext`的任何实现，比如`CoreDataStorageContext`或者`RealmStorageContext`。您会看到`BaseDao`期望两种类型的实体:Domain 和 DB。`DomainEntity`应为`Mappable`类型，而`DBEntity`应符合`Storable`协议。这些实体是在域和数据库实体之间进行映射所必需的。

这里我们有一个神奇的类， *Mapper* ，它将实体从域映射到数据库，反之亦然。我使用了[运行时](https://github.com/wickwirew/Runtime)库来迭代属性，并将它们从域实体复制到 DB 实体，反之亦然。

这是一个特定的`StoryDao`，它是`BaseDao`的子类。`BaseDao`的每个子类都应该提供域和 DB 实体。在`StoryDao`的情况下，域实体是`Story`，DBentity 是`StoryEntity`。我更喜欢为每个实体/数据库表创建不同的 DAO。

为了便于使用，我创建了一个`DBManager`来初始化所需的 Dao。我们需要在初始化 DAO 类时提供`StorageContext`实现。`StorageContext`是`DBManager`的依赖，应该在调用任何 DAO 之前设置。这样，您可以在运行时更改`StorageContext`实现。我们还可以为`StorageContext`提供不同的配置类型，比如在运行测试用例时在内存中。

提供`StorageContext`实现的理想位置是在应用程序的开始。但是，它可以根据您的需要进行更改。

# 测试

> 空谈不值钱，给我看看代码。莱纳斯·托沃兹

现在我们都准备好了。我们已经成功创建了一个数据库层，可以直接从服务中开始使用。在下面的代码片段中，我向`createStory()`方法传递了一个新的`story`对象，并将其保存到数据库的`Story`表中。我们所要做的就是提供`story`对象。我们的`storyDAO`已经知道应该用来保存`story`对象的 DB 实体。

# 总有改进的空间

1.  域实体不是 100%独立的，因为它们必须从`DomainBaseEntity`继承。如果您在每个实体中有一些自定义标识符，如`storyNumber`，则可以省略`DomainBaseEntity`。
2.  在`StorageContext`和`BaseDao`中重复基本的数据库 CRUD 操作。由于域和数据库有不同的实体，这是一种折衷。如果你有一个用例，你想在整个项目中使用相同的域和数据库实体，你不需要`BaseDao`。在这个场景中，您的 Dao，比如`StoreDao`，可以直接从`StorageContext`继承，如果需要的话，甚至可以提供定制的方法。

我希望一切都很清楚。你可以从 [Github](https://github.com/waseemwk22/NetworkLayerSwift) 下载完整的项目。

如有任何困惑，请留言。我很乐意帮忙。欢迎建议。
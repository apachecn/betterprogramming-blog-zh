# 轻松采用不同的数据源

> 原文：<https://betterprogramming.pub/adopt-diffable-data-sources-with-ease-85fc128ca6bc>

## 不同的数据源及其优于使用传统数据源方法的优势

![](img/bff4f2c895c2eb0573c4e51126790403.png)

[阿德里安](https://unsplash.com/@aows?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

不同的数据源在 WWDC 2019 上推出，自 iOS 13 以来一直可用。它们取代了传统的`UICollectionViewDataSource`和`UITableViewDataSource`协议，使数据视图中的变更迁移变得更加容易。

与使用传统的数据源方法相比，不同的数据源有一些好处，是当今处理集合和表视图的首选方法。然而，使用旧方法重写大量集成的现有代码可能并不总是容易的。这太糟糕了，因为它夺走了从这个伟大的新 API 中获益的机会。

因此，我决定向您介绍一个不同数据源的简单扩展，它允许您将它集成到现有项目中，而不会有太多麻烦。但是在我们深入研究之前，让我们先来看看有哪些不同的数据源，以及它们会带来什么样的好处。

# 什么是不同的数据源？

可变数据源提供了一种简单、高效的方式来管理对表或集合视图的数据和 UI 的更新所需的行为。它取代了众所周知的方法，如`cellForRowAtIndexPath:`和`numberOfItemsInSection:`。

集合或表格视图有两个类(`UICollectionViewDiffableDataSource`和`UITableViewDiffableDataSource`)。构造示例如下所示:

数据源是通用的，定义了区段标识符的类型和所列数据的类型。单元格提供程序将把这个泛型作为第三个参数的输出，该参数包含需要返回单元格的数据。

数据是通过所谓的快照(数据的快照)提供的。这已经描述了不同数据源的工作方式。数据快照会相互比较，以确定从一个快照到另一个快照所需的最小更改量。

提供数据快照的示例如下:

```
var snapshot = NSDiffableDataSourceSnapshot<Int, BlogPost>()
snapshot.appendSections([1, 2])

snapshot.appendItems(mostPopularBlogPosts, toSection: 1)
snapshot.appendItems(recentBlogPosts, toSection: 2)

dataSource.apply(snapshot, animatingDifferences: true)
```

数据源足够智能，可以计算出差异并替换代码，例如`performBatchUpdates`。

不同的数据源会产生更紧凑、更集中的代码，同时带来一些好处。最终，系统接管了许多繁重的工作。

为了让你明白这一切都包括什么，最好列出一些好处。

# 使用不同数据源的好处

如果您实现了没有不同数据源的集合或表视图数据源，您可能会遇到以下错误:

```
Invalid update: invalid number of items in section 0\. The number of items contained in an existing section after the update (1) must be equal to the number of items contained in that section before the update (1), plus or minus the number of items inserted or deleted from that section (1 inserted, 0 deleted) and plus or minus the number of items moved into or out of that section (0 moved in, 0 moved out).
```

这是我们自己管理数据的结果，其中集合视图的更新与本地管理的数据不同步。这通常是使用`performBatchUpdates`或者迁移从`NSFetchedResultsControllerDelegate`返回的数据的结果。

不同的数据源旨在通过接管计算快照之间的变化的责任来完全消除这种错误。

## NSFetchedResultsController 中不同数据源的采用

另一个不同数据源大放异彩的地方是一个`NSFetchedResultsController`的代表。当显示核心数据实体的列表时，我们经常会在实体改变后遇到更新。我们将迭代每个`NSFetchedResultsChangeType`,以确保我们使用正确的集合或表视图方法执行所有这些更改。

每一次更新都是在`performBatchUpdates`中执行的，我们祈求最好的结果。

这是前述错误可能发生的另一点。为了应用更改，我们还必须编写许多额外的代码。幸运的是，我们现在可以采用一种方法来替换`NSFetchedResultsControllerDelegate`中的所有逻辑:

这就是在核心数据中使用快照应用更改所需的全部代码。

# 一个没有太多麻烦的移植现有代码的扩展

但是迁移旧代码怎么样呢？我向您承诺了一个简单的扩展，它简化了向不同数据源的迁移。我们在不到一天的时间里就在 WeTransfer app 的 [Collect 中实现了这一点，并且已经从这个新的 API 中受益。](https://collect.bywetransfer.com/)

代码重用现有的数据源方法来提供单元格配置。仍然需要提供数据的快照，但是这应该相对容易，并且因情况而异。

通用代码如下所示:

如您所见，代码要求您传入两个参数:

*   我们正在更新的收藏视图
*   对原始集合视图数据源的引用

在单元格和补充视图提供程序中，我们使用原始数据源来获取视图。这大大简化了现有项目中不同数据源的实现，尤其是如果您可以利用前面提到的`NSFetchedResultsControllerDelegate`方法。

一旦您能够放弃 iOS 12，请务必重新访问您的代码，以清理旧的数据源方法。最后，建议完全远离`UICollectionViewDataSource`实现，以使您的代码可读和可理解。同样的代码也适用于`UITableViewDiffableDataSource`,只是做了一些简单的代码修改:

# 关于 uicollectionviewdiffabledata source reference 的注释

我想指出的是，我们在`UICollectionViewDiffableDataSourceReference`上使用了一个扩展。这允许我们远离泛型，使我们的代码对任何类型的任何数据源都可用。这不是推荐的方式:

> "如果你在 Swift 代码库中工作，总是用`[UICollectionViewDiffableDataSource](https://developer.apple.com/documentation/uikit/uicollectionviewdiffabledatasource)`代替."— [苹果开发者](https://developer.apple.com/documentation/uikit/uicollectionviewdiffabledatasourcereference)

然而，代码是可用的，并且运行良好。它是 Objective-C 对象的快速表示，用于管理数据和为集合视图提供项目，因此不太可能不被维护。如果您在代码中发现了使用泛型的机会，我建议您这样做。

# 结论

不同的数据源是 UIKit 的一个很好的新增功能，用于处理集合和表视图中的变化。它消除了一个我们都见过几次的恼人的错误，简化了我们构建数据表示的方式。

感谢阅读！
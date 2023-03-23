# 如何使用' DiffableDataSource '并仍然支持 iOS 12

> 原文：<https://betterprogramming.pub/how-to-use-diffabledatasource-and-still-support-ios-12-765128bc4082>

## 具有向后兼容性的更好的集合视图

![](img/c6aa8d19646934ea6df00d850fe422a7.png)

是的，你仍然可以支持旧手机。(照片由[巴格斯·赫纳万](https://unsplash.com/@bhaguz?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)。)

# 介绍

如果你大量使用表格和收藏视图，iOS 13 为你带来了很棒的东西——`DiffableDataSource`。如果你不熟悉它，你非常欢迎阅读我的(优秀！)[关于如何在你的项目中实现它的教程](https://medium.com/better-programming/ios-13-be-dynamic-with-diffabledatasource-56ed938a0325)。

总之——`DiffableDataSource`是刷新你收藏的一种方式，而`insert` / `delete` / `update`所有的脏计算都是由 UIKit 替你完成的。

# 听起来不错，有什么好处？

`DiffableDataSource`需要 iOS 13。这就是问题所在。

收藏(当我说*收藏时，*我指的是`UITableView`或`UICollectionView`)是我们应用程序的核心组件，你不能通过向 iOS 12 用户展示其他东西来忽视他们。实现`DiffableDataSource`太简单太有用了，等 1-2 年再开始用。

# 这就是为什么回退到“reloadData()”并不那么简单

这就是直觉和逻辑思维——为什么不能只查 iOS 版本调用`reloadData()`？毕竟，害怕升级的用户应该得到更糟糕的体验！

问题的根源在于数据源本身。

您看，要使用`DiffableDataSource`，您需要将 diffable 数据源保存为一个实例变量。

当你有一个需要 iOS 13 的变量的类时，整个类都需要 iOS 13。

换句话说，你不能这样做:

`import UIKit`

`class MyTableViewController: UIViewController {`

`@available(iOS 13.0, *)`

`var datasource : UITableViewDiffableDataSource<String, String>?`

`}`

如果整个班级都需要 iOS 13，这意味着你需要重复你的分类或子类化——或者想出另一个该死的解决方案。

# 我想我得等到明年了…

先别走！有解决的办法，而且比你想象的简单多了。

把你的`datasource`变量标记为`lazy`，就可以限制到 iOS 13。

原因是`lazy`变量只有在你调用它们的时候才是相关的。因为您可以对调用本身进行版本检查，所以您可以使用`@available`操作符来限制它们。

让我们来看看解决方案:

`import UIKit`

`class MyTableViewController: UIViewController {`

`@available(iOS 13.0, *)`

`lazy var datasource = UITableViewDiffableDataSource<String, String>()`

`}`

现在成功了！

# 那么我们能在支持 iOS 12 的同时使用‘diffabledata source’吗？

我们当然可以。但是你需要做两件事来纠正它。

首先我们要明白，iOS 12 仍然使用`UITableViewDatasource`协议的`cellForRow()`方法。

当你实现`DiffableDataSource`时，`cellForNow()`不是修改`TableView`单元格的方法，当你用`cellProvider`参数初始化`diffableDataSource`时，你正在做这件事。

这意味着您需要将代码合并到第三个函数中，这样您的项目中就不会有代码重复。

其次(现在……)，每当需要刷新表视图时，都需要创建一个后备。

大概是这样的:

`if #available(iOS 13.0, *) {`

`updateSnapshotFromNewData(newData: newData)`

`} else {`

`tableView.reloadData()`

`}`

# 摘要

`DiffableDataSource`是一大特色。你的大多数用户可能已经在运行 iOS 13，通过简单的实现，你可以给他们更好的用户体验，同时仍然保持对那些 iOS 12 用户的支持。天知道他们在等什么更新。
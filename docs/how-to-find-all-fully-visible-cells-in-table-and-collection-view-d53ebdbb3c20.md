# 如何在表格和集合视图中找到所有完全可见的单元格

> 原文：<https://betterprogramming.pub/how-to-find-all-fully-visible-cells-in-table-and-collection-view-d53ebdbb3c20>

## 不… `indexPathsForVisibleRows`不是解决办法

![](img/a96e9983719ed7f465e1a28f273bba6e.png)

作者图片

假设您有一项任务，要找出哪些表格视图单元格当前在屏幕上完全可见。所以你启动 Xcode，访问`UITableView`的`indexPathsForVisibleRows`,并期望它能完成工作。

很快，你会发现有些事情不对劲。你得到的结果有点偏差。屏幕上部分可见的单元格也会包含在结果中，这不是您想要的。你应该怎么做呢？

解决方案其实很简单:你需要做的就是跳出框框思考。让我告诉你怎么做。

# 获取 UITableView 的索引路径

为了找到所有完全可见单元格的索引路径，我们必须首先有一种方法来确定一个单元格是否基于其索引路径完全可见。

那么“细胞完全可见”的另一种说法是什么呢？

其内容视图与表格视图完全相交的单元格被视为完全可见的单元格。

考虑到这一点，我们将上述语句翻译成如下代码:

```
let cellFrame = tableView.rectForRow(at: indexPath)
let isCellFullyVisible = tableView.bounds.contains(cellFrame)
```

一旦我们能够根据单元格的索引路径确定单元格是否完全可见，获取所有完全可见单元格的索引路径就变得相当简单了。

表视图到此为止！接下来，集合视图！

# 获取 UICollectionView 的索引路径

就集合视图而言，一般的方法基本上是相同的。然而，集合视图的工作方式与表格视图略有不同，我们不能直接获得集合视图单元格的框架。我们必须通过布局属性访问单元格的框架，如下所示:

过滤部分与表格视图基本相同，只是要确保使用`indexPathsForVisibleItems`而不是`indexPathsForVisibleRows`。

# 更进一步

作为优秀的开发人员，我们应该始终致力于编写干净且可重用的代码。因此，让我们编写我刚才向您展示的代码，它是表视图和集合视图功能的扩展。它看起来是这样的:

有了这些，获取所有完全可见单元格的索引路径就变得简单了，就像:

很干净，不是吗？

```
**Want to Connect?**You can also follow me on [Twitter](https://twitter.com/Lee_Kah_Seng) so you won’t miss out any of my upcoming iOS development-related articles.
```

感谢阅读。👨🏻‍💻
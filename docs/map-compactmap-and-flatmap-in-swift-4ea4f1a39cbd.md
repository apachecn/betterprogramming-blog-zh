# Swift 中的地图、紧凑地图和平面地图

> 原文：<https://betterprogramming.pub/map-compactmap-and-flatmap-in-swift-4ea4f1a39cbd>

## 如何以及何时使用它们

![](img/cc5354b3695ed139dad64e9a40ae785c.png)

来源: [Undraw](https://undraw.co/search)

在普通的应用程序中，使用数组是很常见的。通常，我们需要一个接一个地获取所有元素，并对它们进行一些转换。

这种情况经常发生，我看到许多初级或中级开发人员使用类似于`forEach`的经典迭代来执行这项任务，他们不知道在`Foundation`框架中有更好的函数来解决这个问题。

# 地图

map 是一个标准的 Swift 函数，用在一个集合上，它接受一个应用于每个元素的闭包的输入，并返回一个不同的集合。换句话说，你有一个起始数组，你一个接一个地获取数组的所有元素，对每个元素应用一个由闭包控制的变换，并将结果值附加到一个新数组上。

好了，举个例子吧。假设我们想创建一个游戏，给玩家一个漫威英雄名字的字谜，他/她必须猜出正确的角色。我们有一个名字列表，我们必须对列表中的每一项应用接下来的步骤:

1.  将名称转换成小写
2.  删除空白
3.  打乱字母顺序

例如，如果没有使用`forEach`的`map`，你当然需要为每一步创建一个空数组，对每个元素应用该步骤并附加结果……三个数组，太多东西，很难阅读和理解。让我们看看这种方法有多冗长:

用`map`代替，一切都变得非常容易和可读。就像魔术一样！

如你所见，即使你不知道所有的步骤，这也是非常可读的。

## 紧凑地图

`CompactMap`工作类似于 map，但是有一个额外的好处…它移除了变换给出的每个`nil`元素。

让我们看一个简单的例子:

如您所见，表情图标无法创建有效的 url。使用`compactMap`你可以过滤它，没有任何问题或其他检查。

`CompactMap`也是过滤列表以删除任何`nil`值的好方法。最近我在开发一个应用程序，其中一个视图控制器的所有 UI 标签都由服务器发送的数据控制。如果有一个值，你必须显示标签，如果没有，标签将被隐藏，不留空白。

在一个`UITableView`中一切都是动态的，所以我只需要在 data source 中添加或不添加一个项目(如果你对我个人处理数据源和部分的技术感兴趣[看看这篇文章](/uitableview-sections-with-nested-types-27eabb833ad9))。

嗯，用`compactMap`过滤所有东西非常快，非常易读。类似这样的东西，当然在应用程序中要复杂一点:

请注意最新例子中的一件事。字段数组的类型是`[String?]`，而条目数组的类型是`[String]`，所以`compactMap`的返回类型永远不是`optional`。哇，太神奇了！

# 平面地图

最后一种类型的映射是一个函数，顾名思义，它将数组中的数组展平。它只是将一系列数组一次转换成一个数组，如下所示:

```
[[1, 1, 1], [2, 2, 2], [3, 3, 3]] -> [1, 1, 1, 2, 2, 2, 3, 3, 3]
```

举个例子吧。

你正在编写一个有战斗英雄小队的游戏。你的队伍有一群角色，你决定每场比赛中谁战斗。

在每一轮中，被击败的敌人数量会被添加到玩家英雄的属性中。你现在需要计算你的队伍每轮被击败的敌人的平均数量，所以基本上你需要创建一个所有英雄的所有数据的数组，并找出平均值。

让我们看一下代码:

所有的魔法都在第 25 行完成了。请注意，为了保持良好的函数式编程风格，我使用了一个扩展来计算平均值(第 26 行)，而不是使用`Accelerate`框架的`vDSP.mean`函数([链接，此处为](https://developer.apple.com/documentation/accelerate/vdsp/3241022-mean))。

# 还有一点

如果你仔细阅读了文章的开头，你可能已经注意到我写的是集合而不是简单的数组。你知道为什么吗？因为地图功能也可以应用于字典！无论如何使用字典的这些功能，都需要格外小心:

正如您在示例中看到的，原始字典被拆分成一个字典数组。现在，您可能希望仅对字典的值而不是键应用转换。

你知道吗？Swift 有一系列针对所有前三种变体的地图来完成这项任务！只需使用`mapValues`版本:

如您所见，结果是一个`[String: String]`，而不是像前一个例子中的`[[String: String]]`。

我希望你喜欢这篇文章，如果你喜欢，请鼓掌。
如果这篇文章对你有用，请随意[给我一杯咖啡](https://www.buymeacoffee.com/dy59tqxn794)，并允许我创作更酷的内容和文章。

享受你的编码！
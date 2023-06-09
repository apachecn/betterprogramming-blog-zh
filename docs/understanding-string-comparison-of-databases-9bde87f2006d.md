# 了解数据库的字符串比较

> 原文：<https://betterprogramming.pub/understanding-string-comparison-of-databases-9bde87f2006d>

## 加快字符串比较的三种方法

![](img/a07dadb4308137b817632443672baffc.png)

照片由 [Niko Photos](https://unsplash.com/@niko_photos) 在 [Unsplash](https://unsplash.com/photos/tGTVxeOr_Rs) 上拍摄

在上一篇文章中，我们简要讨论了基于 B 树的数据库如何使用[部分索引](/partial-indexing-faq-55d6f1e10c08)来提高性能。在这篇文章中，我们将谈论另一个索引的故事。

一般来说，基于 B 树的数据库索引有一个特点，就是最左匹配。以 MySQL 为例，如果`WHERE`条件是`a = 1 AND b = 2 AND c = "Hello"`，那么索引必须按照这个顺序建立，即`(a, b, c)`。

另外，如果索引是`(a, b, c, d)`，那么可以基于最左匹配原则应用相同的查询。

此外，如果我们要查询的条件是`c`字段中的字符串匹配，例如`WHERE a = 1 AND b = 2 AND c LIKE "Hello%"`，这样的条件仍然可以应用于`(a, b, c)`索引。因为最左边的匹配规则，即使使用了`LIKE`，字符串的最左边已经被指定了，所以仍然符合规则。

相反，如果查询条件变为`WHERE a = 1 AND b = 2 AND c LIKE "%World"`，那么只有`a`和`b`字段可以被索引，而`c`字段必须比较所有匹配`(a, b)`值的行，这将使用数据库的大量 CPU 计算，换句话说，非常消耗资源。

# 如何改善？

改善这种情况的最简单的解决方案是去掉字符串中间和后面的百分号。然而，首先必须有这样使用它的用例，所以本文不会要求您简单地放弃它，而是提供一些替代方案。

# 修改最外层的客户端

修改原始字符串模式，以便颠倒索引顺序。

例如，原始查询需要将字符串模式`"PREFIX_ABC_SUFFIX"`与类似`a LIKE "%ABC%"`的查询进行匹配。

然后反转原来的字符串模式，改成`"ABC_PREFIX_SUFFIX"`。通过这一更改，您可以删除顶部的百分号，并将查询更改为最左侧的匹配。

# 修改数据库模式

向原始数据库表添加一个新的布尔字段`abc_matched`。数据库客户端在写入数据时，会预先判断原始字段是否满足条件，并设置相应的标志。

也就是说，原来的查询`a LIKE "%ABC%"`可以改成`abc_matched = 1`。

通过这样做，数据库计算可以转移到数据库客户端，从而有效地减少数据库开销。

这种做法在数据科学中也很常见，被称为数据预处理。它用一个操作代替了后续查询的 N 个操作。

# 修改索引类型

在 MySQL 中，没有办法进行这种查询。

然而，在 Postgres 和 MongoDB 中都有相应的索引类型，可以有效地处理模糊字符串比较。

Postgres 使用`GIN`索引而不是默认的`Bitmap`类型，而 MongoDB 使用`text`索引而不是常规索引。

通过修改索引类型，即使是字符串的模糊比较也能以良好的性能完成。然而，所有的技术选择都有优点和缺点；GIN 或 text 类型的索引占用大量空间，是默认类型的几倍，并且建立索引的过程非常耗时。

所以，在资源有限的情况下，即使解决方案看起来非常容易实现，我也不那么推荐。

# 结论

在软件开发中，总是有许多方法来解决现有的问题，每种方法都有优点和缺点。

以本文中提到的三种方法为例。

修改最外层客户端的成本最高，但设计新功能的成本最低。

因为客户端的修改之后是新旧版本的兼容，此外，最外层的客户端可能是一个移动应用，不仅部署困难，而且难以推广。

但是，如果一开始设计是正确的，那么实现成本是非常低的，因为它只是原始字符串的逆序。

修改数据库模式的成本第二高，虽然不一定需要更改最外层的客户端，但也需要更改数据库的客户端。

数据库模式的修改需要旧数据的迁移，这也需要时间和人力。

然而，数据的预处理提供了除提高查询性能之外的额外好处，例如更显式的特征矩阵，这可以有效地提高机器学习期间模型的准确性。

修改索引类型的实现成本最低，只需修改一次索引，基本不需要修改应用的实现。

但这是系统的最高成本。

首先，这样的索引类型占用了非常大的内存空间，在数据量很大的情况下是不能忽略的。

其次，当索引太大而不适合数据库内存大小或者必须频繁交换时，它也会影响其他查询的性能。

此外，索引的复杂性会影响写操作的性能，如果这是一个高频率的写系统，那么这是一个必须仔细考虑的开销。

因此，虽然有三种方法，但是对于哪一种适合还是需要权衡的，并且每个系统的答案也不尽相同。
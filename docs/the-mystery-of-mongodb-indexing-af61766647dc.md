# MongoDB 索引的奥秘

> 原文：<https://betterprogramming.pub/the-mystery-of-mongodb-indexing-af61766647dc>

## 了解 ESR 规则和索引交集

![](img/98b59c025e08f436955681f1fa9cd3ea.png)

杰里米·毕晓普在 [Unsplash](https://unsplash.com/photos/vGjGvtSfys4) 上拍摄的照片

我相信我们都熟悉 MySQL 的索引规则。因为它是从 B+树构建的，所以 MySQL 索引有一个最左边的匹配规则。为了使查询与索引匹配，MySQL 查询语法中使用的列是从左到右排列的。例如:

```
SELECT * FROM table WHERE a = 1 AND b > 2 ORDER BY c;
```

这种查询最有效的索引是像`(a, b, c)`这样的复合索引。但是，这样的索引不能应用于

```
SELECT * FROM table WHERE a = 1 ORDER BY c;
```

这是因为它缺少必要的`b`列。

这是 MySQL 的索引规则，一般来说，关系数据库遵循几乎相同的规则来实现。但是，MongoDB 用 B 树实现有细微的区别。

# [MongoDB ESR 规则](https://www.mongodb.com/docs/manual/tutorial/equality-sort-range-rule/#std-label-esr-indexing-rule)

我们将继续使用上一节中提到的查询作为演示。

```
db.table.find({a: 1, b: {$gt: 2}}).sort({c: 1})
```

与前面提到的 MySQL 查询非常相似，但是 MongoDB 的 MQL 被重写。然而，对于这样的查询有效的索引是`(a, c, b)`，而不是前面提到的`(a, b, c)`。

这是因为 MongoDB 的复合索引必须遵循 ESR (Equality，Sort，Range)规则。因此，在上面的例子中，`b`用于范围匹配，`c`用于排序，所以正确的索引顺序是`c`在`b`之前。

# [MongoDB 索引交集](https://www.mongodb.com/docs/manual/core/index-intersection/)

除了与关系数据库不同的 ESR 规则，MongoDB 还有另一个奥秘:它可以在同一个查询中使用多个(比如说 2 个)索引，这被称为索引交集。

继续以上一节中的查询为例。

```
db.table.find({a: 1, b: {$gt: 2}}).sort({c: 1})
```

为了提高查询性能，我们建议使用`(a, c, b)`复合索引，但实际上，我们可以用两个索引达到相同的结果。

1.  b
2.  (甲、丙)

我们创建一个单一索引`b`和一个复合索引`(a, c)`，这也可以提高查询性能。

我们为什么要用`(a, c)`？因为即使是指数交集也必须遵循 ESR 规则，所以我们把 ES 和 r 分开。

这有什么好处？最大的好处是指数的构成变得更加灵活。如果只创建一个索引`(a, c, b)`，那么如果单独查询`b`，就没有匹配的索引可以使用，所以必须额外创建一个`b`的索引。众所周知，索引其实是一种成本，会占用内存，影响写入效率。换句话说，如果一个更紧凑的索引可以用来覆盖更复杂的查询条件，那么这样的索引将更有价值。

# [MongoDB ESR“隐藏”规则](https://www.mongodb.com/docs/manual/tutorial/equality-sort-range-rule/#additional-considerations)

当使用 MySQL 时，我们使用`IN`进行范围查询，但是`IN`实际上是 MySQL 中的一个等式。也就是说，当查询为`WHERE a IN (2, 3)`时实际上等同于`WHERE a = 2 OR a = 3`。

但是，在 MongoDB 中，却不是。

如果你只是简单地使用一个列`$in`，那么它仍然具有与 MySQL 相同的行为。

比如`find({a: {$in: [2, 3]}})`和`find({$or: [{a: 2}, {a: 3}]})`是等价的，都属于 ESR 法则的 E。

但是如果与 sort 一起使用，那么`$in`将被视为范围匹配，例如 r:

```
find({a: {$in: [2, 3]}}).sort({b: 1})
```

这里`a`被当作 R，所以为了满足这样的查询，要创建的索引应该是`(b, a)`而不是`(a, b)`。

# 结论

如果您只有使用关系数据库的经验，很容易被功能丰富的 NoSQL 数据库所迷惑，特别是因为底层的 MongoDB 实际上是一个类似于关系数据库的 B 树家族。但是在实现细节上还是有很大的差异。

在创建 MongoDB 索引时，注意 ESR 规则尤为重要。许多从 MySQL 迁移过来的用户很容易在没有注意到它的情况下被这条规则所困扰。

事实上，即使是使用`(a, b, c)`的索引，在数据量很小时也不会产生问题；MongoDB 在内存中排序，但是当数据量增长到一定大小时，MongoDB 无法在内存中加载整个数据集，使用硬盘访问。那么表现会很悲剧。

此外，索引交集特性为用户创建索引提供了更大的灵活性，并为他们提供了更多样化的查询。但是，在使用指数交集时，了解 ESR 规则也很重要，以免得不偿失。
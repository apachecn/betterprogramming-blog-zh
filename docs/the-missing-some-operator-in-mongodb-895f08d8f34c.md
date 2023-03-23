# MongoDB 中缺少的 some 操作符

> 原文：<https://betterprogramming.pub/the-missing-some-operator-in-mongodb-895f08d8f34c>

## 在$in 和$all 之间有一个空白，新的数组查询操作符可以派上用场

![](img/b1db25f11e3b1de4b7b8900400a5fa73.png)

带镜片的长颈鹿，因为何乐而不为？-James Bold 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 MongoDB 中，查询字段值为数组的文档非常简单。在一个数组字段上使用`[$in](https://docs.mongodb.com/manual/reference/operator/query/in/#op._S_in)`比较操作符将会给出这样的文档，其中该字段的数组包含至少一个您提供的值。

您还有一个数组查询操作符`[$all](https://docs.mongodb.com/manual/reference/operator/query/all/#op._S_all)`，其工作方式相同，但是数组需要保存您提供的所有值。

这两个操作符涵盖了很多领域，但是当您希望查询数组至少包含四个值(或者正好八个值)的文档时，MongoDB 缺少一个有用的操作符。

我们举个简单的例子。您有一个客户集合，每个客户文档都有一个`purchasedProducts`字段，它是一个`ObjectId`的数组(引用客户已经购买的所有产品)。

现在，对于一个营销活动，您希望找到所有购买了十种选定产品中至少三种的客户，以便向这些客户发送一条消息。

在这个场景中，`$in`操作符不会这样做，因为它会给出至少购买了一种产品的所有客户。`$all`操作符也不起作用，因为它只会给出购买了该集合中所有十种产品的客户。

在这个场景中，一个`$some`操作符将会派上用场。

`$some`操作符可以接受一个包含两项的数组；第一项是要匹配的值的数组，第二项是声明匹配条件的对象。

虽然`$some`运算符不存在，但仍然有可能获得结果。使用一个聚合管道，我们可以获得创建一个`$some`操作符的等价物所需的工具。

理论上，`$some`操作符要做的是在文档上的一个数组字段和所提供的要匹配的值数组之间创建一个交集。

然后将这个交集的大小(数组长度)与提供的条件进行比较，如果大小至少为 2(在上面的例子中)，那么这个文档就是匹配的。

要将其转化为聚合管道，我们需要四个阶段:

1.  将文档数量缩小到至少与一个提供的值匹配的文档。
2.  在文档上的数组字段和提供的值数组之间创建交集。获取交集的大小，将其与您的匹配条件进行比较，并将结果保存为文档上的一个`temporary`字段。
3.  基于`temporary`字段，过滤掉任何不匹配的文档。
4.  通过从每个文档中删除`temporary`字段来清理文档

*正如* [*Asya*](https://medium.com/u/a433fb342895?source=post_page-----895f08d8f34c--------------------------------) *所指出的，在对故事的响应中，没有必要使用聚合管道。使用*[*$ expr*](https://docs.mongodb.com/manual/reference/operator/query/expr/index.html)*操作符，实际上可以使它变得更短，并且在常规收集方法中可用。*👍

尽管有可能解决缺少`$some`操作符的问题，但这有点冗长，而$some 操作符将是对$in 和$all 操作符的一个很好的补充。

让我知道你对一个`$some`操作符的想法，如果你碰巧在 MongoDB 开发团队中有联系人，请随意与他们分享这篇文章作为特性请求。
# 设计 DynamoDB 表的技巧

> 原文：<https://betterprogramming.pub/tips-for-design-a-dynamodb-table-d12faf3d0a67>

## 我设计 DynamoDb 表的技巧和诀窍

## 高效便捷地

![](img/61511027a0cfb65c4bac71138a3b15d4.png)

Jan Antonin Kolar 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是 DynamoDB？

Amazon DynamoDB 是一个完全托管的、无服务器的键值 NoSQL 数据库，旨在运行任何规模的高性能应用程序。在对数据建模时，需要考虑一些事情，比如访问模式和安全性。

今天我将描述一些我在用单表设计来设计 DynamoDb 表时考虑的技巧。

# 类型对象的名称

当我的表中有多个类型对象时，我添加对象名作为前缀作为分区键。为什么？如果要使用 IAM 策略限制访问，可以指定类型对象的名称。在这种情况下，您可以创建一个策略，其中一个组只能读取该类型的对象，另一个组可以读取和写入相同类型的对象。

`TYPE#ID`

示例:

```
Event#123 (for Event Object)User#245 (for User Object)
```

您可以在单独的字段中添加相同的名称:这样，您可以从 GSI 中查询相同类型的所有记录。

# 使用更新项目

使用`UpdateItem`来更新或添加一个项目是一件好事。如果一个元素已经存在，你不必担心覆盖它，因为`UpdateItem`只更新特定的属性。

如果您需要“覆盖”整个项目，您可以使用`PutItem`来代替。

## 创建日期/更新日期

另一个好主意是保存记录的最后更新和创建日期。您可以填写`updatedAt`和`createdAt`来记录审计或向最终用户显示这些信息。

`createdAt = if_not_exists(createdAt, :now), updatedAt = :now`

## 来源名称

当您有多个更新 DynamoDb 的源时，您想知道是谁更新了该项。一个好的做法是添加一个带有源名称的字段。

您可以使用此字段过滤流或审计的元素。

例如:在`dynamodb` streams 上附加了一个 lambda 来更新记录。该更新可能会触发更新循环。您可以使用按事件桥筛选条件来筛选具有特定源名称的所有记录。

# 所有者 Id

在您的分区键中，您可以添加写记录的所有者用户`id`。

`OWNERID#TYPE#ID`

正如我们看到的对象名称，您可以有多个用户，他们可以读写自己的记录。如果在分区键的第一部分添加用户`id`，就可以查询该用户拥有的所有记录。

如果您使用 IAM 策略，您可以限制用户`id`对所有记录的访问，这样，一个用户就不能代替另一个用户访问记录。

# 时间戳与 ISO 日期时间

根据您希望如何检索数据，排序关键字的类型将至关重要。

如果您想对数据创建的元素列表进行排序，您可以简单地设置类型`string`并存储`datetime iso`。默认情况下，DynamoDb 按字节对元素进行排序。

以下是 ISO `Datetime`中过滤日期的一些例子:

*   获取所有六月项目

```
KeyConditionExpression: 'begins_with(#date, “2022–06”)'
```

*   获取所有 6 月 22 日的项目

```
KeyConditionExpression: 'begins_with(#date, “2022–06–22”)'
```

额外案例:对 ISO 日期时间使用 between-like

```
KeyConditionExpression: 'begins_with(#date, "2022–06"),
ExpressionFilter: 'begins_with(#date, "2022–06–21") or begins_with(#date, "2022–06–22")'
```

有了它，你可以得到两天的数据(像中间)。

> 记住:首先 DynamoDb 查询整个 6 月的所有数据，然后根据 6 月 22 日和 21 日进行过滤。这不是一个有效的方法，但它是有效的。

相反，如果你需要在日期之间过滤，正确的选择是`timestamp`与数字类型。如果您有许多需要这种目的的访问模式，那么效率会更高。

今天到此为止！单表设计的最佳实践是什么？我很想听听你的建议。

谢谢你，再见。
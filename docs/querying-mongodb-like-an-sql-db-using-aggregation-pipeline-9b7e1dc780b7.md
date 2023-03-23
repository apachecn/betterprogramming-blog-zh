# 使用聚合管道像查询 SQL DB 一样查询 MongoDB

> 原文：<https://betterprogramming.pub/querying-mongodb-like-an-sql-db-using-aggregation-pipeline-9b7e1dc780b7>

## 要像老板一样在 MongoDB 中使用 SQL SELECT、GROUP、JOIN、LIMIT 和 OFFSET 查询，您只需要知道

![](img/e16c18c67694c0f192c62a48da2fc364.png)

斯蒂芬·道森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是聚合？

聚合操作处理数据记录并返回计算结果。聚合操作将多个文档中的值组合在一起，并可以对分组的数据执行各种操作以返回单个结果。

在`db.collection.aggregate`方法和`[db.aggregate](https://docs.mongodb.com/manual/reference/method/db.aggregate/#db.aggregate)`方法中，[流水线](https://docs.mongodb.com/manual/core/aggregation-pipeline/)阶段出现在一个数组中。文档按顺序通过各个阶段。我们将经历一些阶段来实现类似关系数据库的结果。

# $match(在哪里)

过滤文档，只将符合指定条件的文档传递到下一个管道阶段。

它有以下原型:

```
{ $match: { <query> } }
```

它相当于 SQL 查询中的 WHERE。让我们举个例子把事情说清楚。这个例子使用了一个名为`articles`的集合，包含以下文档:

```
{ "_id" : ObjectId("512bc95fe835e68f199c8686"), "author" : "dave", "score" : 80, "views" : 100 }
{ "_id" : ObjectId("512bc962e835e68f199c8687"), "author" : "dave", "score" : 85, "views" : 521 }
{ "_id" : ObjectId("55f5a192d4bede9ac365b257"), "author" : "ahn", "score" : 60, "views" : 1000 }
{ "_id" : ObjectId("55f5a192d4bede9ac365b258"), "author" : "li", "score" : 55, "views" : 5000 }
{ "_id" : ObjectId("55f5a1d3d4bede9ac365b259"), "author" : "annT", "score" : 60, "views" : 50 }
{ "_id" : ObjectId("55f5a1d3d4bede9ac365b25a"), "author" : "li", "score" : 94, "views" : 999 }
{ "_id" : ObjectId("55f5a1d3d4bede9ac365b25b"), "author" : "ty", "score" : 95, "views" : 1000 }
```

平等匹配。

```
db.articles.aggregate(
    [ { $match : { author : "dave" } } ]
);// Result
{ "_id" : ObjectId("512bc95fe835e68f199c8686"), "author" : "dave", "score" : 80, "views" : 100 }
{ "_id" : ObjectId("512bc962e835e68f199c8687"), "author" : "dave", "score" : 85, "views" : 521 }
```

在`$match`中我们可以有多个约束，比如`$or`、`$and`等。根据我们的要求，虽然它也有一些限制。你可以在 [Mongo 文档](https://docs.mongodb.com/manual/reference/operator/aggregation/match/)中读到。

# $skip(偏移)

跳过传递到该阶段的指定数量的[文档](https://docs.mongodb.com/manual/reference/glossary/#term-document)，并将剩余的文档传递到[管道](https://docs.mongodb.com/manual/reference/glossary/#term-pipeline)中的下一阶段。它有以下原型:

```
{ $skip: <positive integer> }
```

在上面的例子中，我们能够匹配与`dave`相关的记录。如果我们想从头开始跳过几个结果，我们可以将查询写成:

```
db.articles.aggregate([ 
    { $match : { author : "dave" } },
    { $skip: 1 } 
]);// We are skipping 1 result and we should get just this{ "_id" : ObjectId("5dc1d22f24a8e913bfcf4f60"), "author" : "dave", "score" : 85, "views" : 521 }
```

我们通常使用`$skip`和`$limit`对数据进行分页，让我们将更多的记录插入到我们的集合中，并在下一节中看看`$skip`和`$limit`如何协同工作。

# 美元限额(限额)

限制传递到[管道](https://docs.mongodb.com/manual/reference/glossary/#term-pipeline)中下一个阶段的文档数量。它有以下原型:

```
{ $limit: <positive integer> }
```

我们已经为`dave`添加了新的记录，让我们用一个简单的`$match`看看这些系列看起来怎么样:

```
db.articles.aggregate(
    [ { $match : { author : "dave" } } ]
);{ "_id" : ObjectId("5dc1d22124a8e913bfcf4f5f"), "author" : "dave", "score" : 80, "views" : 100 }
{ "_id" : ObjectId("5dc1d22f24a8e913bfcf4f60"), "author" : "dave", "score" : 85, "views" : 521 }
{ "_id" : ObjectId("5dc1d53924a8e913bfcf4f65"), "author" : "dave", "score" : 185, "views" : 1521 }
{ "_id" : ObjectId("5dc1d54f24a8e913bfcf4f66"), "author" : "dave", "score" : 15, "views" : 21 }
```

在示例集合中，我们有四个匹配的记录。假设您被要求对结果进行分页，以便一次显示两个结果，您会怎么做？让我们看看。

```
db.articles.aggregate([ 
    { $match : { author : "dave" } },
    { $skip: 0},
    { $limit: 2}
]);// We are not skipping any records and but limiting the records to 2{ "_id" : ObjectId("5dc1d22124a8e913bfcf4f5f"), "author" : "dave", "score" : 80, "views" : 100 }
{ "_id" : ObjectId("5dc1d22f24a8e913bfcf4f60"), "author" : "dave", "score" : 85, "views" : 521 }// We got the first two results, to get the next two results just update the $skipdb.articles.aggregate([ 
    { $match : { author : "dave" } },
    { $skip: 2},
    { $limit: 2}
]);// This should give two records after skipping the first two.{ "_id" : ObjectId("5dc1d53924a8e913bfcf4f65"), "author" : "dave", "score" : 185, "views" : 1521 }
{ "_id" : ObjectId("5dc1d54f24a8e913bfcf4f66"), "author" : "dave", "score" : 15, "views" : 21 }
```

到目前为止，我们做得很好，但是假设您的经理走到您面前，让您按视图对结果进行排序。你要怎么办？我们为此准备了`$sort`。

# $sort(排序依据)

对所有输入文档进行排序，并按排序后的顺序将它们返回到管道。它有以下原型:

```
{ $sort: { <field1>: <sort order>, <field2>: <sort order> ... } }
```

让我们使用管道中的`$sort`阶段。

```
1 to specify ascending order
-1 to specify descending orderdb.articles.aggregate([ 
    { $match : { author : "dave" } },
    { $sort: { views: 1}}
]);// Result{ "_id" : ObjectId("5dc1d54f24a8e913bfcf4f66"), "author" : "dave", "score" : 15, "views" : 21 }
{ "_id" : ObjectId("5dc1d22124a8e913bfcf4f5f"), "author" : "dave", "score" : 80, "views" : 100 }
{ "_id" : ObjectId("5dc1d22f24a8e913bfcf4f60"), "author" : "dave", "score" : 85, "views" : 521 }
{ "_id" : ObjectId("5dc1d53924a8e913bfcf4f65"), "author" : "dave", "score" : 185, "views" : 1521 }Voilà ! The results are sorted now.
```

尽可能早地将`$match`放在聚合[管道](https://docs.mongodb.com/manual/reference/glossary/#term-pipeline)中。因为`$match`限制了聚合管道中的文档总数，所以更早的`$match`操作可以最小化管道中的处理量。

# $组

根据指定的`_id`表达式对输入文档进行分组，并为每个不同的分组输出一个文档。

每个输出文档的`_id`字段包含唯一的分组依据值。输出文档还可以包含计算字段，这些字段包含一个[累加器表达式](https://docs.mongodb.com/manual/reference/operator/aggregation/group/#accumulators-group)的值。它有以下原型:

```
{
  $group:
    {
      _id: <expression>, // Group By Expression
      <field1>: { <accumulator1> : <expression1> },
      ...
    }
}
```

假设我们想按作者对文章进行分组，换句话说，按每个作者的文章数量进行分组，我们可以利用管道中的分组阶段。所以，让我们来看看现场直播:

```
db.articles.aggregate([ 
    { $group : { _id: "$author", count: { $sum: 1 }}},
    { $sort: { count: 1 }}
]);// We have grouped the articles by author ann getting the count and sorting it by count{ "_id" : "annT", "count" : 1 }
{ "_id" : "ahn", "count" : 1 }
{ "_id" : "li", "count" : 2 }
{ "_id" : "dave", "count" : 4 }// We can have more constraints like if we want only the results whose count is greater than 1, then we can add a $match stage in the pipeline after $groupdb.articles.aggregate([ 
    { $group : { _id: "$author", count: { $sum: 1 }}},
    { $sort: { count: 1 }},
    { $match: { count : { $gt: 1 }}}
]);{ "_id" : "li", "count" : 2 }
{ "_id" : "dave", "count" : 4 }
```

让我们把这一组提高一个档次。假设我们想按数组结构中存储的值进行分组。我们有个东西叫`$unwind`。让我们看看它是如何工作的。

# $展开

从输入文档中解构一个数组字段，为每个元素输出一个文档。每个输出文档都是输入文档，其中数组字段的值被元素替换。

可以将数组字段路径传递给`$unwind`。使用此语法时，如果字段值为 null、缺失或空数组，`$unwind`不会输出文档。它有以下原型:

```
{ $unwind: <field path> }
```

让我们用下面的命令获取一个新集合`inventory`和一个新记录:

```
db.inventory.insertOne({ "_id" : 1, "item" : "ABC1", sizes: [ "S", "M", "L"] })
```

这就是 MongoDB 的美妙之处，您可以创建一个新的集合，每个文档都与输入文档相同，除了`sizes`字段的值现在保存了来自原始`sizes`数组的值。在没有任何设置的情况下向其添加记录。

让我们按尺寸来看这个。

```
db.inventory.aggregate( [ { $unwind : "$sizes" } ] )// Result
{ "_id" : 1, "item" : "ABC1", "sizes" : "S" }
{ "_id" : 1, "item" : "ABC1", "sizes" : "M" }
{ "_id" : 1, "item" : "ABC1", "sizes" : "L" }
```

除了`sizes`字段的值之外，每个文档都与输入文档相同，该字段现在保存来自原始`sizes`数组的值。

让我们取一个新的集合`inventory2`并按大小做一个分组，使用这个命令插入更多的记录:

```
db.inventory2.insertMany([
  { "_id" : 1, "item" : "ABC", price: NumberDecimal("80"), "sizes": [ "S", "M", "L"] },
  { "_id" : 2, "item" : "EFG", price: NumberDecimal("120"), "sizes" : [ ] },
  { "_id" : 3, "item" : "IJK", price: NumberDecimal("160"), "sizes": "M" },
  { "_id" : 4, "item" : "LMN" , price: NumberDecimal("10") },
  { "_id" : 5, "item" : "XYZ", price: NumberDecimal("5.75"), "sizes" : null }
])
```

如果我们展开它，我们会得到这样的结果:

```
db.inventory2.aggregate( [ { $unwind: "$sizes" } ] )// Results{ "_id" : 1, "item" : "ABC", "price" : NumberDecimal("80"), "sizes" : "S" }
{ "_id" : 1, "item" : "ABC", "price" : NumberDecimal("80"), "sizes" : "M" }
{ "_id" : 1, "item" : "ABC", "price" : NumberDecimal("80"), "sizes" : "L" }
{ "_id" : 3, "item" : "IJK", "price" : NumberDecimal("160"), "sizes" : "M" }// Notice it ignores the null and undefined valuesdb.articles.aggregate([ 
    { $unwind: { path: "$sizes" } },
    { $group: { _id: "$sizes", count: { $sum: 1 }}}
]);// Results{ "_id" : "M", "count" : 2 }
{ "_id" : "L", "count" : 1 }
{ "_id" : "S", "count" : 1 }
```

我们可以对此应用不同的阶段，如`$match`、`$sort`、`$skip`、`$limit`等。才能得到想要的结果。

现在，让我们继续讨论 SQL 连接，在 MongoDB 中我们有`$lookup`来实现连接。

# $lookup

*3.2 版本新增。*

对 *same* 数据库中的非共享集合执行左外连接，以从“连接”集合中过滤文档进行处理。

对于每个输入文档，`$lookup`阶段添加一个新的数组字段，其元素是来自“joined”集合的匹配文档。`$lookup`阶段将这些整形后的文档传递到下一阶段。

可以有不同的连接条件，但我们将研究最基本的条件，即相等匹配。

## 平等匹配

在两个集合之间执行不相关的子查询，并允许除单个相等匹配之外的其他连接条件。`[$lookup](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#pipe._S_lookup)`阶段具有以下语法:

```
{
   $lookup:
     {
       from: <collection to join>,
       let: { <var_1>: <expression>, …, <var_n>: <expression> },
       pipeline: [ <pipeline to execute on the collection to join> ],
       as: <output array field>
     }
}**from:** Specifies the collection in the *same* database to perform the join with.**let:** Optional. Specifies variables to use in the pipeline field stages. Use the variable expressions to access the fields from the documents input to the $lookup stage.**pipeline:** Specifies the pipeline to run on the joined collection. The pipeline determines the resulting documents from the joined collection. To return all documents, specify an empty pipeline [].**as**: Specifies the name of the new array field to add to the input documents. The new array field contains the matching documents from the from collection. If the specified name already exists in the input document, the existing field is *overwritten*.
```

让我们看一些例子来更好地理解术语:

## 使用$lookup 执行单个等式连接

使用以下文档创建集合`orders`:

```
db.orders.insert([
   { "_id" : 1, "item" : "almonds", "price" : 12, "quantity" : 2 },
   { "_id" : 2, "item" : "pecans", "price" : 20, "quantity" : 1 },
   { "_id" : 3  }
])
```

使用以下文档创建另一个集合`inventory`:

```
db.inventory.insert([
   { "_id" : 1, "sku" : "almonds", description: "product 1", "instock" : 120 },
   { "_id" : 2, "sku" : "bread", description: "product 2", "instock" : 80 },
   { "_id" : 3, "sku" : "cashews", description: "product 3", "instock" : 60 },
   { "_id" : 4, "sku" : "pecans", description: "product 4", "instock" : 70 },
   { "_id" : 5, "sku": null, description: "Incomplete" },
   { "_id" : 6 }
])
```

下面对`orders`集合的聚合操作使用来自`orders`集合的字段`item`和来自`inventory`集合的字段`sku`将来自`orders`的文档与来自`inventory`集合的文档连接起来:

```
db.orders.aggregate([
   {
     $lookup:
       {
         from: "inventory",
         localField: "item",
         foreignField: "sku",
         as: "inventory_docs"
       }
  }
]);
```

该操作返回以下文档:

```
{
   "_id" : 1,
   "item" : "almonds",
   "price" : 12,
   "quantity" : 2,
   "inventory_docs" : [
      { "_id" : 1, "sku" : "almonds", "description" : "product 1", "instock" : 120 }
   ]
}
{
   "_id" : 2,
   "item" : "pecans",
   "price" : 20,
   "quantity" : 1,
   "inventory_docs" : [
      { "_id" : 4, "sku" : "pecans", "description" : "product 4", "instock" : 70 }
   ]
}
{
   "_id" : 3,
   "inventory_docs" : [
      { "_id" : 5, "sku" : null, "description" : "Incomplete" },
      { "_id" : 6 }
   ]
}
```

# 结论

这只是在 MongoDB 中使用类似 SQL 的查询的基本概述。

使用许多其他可用的[阶段](https://docs.mongodb.com/manual/reference/operator/aggregation-pipeline/)可以做更多的事情。掌握它的最好方法是练习不同的场景，并在项目中使用它们。希望这能有所帮助。

# 资源

[MongoDB 文档](https://docs.mongodb.com/manual/reference/operator/aggregation-pipeline/)
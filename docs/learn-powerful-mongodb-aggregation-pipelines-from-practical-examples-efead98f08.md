# 从实际例子中了解强大的 MongoDB 聚合管道

> 原文：<https://betterprogramming.pub/learn-powerful-mongodb-aggregation-pipelines-from-practical-examples-efead98f08>

## 通过聚合深入了解 MongoDB 中的数据

![](img/64c1ed07485045d5f86f897e086c140f.png)

图片来自 [Pixabay](https://pixabay.com/illustrations/conveyor-valve-pressure-gauge-5438440/)

MongoDB 不仅仅是一个 NoSQL 文档数据库。您可以使用聚合管道对集合中的文档执行复杂的分析。一个常见的任务是对结果进行分组，并获得每组的总数据或平均数据。您甚至可以在分组前过滤、转换或清理数据。

聚合管道由一个或多个按顺序处理文档的阶段组成。每个阶段对输入文档执行一些操作，并将处理过的文档传递给下一个阶段。例如，第一个阶段可以根据一些条件过滤文档，第二个阶段可以对过滤的文档进行分组并进行一些聚合，第三个阶段可以输出结果。

在本文的演示中，我们将使用文章中介绍的数据进行高级 MongoDB 查询。如果您想继续学习，查看该文章中的系统设置步骤会有所帮助，以便更好地了解数据。但是，如果您很着急，您可以下载这个 JSON 文件并使用以下命令导入数据:

在这篇文章中，我们使用 Docker 在本地启动 MongoDB 服务器，但是您可以使用 MongoDB Atlas 这样的托管服务器，它有一个免费层，对于学习来说是一个不错的选择。

当上面的代码运行时，我们将在`products`数据库中拥有一个包含 200 个笔记本电脑数据文档的`laptops`集合。这些文件的内容如下:

现在数据已经准备好了，我们可以开始编写聚合管道来分析数据。建议使用 [MongoDB IDE](https://medium.com/codex/how-to-use-mongodb-with-graphical-ides-420597ede80e) 来编写通常跨越多行的管道。使用 IDE，您可以自动完成代码，并且可以方便地编写跨多行的查询或管道。然而，在这篇文章中，你可以复制代码，在`mongosh`中运行并检查结果。

## `**$group**` **阶段**

让我们首先检查一下我们的集合中有多少文档。

```
$ **docker exec -it mongo-server bash**
$ **mongosh "mongodb://admin:pass@localhost:27017"**test> **use products**
products> **db.laptops.countDocuments**()
200
```

我们还可以使用一个聚合管道来计算文档的数量，这在现在看起来有点过了，但它可以作为以后更复杂管道的介绍:

在我们的第一个管道中，只有一个`$group`阶段，顾名思义，它按照指定的`_id`对文档进行分组。通常我们会为`_id`指定一个字段，根据该字段对文档进行分组。然而，特别是当`_id`是`null`时，这意味着我们将所有的文件分组在一起。此外，`total`是一个新字段，保存由`$count`累加器运算符返回的数据，它只是统计每组中的文档数。

现在让我们按品牌分组，并检查每个品牌的数量:

如上所述，在`$group`阶段，`_id`指定对输入文档进行分组的字段。`_id`接受一个[聚合表达式](https://docs.mongodb.com/manual/meta/aggregation-quick-reference/#std-label-aggregation-expressions)作为其值，可以是[字段路径](https://docs.mongodb.com/manual/meta/aggregation-quick-reference/#std-label-agg-quick-ref-field-paths)、[系统变量](https://docs.mongodb.com/manual/meta/aggregation-quick-reference/#std-label-agg-quick-ref-variables)、[表达式对象](https://docs.mongodb.com/manual/meta/aggregation-quick-reference/#std-label-agg-quick-ref-expression-objects)等。

`**"**$brand"`是字段路径，必须用引号括起来。您可能想知道为什么我们必须在字段名前加一个美元符号`$`。是的，这是你第一次看到它时非常困惑的。您需要理解`_id`接受一个表达式，而不是一个普通的字段名称。并且在表达式中，如果我们要指定一个字段，就必须在它前面加一个美元符号，否则，它会被当作一个文字字符串。

从技术上来说，`"$brand"`是`"$$CURRENT.brand"`的快捷方式，其中`[CURRENT](https://docs.mongodb.com/manual/reference/aggregation-variables/#mongodb-variable-variable.CURRENT)`是默认为当前单据的系统变量。一旦知道了这些，`"$brand"`就没那么神秘了吧？

## `**$sort**` **阶段**

让我们按品牌数量降序排列笔记本电脑:

在`$group`阶段之后添加新的`$sort`阶段。正如我们已经知道的，一个阶段过滤或聚合的文档被传递到下一个阶段。在这种情况下，分组结果被传递到`$sort`阶段。原始文档的字段现在不可用，只有以前`$group`阶段的字段可用。因此，我们可以通过在前面的`$group`阶段生成的`total`字段进行排序。另外，`$sort`接受一个普通的字符串作为字段名，这样就不需要在它前面加上美元符号。

## `**$match**` **舞台**

我们可以添加一个`$match`阶段来过滤输入文档，只将符合指定条件的文档传递给下一个管道阶段。我们把没有现货的笔记本电脑过滤掉，只计算还有现货的。

我们可以在同一个管道中多次使用`$match`阶段。我们只输出库存超过 10 个的品牌:

## `**$unwind**`阶段**阶段**

现在让我们介绍一个更复杂的阶段，`$unwind`，它从输入文档中解构一个数组字段，并为原始数组的每个元素输出一个文档。

对于这样的文档:

```
{"_id": 1, "name": "John", "scores": [70, 90, 80]}
```

`scores`字段上的`$unwind`阶段将生成以下文件:

```
{"_id": 1, "name": "John", "scores": 70}
{"_id": 1, "name": "John", "scores": 90}
{"_id": 1, "name": "John", "scores": 80}
```

由来自相同数组的`$unwind`产生的文档将具有相同的主键`_id`。现在让我们展开`laptops`文档的`attributes`字段，它是一个属性文档数组:

是的，它按预期展开，每个新文档包含原始数组的一个属性。注意，`$unwind`阶段期望一个[字段路径](https://docs.mongodb.com/manual/reference/glossary/#std-term-field-path)作为输入，它应该用类似于`$group`阶段的`_id`字段的美元符号前缀来指定。

现在让我们找出每个品牌笔记本电脑的最大内存:

```
[
  { _id: 'HP', max_memory: '8GB' },
  { _id: 'Dell', max_memory: '8GB' },
  { _id: 'Asus', max_memory: '8GB' },
  { _id: 'Apple', max_memory: '8GB' },
  { _id: 'Lenovo', max_memory: '8GB' }
]
```

操作符获取一个组的最后一个成员。当与按指定字段对文档排序的`$sort`阶段一起使用时，我们可以获得按升序排序的字段的最大值。

有趣的是，所有的品牌都有“8GB”作为最大值。这是因为内存值是一个字符串值，而“8GB”被视为大于“16GB”或“32GB”。我们需要把它转换成一个数值来排序，这可以用`$project`阶段来实现。

## `**$project**` **阶段**

`$project`阶段将带有请求字段的文档传递到管道中的下一个阶段。指定的字段可以是输入文档中的现有字段，也可以是新计算的字段。在本例中，我们希望按原样传递品牌，但是将内存字符串值转换为数值(在本例中为整数)。内存字符串值都包含一个“GB ”,我们需要在它被转换成整数之前移除它，这可以用`[$trim](https://docs.mongodb.com/manual/reference/operator/aggregation/trim/#mongodb-expression-exp.-trim)`操作符来完成。从字符串到整数的转换可以用`[$toInt](https://docs.mongodb.com/manual/reference/operator/aggregation/toInt/#mongodb-expression-exp.-toInt)`操作符来完成。

最终使用的管道如下所示。注意`$sort`载物台也用了两次，就像上面介绍的`$match`载物台一样。

现在，内存已正确排序:

```
[
  { _id: 'HP', max_memory: 32 },
  { _id: 'Lenovo', max_memory: 32 },
  { _id: 'Dell', max_memory: 16 },
  { _id: 'Asus', max_memory: 16 },
  { _id: 'Apple', max_memory: 16 }
]
```

我们可以继续分析不同阶段组合的数据。然而，它将类似于我们所介绍的。一旦您掌握了聚合管道的基本和常用阶段，您将能够执行您想要的各种分析。建议您手动编写一些管道，并检查每个阶段的输出，以便更好地了解不同阶段的逻辑。

[](https://lynn-kwong.medium.com/all-you-need-to-know-about-using-mongodb-in-python-caa077c9a20f) [## 关于在 Python 中使用 MongoDB，您需要知道的是

### 了解 Python 中 MongoDB 的常见用例

lynn-kwong.medium.com](https://lynn-kwong.medium.com/all-you-need-to-know-about-using-mongodb-in-python-caa077c9a20f)
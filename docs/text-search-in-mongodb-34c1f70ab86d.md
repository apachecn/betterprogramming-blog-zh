# 如何在 MongoDB 中进行文本搜索

> 原文：<https://betterprogramming.pub/text-search-in-mongodb-34c1f70ab86d>

## 从来没有这么简单

![](img/66b7ef2b0285edfebf8e94368877b6c4.png)

照片由[安特·哈默斯特](https://unsplash.com/@ante_kante?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在大多数应用程序中，文本搜索是一个非常常见的需求，如果您在字段上创建了一个索引，您会希望大多数数据库都支持现成的文本搜索。

但是当我试图为我的应用程序实现文本搜索时，结果却复杂得多。经过一些研究，我发现了用 [MongoDB](https://www.mongodb.com/) 实现文本搜索的三种主要方法。

# 1.创建文本索引

如果你在谷歌上搜索“mongo 全文搜索”，这是你会发现的第一种方法根据 [MongoDB 的文档](https://docs.mongodb.com/manual/text-search/)，这是实现文本搜索最有效的方式。例如，考虑以下数据:

现在创建索引，因为索引将使它发生！

```
> db.names.createIndex({ name: "text" })
```

现在尝试以下查询:

正如您所看到的，如果您搜索`Army`，它会在 names 列中显示所有包含单词`Army`或该单词任何已知变体的文档。但这对`Arm`不起作用。

因此，当我们搜索`Army`时，我们的文本搜索足够智能来匹配`Armies`，但不会笨到部分匹配`Arm`和`Army`或`Armies`。

我的产品经理希望(或者说期待)如果我搜索`Arm`，它会显示我搜索`Army`时出现的三个结果。

为了解决这个问题，我认为在我搜索`Arm`而不是`Army`时，了解为什么两个文档不匹配是个好主意。

正如我所怀疑的，令牌化！文本索引通过空格将数据(在本例中是`Army Ants`和`Army of Ants`)分成标记。于是`Army Ants`变成了`[Army, Ants]`，`Army of Ants`变成了`[Army, of, Ants]`。并且当您搜索`Army`时，该单词与两个文档中的一个标记相匹配，这就是当您搜索`Army`时在结果中看到两个文档的原因。

*注意:我在这里过于简单化了。标记化的实际过程包括更多的内容(例如，去除无关紧要的单词，如* `*of*` *)。*

因此，似乎用文本搜索来满足我们的总理的“希望”几乎是不可能的。您可能认为我们必须进入自动完成的世界。

# 2.使用简单但功能强大的旧正则表达式

正则表达式在大多数数据库中是非常低效的，因为它们为我们提供了更加灵活的搜索方式，而技术总是需要权衡。我们用效率换取搜索的灵活性。

瞧啊。然而，这并不是一幅美丽的幕后画面。让我们戴上眼镜。

看到那个躲在窗帘后面的`COLLSCAN` 了吗？

让我们看看创建一个索引是否会让这一切变得更好。

嗯，`IXSCAN`乍一看更好看。但是如果你够古怪，而且你的直觉不允许你认为事情会这么简单，那你就有得吃了。我们来看看`executionStats`。

看`totalKeysExamined`的值？就像索引中的所有键都被扫描了一样。让我们再添加几行，看看这个值是否成比例增加。

`totalKeysExamined` 随着行数的增加而增加。所以很明显，我不能将它部署到生产环境中——即使它可以工作。整个索引扫描不好！

环顾了一下四周之后:

所以这对于生产来说已经足够了！我们通过牺牲灵活性来限制扫描的行数(效率)。我们现在只能从名字开始匹配。

但是任务还没有完成，不是吗？

所以这不是匹配的`Army of Ants`或`Army Ants`，首相真的希望我也能做到这一点。

我研究了一段时间，但没有找到任何可以让我开箱即用的东西。

# 3.黑掉它

很明显，是时候进行一些创新思维了。

似乎我们的问题可以解决，如果我们可以用空格将名字分解，然后在每个分解的单词上使用带有`^`的正则表达式。因此，如果我们可以将`Army of Ants`分解成`[Army, of, Ants]`，然后在所有分解的单词中搜索`^Ant`，您将匹配`Army of Ants`。

但是我们如何利用数据库来完成这些任务呢？MongoDB 会比我们写的任何代码都更有效率。

数组字段来拯救。如果我们只是用空格将名字分开，将它们转换成一个列表(`[Army, of, Ants]`)，并将其存储在一个数组字段中，我们就可以对每个名字进行`^`正则表达式匹配。如果我们在这个字段上创建一个索引( [multikey index](https://docs.mongodb.com/manual/core/index-multikey/) )，Mongo 会处理它。

让我们看看它在新系列中的表现。

好吧，现在有用了。让我们检查一下我们是否仍然有效率。

所以，很有效率。我们使用的是索引扫描，而不是扫描整个索引。

# 结论

仅此而已。如果使用不当，数据库既复杂又低效。每个数据库都有一些限制，每当我们尝试新的东西时，我们必须确保它是有效的。否则，你只是在为自己将来的失败做准备。

还记得我指出技术都是关于权衡的吗？你能看出最后一种方法的利弊吗？给我留言评论！

# 进一步阅读

[](https://scalegrid.io/blog/mongodb-regular-expressions-indexes-performance/) [## MongoDB 正则表达式，索引和性能

### MongoDB 支持使用$regex 操作符的正则表达式。然而，这些 MongoDB 正则表达式查询有一个缺点…

scalegrid.io](https://scalegrid.io/blog/mongodb-regular-expressions-indexes-performance/) [](https://docs.mongodb.com/manual/reference/method/db.collection.explain/index.html#explain-method-verbosity) [## db . collection . explain()-MongoDB 手册

### 的行为和返回的信息量取决于模式。queryPlanner 模式(默认)执行启动…

docs.mongodb.com](https://docs.mongodb.com/manual/reference/method/db.collection.explain/index.html#explain-method-verbosity) [](https://docs.mongodb.com/manual/core/index-text/) [## 文本索引- MongoDB 手册

### MongoDB Atlas Search Atlas Search 使在您的基础上构建快速、基于相关性的搜索功能变得非常容易

docs.mongodb.com](https://docs.mongodb.com/manual/core/index-text/)
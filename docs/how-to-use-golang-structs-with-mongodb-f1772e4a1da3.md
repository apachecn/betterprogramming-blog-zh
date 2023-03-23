# 如何在 MongoDB 中使用 Golang 结构

> 原文：<https://betterprogramming.pub/how-to-use-golang-structs-with-mongodb-f1772e4a1da3>

## 在这个简短的例子中，我将向您展示如何在 Go 语言中创建用于 MongoDB 的结构

![](img/df7b424e09170e9a2e235a1cbf88c4dc.png)

Ana Abad 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了演示 Golang 结构在 MongoDB 中的使用，我创建了一个简单的电子邮件自动回复器作为示例。我们想给一些联系人发电子邮件。此外，我们还会在特定时间发送电子邮件，注明主题和内容。最后，我们还有一个将联系人与电子邮件联系起来的序列。

通过使用 Golang 结构，我们最大限度地减少了代码中 BSON 的使用，提高了数据库查询结果的可用性。这篇文章可以看作是上一篇文章[“如何在 MongoDB 中使用 Go”的延续。](/how-to-use-golang-with-mongodb-26f043d31d23)

在接下来的内容中，我会一段一段地检查一个小演示的代码。

# 导入相关包

首先，我们将导入必要的 Golang 包。

我们需要导入这些库来完成以下任务:

*   `context`是[为 MongoDB 操作创建上下文](https://www.mongodb.com/docs/drivers/go/current/fundamentals/context/)所必需的。
*   `bson`用于序列化发送到 MongoDB 服务器的数据。
*   `mongo`处理与 MongoDB 服务器的连接，并设置数据库和集合。

# 创建结构

这里的想法是使用 Golang 结构，而不是像我们在上一篇文章中所做的那样使用`bson`序列化。这些结构需要使用`bson`注释才能正常工作。

在上面这段代码中，我们可以看到各种`bson`注释。

这些注释都遵循相同的模式:

> `bson:"<fieldname>,omitempty"`其中`<fieldname>`是数据库中的实际字段名，而`omitempty`表示如果没有给出值，该字段将被省略。

请注意，注释也在向下重音符号之间**，并且没有**没有空格**。**

除了使用`omitempty`，还可以使用其他 struct 标签(或者根本不使用)。点击阅读更多关于[的内容。](https://www.mongodb.com/docs/drivers/go/current/fundamentals/bson/)

在第 2、9 和 16 行，我们将`ID`字段的类型设置为`primitive.ObjectID`。通过这样做，我们告诉 BSON 这些字段将是实际的 MongoDB `ObjectIDs`。

此外，`_id`数据库字段将总是被填充，即使我们在定义这些结构的实例时会省略它们(稍后会有更多的介绍)。MongoDB 服务器将填充这些字段，并赋予它们惟一的`ObjectIDs`。我们将使用这些`ObjectIDs`来处理集合中的单个文档。

在第 5 行，我们创建了一个字符串片段。这个切片会自动转换成一个`bson.A` (BSON 数组)。

在第 10 行，您可以看到也可以使用一个字段`time.Time`。

在第 17–18 行，我们将引用数据库中的其他文档。这些参考将是`ObjectIDs`的列表。在这里，我向你展示两种方法。

您可以使用`[]interface{}`，这是最通用的方法。危险在于您可以在这里放入任何值—包括整数、字符串等。

另一种可能是使用`[]primitive.ObjectID`。这是声明类型的一种特定方式。现在只接受`ObjectIDs`。这种方法的问题是，我们需要在以后编写更多的代码。

# MongoDB 连接和数据库设置

在`main()`函数中要做的第一件事是连接到 MongoDB 服务器，并用包含文档的集合创建数据库。

在第 1 行和第 2 行，我们使用`NewClient()`和正确的 URI 为 MongoDB 创建了一个新的客户端。注意，我的 MongoDB 运行在端口为`27017`的`localhost`上。没有设置用户名或密码，因为这是我的测试服务器。

在第 7 行，我们使用`context.TODO()`创建了一个上下文。这是可能的最基本的上下文。

在第 9 行，我们让客户机用给定的上下文连接到 MongoDB 服务器。

在第 14 行，我们`defer`从 MongoDB 服务器断开连接。假设`Disconnect()`函数被延迟，它将在`main()`函数中的所有其他语句运行完之后执行。

在第 16 行，我们创建了一个名为`autoresponder`的数据库。

在第 17–19 行，我们在这个数据库中进行了三次收集。这些集合分别被称为`contacts`、`emails`和`sequences`。

在第 21–23 行，我们`defer`删除数据库集合。这只针对我们的例子，因为我不希望每次运行代码和测试新东西时数据库都被填满。如果您希望将数据保留在数据库中，请删除这些行。

# 数据库插入

## 将联系人插入数据库

让我们创建一些模拟触点并将它们插入到`contactsCollection`中。

在第 1–17 行，我们创建了三个联系人(MongoDB 术语中的“文档”)，创建方式与创建常规结构相同。注意这与我们在这里使用的(向下滚动页面一半)使用 BSON 有什么不同。

在第 19 行，我们使用`InsertMany()`将这些联系人插入到`contact`集合中，因为我们想要一次插入多个文档。

在第 24 行，我们使用第 19 行的结果`insertResult` 和属性`InsertedIDs`获得插入的联系人的 id。

在第 26 行，我们创建了一个名为`contactIDs_`的`[]primitive.ObjectID`类型的切片。

在第 17–29 行，我们循环返回的`contactIDs`，将每个元素转换为`primitive.ObjectID`，并将其附加到`contactIDs_`。显然，从`[]interface{}`到`[]primitive.ObjectID`的类型转换无法完成；这就是我们使用`for`循环的原因。

请注意，我们没有自己定义 id。当插入文档时，MongoDB 自动分配 id。这些 id 保存在实际数据库的`_id`字段中，也可以通过使用`ID`属性在结构中访问。

在第 31 行，我们打印出`contactIDs_`切片来证明我们插入了三个触点，并且类型是正确的。

我们也可以使用 [MongoDB Compass](https://www.mongodb.com/products/compass) 来检查这一点。

## 将电子邮件插入数据库

现在，让我们也将一些电子邮件插入到`emailsCollection`中。

这与触点的插入非常相似。

这里要注意的是，在第 3、7 和 11 行中，我们使用`time.Now()`作为变量值。

在第 21 行，我们从第 16 行的`InsertMany()`操作的返回结果中检索`emailIDs`。

## 将序列插入数据库

最后，让我们也在`sequencesCollection`中插入一个序列。

在第 1 行，我们使用`Sequence`结构创建了一个序列。在这个结构中，我们放置了之前从插入结果中获取的`emailIDs`和`contactIDS_`。

在第 3 行中，我们使用`InsertOne()`而不是之前使用的`InsertMany()`。

# 数据库查询

## 查找特定联系人

在下面的代码中，我演示了如何使用自定义过滤器查找联系人。

在第 2 行，我们使用`Find()`函数来查找满足过滤器`bson.M{"tags": "Customer"}`的所有`contacts`。通过这个过滤器，我们将找到标签列表中包含`"Customer"`的所有联系人。

当然，您可以根据自己的需要调整这个过滤器。如果要查找所有联系人，可以使用`bson.M{}`。或者，如果您希望找到电子邮件地址为`mm@example.com`的联系人，您可以写`bson.M{"email":"mm@example.com"}`。

此外，您可以使用`bson.D`而不是使用`bson.M`格式进行过滤。更多关于这个[这里](https://stackoverflow.com/questions/64281675/bson-d-vs-bson-m-for-find-queries)。

在第 7 行，使用`All()`将查询结果加载到名为`contacts`的`[]Contact`切片中。

在第 11–15 行，我们简单地打印出了`Contact`结构的一些属性，就像我们对任何结构所做的那样。

这样，我们可以最大限度地利用 Golang 结构，并减少查询中过滤器的 BSON 使用。

## 找到序列并检索联系人和电子邮件

下面，我们从`sequencesCollection`开始恢复序列。我们从这个序列中得到电子邮件 id 和接收者 id。这些 id 随后用于检索相应的电子邮件和联系人。

在第 10–13 行，我们使用了`[0]`,因为只返回了一个序列。

在第 17 行和第 23 行，我们使用`FindOne()`和`bson.M{"_id": <id>}`来查找和过滤电子邮件和联系人，只找到一个基于其`ObjectID`(在`<id>`中)的特定文档。由于只有一个结果，我们也可以使用`Decode()`而不是`All()`将结果分别提取到`Email`和`Contact` struct 中。

# 完整的代码

下面你可以找到完整的代码。确保在执行代码之前有一个 MongoDB 服务器在`mongodb://localhost:27017`上运行——或者更改 URI。

# 参考

[MongoDB](https://www.mongodb.com/blog/post/quick-start-golang--mongodb--modeling-documents-with-go-data-structures)的“快速入门:Golang & MongoDB —用 Go 数据结构建模文档”

[“与 BSON 合作”，MongoDB 文档](https://www.mongodb.com/docs/drivers/go/current/fundamentals/bson/)

[“bson。d 对 bson。m 用于查找查询”](https://stackoverflow.com/questions/64281675/bson-d-vs-bson-m-for-find-queries)
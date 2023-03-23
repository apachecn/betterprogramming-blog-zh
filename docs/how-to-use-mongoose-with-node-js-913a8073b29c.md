# 如何在 Node.js 中使用 Mongoose

> 原文：<https://betterprogramming.pub/how-to-use-mongoose-with-node-js-913a8073b29c>

## 向您的 MongoDB 应用程序添加更多传统的关系数据库功能

![](img/3bebfb18203e12179c88a37e04c64785.png)

[桑德罗·卡塔琳娜](https://unsplash.com/@sandrokatalina?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/techno?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

您终于学会了如何在您的节点中使用 [MongoDB。Js](https://medium.com/swlh/use-mongodb-with-node-js-14781ec11c0a) 应用程序为您的图书馆后端。现在，您当地的超市老板听说了您所做的出色工作，希望您也能为他做同样的事情。

然而，有一些警告。他希望他的数据记录有一个结构，他希望在应用程序内置验证。你做了一些研究，发现一个叫做 Mongoose 的工具可以满足他的需求。

# 简介:猫鼬是什么？

Mongoose 是位于节点之上的对象文档建模(ODM)层。Js MongoDB API。如果您来自 SQL 背景，那么 Mongoose 类似于 ORM(对象关系映射)。

在 MongoDB 原生 API 上使用 Mongoose 并不是强制性的。然而，这样做也有一些好处。

## 为什么选择 Mongoose 而不是 MongoDB？

*   **模式**:还记得我说过超市老板的数据记录需要结构化吗？这种结构被称为模式。这些将在本文后面更详细地讨论。
*   **内置验证**:这意味着您不必编写额外的代码，而这些代码是您必须用 MongoDB 驱动程序编写的。通过简单地在模式定义中包含类似于`required:true`的东西，Mongoose 为您的集合(包括数据类型)提供了内部验证。
*   **实例方法:**您可以用最少的代码在文档上定义定制方法。虽然在 MongoDB 中也可以做到这一点，但是 Mongoose 使得在模式定义中创建和组织这样的方法更加容易。反过来，这意味着您在 MongoDB 中有更多的样板代码。

1.  **返回结果**:在 Mongoose 中，返回查询生成的文档一般比较容易。一个例子是`update`查询。在 MongoDB 中，这个查询只返回一个带有成功标志和修改的文档数的对象。另一方面，Mongoose 为您提供更新的文档本身，因此您可以轻松地处理结果。

# 通用术语

*   **模式**:mongose 中的一切都以模式开始。每个模式映射到一个 MongoDB 集合，并定义该集合中文档的形状。它有关于文档的属性/字段类型的信息。模式还可以存储关于验证和默认值的信息，以及特定属性是否是必需的。换句话说，它们是文件的蓝图。
*   **模型**:模型是我们用来构造文档的类。

还有其他的术语**、**在我之前的[文章](https://medium.com/better-programming/use-mongodb-with-node-js-14781ec11c0a)中已经深入讨论过了。然而，总而言之:

*   **文档**:简单的数据记录。
*   **集合**:一套文档。

# 入门指南

只需通过 npm 安装`mongoose`模块。

```
npm install mongoose
```

如需 npm 指南，请点击[此处](https://medium.com/@hussainarifkl/the-basics-of-npm-a32ee1d79901)。

# 初始化我们的数据库

现在机器上已经安装了 Mongoose，我们将使用`mongoose.connect`连接到我们的数据库，就像这样:

```
const mongoose = require('mongoose')
**mongoose.connect('mongodb://localhost/test', {useNewUrlParser: true});**
```

在这里，我们连接到一个名为`test`的数据库。第一个参数是`URI`，第二个参数是`options`。

网址的格式是`type://username:password@host:port/database_name`。

# 定义模式

让我们创建一个模式，`BookSchema`，它将有一个类型为`String`的属性`name`。

```
const Schema = mongoose.Schema
const BookSchema = new Schema({
name : String
})
```

这意味着通过这个`BookSchema`定义的文档将有一个字段，这是一个类型为`String`的`name`。

# 定义模型

使用我们刚刚创建的`BookSchema`，让我们创建模型`Books`。

```
const Model = mongoose.model
const Book = Model('Books',BookSchema)
```

`mongoose.model`的第一个参数是我们集合的名称。第二个参数是模型将使用的模式。

在这种情况下，我们的集合的名称是`Books`，它将使用`BookSchema`的模式。

# 创建/插入文档

现在已经完成了，让我们在数据库中创建一本书，名为`NodeJS : A Guide`。

```
const NodeJsGuide = new Book({name : 'NodeJS : A Guide' })
```

剩下的就是用`book.save`保存了，像这样:

下面是代码的输出:

![](img/c4527db72b5ac2e4a303d1cfaee1ffd2.png)

运行 save()时的代码输出

如果您也一直在使用 MongoDB，请注意创建文档所需的代码相对较少。

# 查找/阅读文档

## 阅读所有文档

为了读取所有文档，我们使用了`model.find`方法，而没有指定查询。这给出了集合中所有可用文档的数组。

```
model.find((err,document)=> {
console.log(document)
})
```

我们的例子是这样的:

这是应用的输出:

![](img/12796199926e46073fb59d5a0a6dd950.png)

返回的文档数组

## 读取查询的文档

然而，为了使用查询，我们在`model.find`方法中指定了`query`参数:

```
model.find(query,(err,document)=> {
console.log(document)
})
```

为了说明这个例子，我在数据库中添加了一本名为`Ali & Hussain: Best Friends`的书。让我们运行一个查询并输出结果:

![](img/a023acf136b143811ca74f3df52120d3.png)

查询后返回的结果。

# 更新文档

更新文档的方法有多种，这里讨论[。最常见的是`model.updateOne`。顾名思义，我们将更新匹配该查询的第一个文档。](https://mongoosejs.com/docs/api.html#model_Model.updateOne)

该方法定义如下:

```
model.updateOne(query,fieldsToUpdate,(err,result)=>{
//code
})
```

这个回调中的`result`属性将提供:

*   匹配此查询的文档数(`n`)
*   修改的文档数。(`nModified`)
*   手术是否成功。(`ok`)

在本例中，让我们将名为`Ali & Hussain: Best Friends`的书更新为`Maarij And Hussain: Best Friends`。

输出如下所示:

![](img/ad3b8e7bc88f8da2c773c2423100d675.png)

最后，让我们使用`find`方法来遍历我们的文档。这是输出:

![](img/f2817ae24116cf692d429d31c9c978c5.png)

运行 find()方法时的输出

# 删除文档

要删除文档，我们将使用`model.deleteOne`或`model.deleteMany`。两者具有相似的参数。

```
model.deleteOne(query,(err,result)=>{
//code
})
```

回调中的`result`参数为我们提供了一个对象，它告诉我们:

*   删除了多少文档，(`deletedCount`)
*   有多少文档与查询匹配，(`n`)
*   以及操作是否成功(`ok`)

在本例中，我们将删除一个匹配名称`NodeJS : A Guide`的文档。

这样做的结果将如下:

![](img/5629f82b06f86c22634b78c0b56b47a2.png)

updateOne 运行时的输出

使用`find`方法迭代结果，最终得到以下结果:

![](img/0e8275e23d78d3f3aca41e5dc0c2aea3.png)

运行 find()时的输出

# 自定义模式方法

这些方法被附加到模式中。它们的类型有:

## 实例方法

从各自的模式派生的文档可以使用这些方法。

作为一个例子，让我们更新我们的`BookSchema`来创建一个为我们提供书名和作者的方法。

这是代码的输出:

![](img/48ad993a91fc27e28c75a501d416a429.png)

运行上述代码时的输出。

*记住:为了在函数中保留 Mongoose 的* `*this*` *的含义，您需要为静态函数使用普通的函数声明，而不是使用 arrow 语法。*

## 静态方法

每当我们没有特定的对象或者我们不需要它的时候，就会用到这些。从它们各自的模式中派生出来的模型可以使用这些方法。

例如，让我们创建一个函数，通过查询书名在数据库中搜索书籍。这里，我们假设书已经被保存了。

输出如下所示:

![](img/330f30e43662e7c94e106f0bf813c316.png)

代码运行时的输出。

## 钩住

这些也称为中间件功能。它们是在异步函数执行时被传递控制的函数。

一些常见的中间件功能包括:

*   `[validate](https://mongoosejs.com/docs/api/document.html#document_Document-validate)`
*   `[save](https://mongoosejs.com/docs/api/model.html#model_Model-save)`
*   `[remove](https://mongoosejs.com/docs/api/model.html#model_Model-remove)`
*   `[updateOne](https://mongoosejs.com/docs/api/document.html#document_Document-updateOne)`
*   `[deleteOne](https://mongoosejs.com/docs/api/model.html#model_Model-deleteOne)`

在这个例子中，我们将使用一个**预挂钩**函数——即它将在函数执行之前执行。在这里，我们将检查是否存在同名的图书—如果存在，将不会保存该图书。假设名为`Maarij And Ali: Best Friends`的书已经被保存，我们将再次保存它。

使用中间件功能的完整列表可以在[这里](https://mongoosejs.com/docs/middleware.html)找到。

输出如下所示:

![](img/fc9fd03640bad6ca13386465417be202.png)

代码运行时的输出

# 虚拟字段

这些属性不需要存储在数据库中。它们通常用于文档中的计算属性。例如，您不需要数据库中的`full name`字段——您可以将数据库中的`lastname`和`first name`组合起来。

虚拟字段有`get`和`set`方法。

在这个例子中，我们将连接`author`字段和`name`字段，然后使用`get`方法返回它们。

这段代码的输出如下:

![](img/ff5d73d47c33e0235b5896bd138719d2.png)

虚拟字段的输出-示例

# 外部资源和阅读

我只是触及了 MongooseJS 的皮毛。最好你多读读这个 API。以下是可用的最佳资源:

[](https://code.tutsplus.com/articles/an-introduction-to-mongoose-for-mongodb-and-nodejs--cms-29527) [## Mongoose for MongoDB 和 Node.js 简介

### Mongoose 是一个 JavaScript 框架，通常用在带有 MongoDB 数据库的 Node.js 应用程序中。在这个…

code.tutsplus.com](https://code.tutsplus.com/articles/an-introduction-to-mongoose-for-mongodb-and-nodejs--cms-29527)  [## mongose v 5 . 9 . 11:入门

### 首先确保您已经安装了 MongoDB 和 Node.js。接下来从命令行使用 npm: $ npm 安装 Mongoose

mongoosejs.com](https://mongoosejs.com/docs/index.html) 

# 概述

## 发现

```
model.find(query?,(err,document)=> {
//code
})
```

## 创造

```
document.save((err,document)=>{
//code
})
```

## 更新

```
model.updateOne(query,fieldsToUpdate,(err,result)=>{
//code
})
```

## 删除

```
model.deleteOne(query,(err,result)=>{
//code
})
```

## 实例方法

```
schema.methods.functionName = function() {
//code
})
document.functionName()
```

## 静力学方法

```
schema.statics.functionName = function() {
//code
})
model.functionName()
```

## 预挂钩功能

```
schema.pre('functionName' , function() {
//code
})
document.functionName()
```

## 虚拟字段

```
schema.virtual('fieldName')
       .get() {
        //code
     }.set(value){
       //code
     })
document.fieldName = 'value'
document.fieldName; //returns value
```

在[下一个教程](https://medium.com/better-programming/simple-notes-app-with-node-js-and-mongoosejs-6595cd5d15b)中，我们会用到所有关于 Mongoose 和上一个 Node 的知识。Js 教程来构建一个简单的 Notes 应用程序。

乍一看，猫鼬似乎是一个棘手的话题。然而，如果您尝试本文中的示例，就很容易理解。记住，你只能在实践中学习，不要放弃！

谢谢你坚持到最后。祝你愉快。希望你学到了很多！
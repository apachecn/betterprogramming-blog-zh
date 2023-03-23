# 如何在 JavaScript 中使用 MongoDB

> 原文：<https://betterprogramming.pub/how-to-use-mongodb-with-javascript-a941335674b5>

## 设置一个基本服务器，连接到它，并使用您的第一个文档

![](img/1dfa66839552993615000bba4cb0750d.png)

Paul Frenzel 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

要设置 MongoDB，我们只需要下载[安装程序](https://www.mongodb.com/download-center/community)并执行它。如果你在安装 MongoDB 上需要任何帮助，我只能推荐访问关于在 Windows 上安装 MongoDB 的[官方文档](https://docs.mongodb.com/manual/installation/)或者这个[由](https://medium.com/@LondonAppBrewery/how-to-download-install-mongodb-on-windows-4ee4b3493514)[伦敦 App Brewery](https://medium.com/u/6528ee62bc63) 撰写的博文。

安装后，我们可以看看如何在我们的项目和基本的 CRUD 功能中使用它。

# 基本服务器

在我们开始研究如何在我们的应用程序中实现 CRUD 功能之前，我们首先需要创建一个基本的 MongoDB 服务器。

为此，我们首先需要在项目中安装 MongoDB 依赖项，可以使用 npm 包管理器来完成:

```
npm install mongodb --save
```

# 正在连接到 MongoDB

首先，我们需要请求我们刚刚安装的`mongodb` 包，并从中获取`MongoClient` 对象:

```
const mongo = require('mongodb').MongoClient
```

之后，我们需要创建一个到 MongoDB 服务器的 URL。如果像我在本文中所做的那样在本地使用它，URL 将类似于 mongodb://localhost:27017:

```
const url = 'mongodb://localhost:27017'
```

我们还需要定义数据库的名称:

```
const dbName = 'exampleproject'
```

然后我们需要使用`mongo.connect()`函数来获取对我们的 MongoDB 客户端的引用:

```
mongo.connect(url, (err, client) => {  if (err) {   console.error(err)   return }  console.log('Connected successfully to server')  const db = client.db(dbName) })
```

现在我们已经有了基本的结构，让我们看看如何在我们的应用程序中实现 CRUD 功能。

# CRUD 功能

现在让我们看看如何实现基本的 CRUD(创建、读取、更新、删除)功能。

为此，我们首先需要创建或获取我们将在其中工作的数据库的集合。我们可以使用将集合名称作为参数的`collection()`函数来实现:

```
const collection = db.collection('collectionname')
```

## 创造

我们可以使用 MongoDB 提供的`insertOne()`函数将一个对象插入我们的数据库:

```
collection.insertOne({ name: 'Test', age: '30' }, ((error, item) => { if(error) { console.error(error) return } console.log(item) }))
```

我们还可以使用`insertMany()`功能插入多个项目:

```
collection.insertMany([{name: 'Test', age: '30'}, {name: 'Test2', age: '60'}], (error, result) => { })
```

## 阅读

`find()`函数用于获取集合中的所有文档:

```
collection.find().toArray((error, items) => { console.log(items) })
```

我们还可以向`find()`函数传递一个参数，以便只获取特定的文档:

```
collection.find({name: 'Gabriel'}).toArray((error, items) => { console.log(items) })
```

如果我们只想获得一个元素，我们可以使用`findOne()`跳过`toArray`函数:

```
collection.findOne({name: 'Gabriel'}, (error, item) => { console.log(item) })
```

## 更新

接下来，让我们看看如何使用`updateOne()`函数更新一个已经存在的文档:

```
collection.updateOne({name: 'Gabriel'}, {'$set': {'name': 'Jeff'}}, (error, item) => { console.log(item) })
```

我们也可以使用`updateMany()`功能更新多个文档:

```
collection.updateMany({ name: 'Gabriel' },{'$set': {'name': 'Jeff'} }, (error, item) => { console.log(item) })
```

## 删除

现在让我们看看如何删除数据库中的文档:

```
collection.deleteMany({name: 'Gabriel'}, (error, item) => { console.log(item) })
```

如果您只想删除一份文件，请使用`deleteOne()`功能:

```
collection.deleteOne({name: 'Gabriel'}, (error, item) => { console.log(item) })
```

# 模式设计

模式设计是 MongoDB 非常重要的一部分，可以帮助我们开发人员构建更健壮、更快速的应用程序。

使用模式设计的常见好处包括:

*   对数据库的写入更少
*   更简单、更快速的查询
*   预先知道文档结构时的设计简化
*   减少未来技术债务

# 如何设计模式

在我们开始研究不同的模式之前，我们需要问自己一些关于数据和数据结构的问题。这些是最重要的:

*   您的数据访问模式
*   读取次数与更新次数
*   文档的大小
*   数据和索引大小
*   可量测性

如果你想了解更多关于 Mongo schema 模式的知识，可以看看 MongoDB 团队的这个[官方视频](https://resources.mongodb.com/getting-started-with-mongodb/back-to-basics-schema-design-basics)。

现在我们已经知道了 MongoDB 模式设计的重要部分，让我们看看如何在 Node.js 应用程序中实现模式。

# 猫鼬

除了 Mongo 本身之外，另一个经常使用的出色特性是 Mongoose，它提供了一个简单的、基于模式的解决方案来为应用程序数据建模，并使验证和转换更加容易。

## 安装猫鼬

Mongoose 可以使用 npm 或 yarn package manager 进行全局安装，也可以只用于一个项目:

```
npm install mongoose --save
```

## 正在连接到 MongoDB

在项目中安装了`mongoose`依赖项之后，您可以通过实例化一个到 MongoDB 数据库的连接来继续:

```
const mongoose = require('mongoose'); mongoose.connect('mongodb://localhost/test', {useNewUrlParser: true});
```

您还可以添加一些基本的日志记录，以便知道您的连接是否正确建立，或者发生了什么错误:

```
mongoose .connect( `mongodb://localhost/test`, { useNewUrlParser: true } ) .then(() => console.log('MongoDB Connected')) .catch(err => console.log(err))
```

## 创建模式

Mongoose 现在使我们能够为我们的数据库对象创建一个预定义的模式，就像您对传统 SQL 数据库所做的那样。模式映射到 MongoDB 集合，并定义该集合中文档的形状。

```
const userSchema = mongoose.Schema({ email: { type: String, require: true, unique: true }, password: { type: String, require: true }, })
```

我们的`mongoose.Schema()`中的每个键定义了文档中的一个属性，该属性将被转换为我们定义的相关联的`SchemaType`。

你可以看一下官方文档来更详细地了解模式。

## 创建模型

现在我们已经有了一个完整的模式，我们可以继续将它编译成一个模型:

```
const user = mongoose.model('User', userSchema)
```

模型是用于构造特定文档的类，您将在下一步中看到。

## 保存项目

我们的模型现在允许我们创建一个可以在数据库操作中使用的特定文档:

```
const user = new User({ email: "test@test.com", password: "password123", })
```

创建文档后，我们可以使用`save()`功能保存它:

```
user.save().then(() => { console.log("Successfully saved document) }).catch((error) => { console.log(error) })
```

## 查找项目

查找商品也是一个非常简单的过程，只需一个命令就可以完成。

`find()`方法在给定的集合中查找与您传递给它的查询相匹配的所有对象:

```
// Request with no query const users = await user.find() //Request with a specific query const specificUser = await user.find({email: "test@test.com"})
```

如果你只想找一个单品，我推荐你用`findOne()`法。

# 蒙古地图集

MongoDB Atlas 是一个全球云数据库服务，允许您在 AWS、Azure 或 GCP 的集群中部署和管理 MongoDB 应用程序。

## 为什么应该使用 Atlas

以下是您应该使用 Atlas 的原因以及它在工作流程中可以为您提供帮助的地方:

*   快速简单——Atlas 不需要任何安装或配置文件。只需注册，创建您的集群，您就万事俱备了。
*   云数据库——不需要在您的机器上安装 MongoDB。在像 Kubernetes 这样的容器集群中运行应用程序时，云也有所帮助。
*   保护敏感数据——Atlas 为您的所有数据提供内置安全控制。
*   高达 512MB 存储空间的绝佳免费层。

在本文中，我将不再深入讨论 MongoDB Atlas。如果你想学习如何设置一个集群并把你的应用程序连接到它，我推荐你使用[官方文档](https://www.mongodb.com/blog/post/quick-start-getting-your-free-mongodb-atlas-cluster)或者[Lenmor Ld](https://dev.to/lenmorld/rest-api-with-mongodb-atlas-cloud-node-and-express-in-10-minutes-2ii1)的本指南。

# 结论

你一路走到了最后！我希望这篇文章能帮助您理解 MongoDB 的基础知识，以及如何在 JavaScript 中使用它。

如果你有任何问题或反馈，请在评论区告诉我。
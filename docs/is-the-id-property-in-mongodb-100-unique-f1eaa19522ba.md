# MongoDB 中的 _id 属性是 100%唯一的吗？

> 原文：<https://betterprogramming.pub/is-the-id-property-in-mongodb-100-unique-f1eaa19522ba>

## MongoDB 对象 id 背后的科学

![](img/15e830f8814814e94b2621d7416dd2dc.png)

照片由[毛里西奥·马斯卡罗](https://www.pexels.com/@maumascaro?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[佩克斯](https://www.pexels.com/photo/person-holding-magnifying-glass-712786/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

MongoDB 是一个 NoSQL 数据库，处理集合和文档。在 MongoDB 上创建的每个文档都有一个惟一的对象 ID 属性。因此，当创建一个没有输入 ID 的文档时，该文档将使用自动生成的 ID 来创建。

# 谁生成 ID？

当填写文档的属性时，我们不一定需要输入对象 ID。但是当我们在创建文档后引用 MongoDB 时，它会有一个如下所示的对象 ID:

```
{ 
   “_id”: “5f1819229fdf8a0c7c2d8c36”
}
```

这使得我们在 MongoDB 中创建文档变得更加容易，并节省了我们大量的时间。MongoDB 中文档中的对象 ID 是由 MongoDB 驱动程序创建的，它与 MongoDB 对话。因此，这带来了很多好处:

*   您不需要等待 MongoDB 创建新的惟一标识符。
*   MongoDB 的应用程序是高度可伸缩的。
*   您可以创建几个 MongoDB 实例。
*   不需要与中心位置交谈来获得唯一的标识符。

# 对象 ID 的属性

一个对象 ID 有 24 个字符长，其中两个字符占用一个字节，因此总共包含 12 个字节。这是 12 个字节的对象 ID 告诉我们的内容。

## 时间戳

对象 ID 的前四个字节表示文档创建时的时间戳。我们不需要在文档中创建一个单独的属性，比如`created-at`，这样可以节省时间并优化代码行。

由于时间戳对对象 ID 的贡献，我们可以通过专门引用 ID 来获得文档的创建时间。因此，在查询数据时，我们不需要不同的方法来根据时间戳对文档进行排序。

您可以使用以下命令获取对象的时间戳:

```
const mongoose = require('mongoose');// Create object Id on memory
const id = new mongoose.Types.ObjectId();// Get timestamp
console.log(id.getTimestamp());
```

## 机器标识符

接下来的三个字节代表机器标识符(即创建文档的机器)。假设在不同的机器上同时创建了两个文档。这三个字节是不同的，增加了对象 ID 的唯一性。

## 进程标识符

接下来的两个字节表示进程标识符(即在其上创建文档的机器中的进程)。假设在同一台机器上用不同的过程同时创建了两个文档。这两个字节是不同的，增加了对象 ID 的唯一性。

## 计数器

最后三个字节代表一个计数器。此计数器是一个自动递增的数字，类似于 SQL 和 NoSQL 数据库中的其他计数器变量，使对象 ID 唯一(在 SQL 中，这可能会妨碍可伸缩性)。假设两个文档同时在同一台机器上以相同的流程创建。计数器字节将是不同的，这有助于对象 ID 的唯一性。

# 对象 ID 问题

在 MongoDB 中，对象 ID 中的计数器出现了一个问题，可能会限制它的唯一性。

只要计数器不溢出，对象 ID 就是唯一的！

*计数器溢出*问题是当计数器达到其最大容量时，导致文档具有相同的对象 ID。因此，对象 ID 几乎是唯一的，但不是 100%唯一的！

原因如下:计数器被分配了三个字节。这意味着它有能力代表多达 1600 万个数字。如果在同一时间，在同一台机器上，用同一过程生成那么多文档，那么两个文档可以共享同一个对象 ID。

# 使用猫鼬

当使用 Node.js 和 Express.js 构建应用程序时，我们使用[mongose](https://mongoosejs.com/)。Mongoose 是 MongoDB 驱动程序的抽象。因此，在创建文档时，mongose*与 MongoDB 驱动程序对话*来创建一个新的对象 ID。

## 创建对象 ID

```
// create an object id on memory - Not on DB!const mongoose = require('mongoose');const id = mongoose.Types.ObjectId();
console.log(id);// output -> 5f1819229fdf8a0c7c2d8c36
```

## 正在验证对象 ID

使用 mongoose，您还可以使用`isValid`属性静态验证对象 ID:

# 结论

MongoDB 对象 ID 是惟一的，除非达到了特定的场景(如上所述)。因此，作为软件开发人员，我们应该考虑系统的*大小*和*复杂度*，在自动生成的 ID 和自定义对象 ID 之间进行权衡。

方法完全由你决定。关于 MongoDB 对象 ID 的更多信息，可以参考 [MongoDB](https://docs.mongodb.com/manual/indexes/#default-id-index) 和[mongose](https://mongoosejs.com/docs/guide.html#_id)的官方文档。

我希望这个故事让您了解了 MongoDB 中对象 ID 的唯一性和可靠性。享受学习和编码的乐趣！
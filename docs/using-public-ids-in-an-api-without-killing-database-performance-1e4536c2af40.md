# 在不降低数据库性能的情况下在 API 中使用公共 id

> 原文：<https://betterprogramming.pub/using-public-ids-in-an-api-without-killing-database-performance-1e4536c2af40>

## 让我们提高数据库速度

![](img/a93ed1fae31a1e329b06b74485cb96e8.png)

[布拉登·科拉姆](https://unsplash.com/@bradencollum?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

如果你曾经访问过现代网络应用，你可能会注意到一个类似这样的网址:

```
[https://medium.com/p/1e4536c2af40](https://medium.com/p/1e4536c2af40)
```

众所周知，URL 的最后一位`[1e4536c2af40](https://medium.com/p/1e4536c2af40)`是一个惟一的标识符，用于获取和显示资源。说到后端开发，这可以称之为公共 ID。

然后，web 应用程序或用户通常可以使用这个公共 ID 进行呼叫。Stripe 的 API 文档中的 API 调用示例(本文中的示例是 REST APIs，但它也与 GraphQL APIs 密切相关！):

```
[https://api.stripe.com/v1/customers/abcdef123456](https://api.stripe.com/v1/customers/abcdef123456) 
```

# 为什么公开 id 有用？

使用公共 ID 的替代方法通常是使用数据库为该对象提供的主键。给定上面 Stripe 的 API 查询，它会变成类似于:

```
[https://api.stripe.com/v1/customers/3](https://api.stripe.com/v1/customers/abcdef123456)
```

## **但是为什么这样更不好呢？**

这是一个很好的问题，关于这个话题有几个不同的阵营。

主要论点是，尽可能少地给出关于数据库的信息总是更好的主意。

当然，使用真实的数据库 ID 所揭示的信息是 ID 本身，以及可能有多少行。在上面的例子中，攻击者可能会推断出`/1`和`/2`可能是真实的资源。使用公共 ID，猜测真实资源的标识符变得更加困难。

# 使用公共 id 的缺点是什么？

反对使用公共 id 的主要理由是，很容易以负面影响性能的方式实现它们。考虑以下场景:

*   Stripe 有一个用户数据库
*   users 表具有典型的自动递增和唯一整数主键
*   因为我们希望使用公共 ID，所以每次创建用户时，Stripe 都会生成一个公共 ID，并将其作为文本存储在一个新列中
*   当给定一个公共 ID 调用 get-user 端点时，将触发一个查询，该查询按公共 ID 进行搜索并返回匹配的用户

您可能已经发现了这里的问题。现在，数据库必须进行全表扫描，而不是通过唯一的主键进行搜索。这可以通过使 public ID 列惟一或被索引来缓解，但这并不理想，因为真正的主键列已经惟一并被索引了。

还有另一个问题:外键怎么办？如果我有一个用户可以拥有的`paymentOption`对象，那么`paymentOption` `ownedByUserId`列应该是公共 ID 还是主键？API 中应该返回什么？这已经变得一团糟了！

我们可以做得更好。

# 在不牺牲性能的情况下实现公共 IDs

让我们在不影响性能的情况下重写条带场景:

*   Stripe 有一个用户数据库
*   users 表具有典型的自动递增和唯一整数主键
*   除了这个主键之外，我们不在模式中添加任何东西

我们不会在模式中做任何与公共 id 相关的事情，而是让它保持原样。现在让我们进入我们虚构的 Stripe Node.js 服务器。

```
const processGetQuery = (customerId) => {
  const dbCustomer = await Database.fetchCustomer(customerId);
  return dbCustomer;
}
```

如您所见，我们神奇的`getCustomer`端点函数获取客户的 ID，获取并返回对象。目前这使用一个真实的数据库 ID。让我们解决这个问题。

```
const getCustomerPublicId = (databaseId) => encodeFunction(`user-${databaseId}`);const getCustomerDatabaseId = (publicId) => decodeFunction(`user-${databaseId}`);const processGetQuery = (publicCustomerId) => {
  const databaseId = getCustomerDatabaseId(publicCustomerId);
  const dbCustomer = await Database.fetchCustomer(databaseId);
  return {
    ...dbCustomer,
    id: getCustomerPublicId(dbCustomer.id)
  };
}
```

现在，用户向 API 提供了一个公共 ID。在服务器上，我们使用某种解码功能将其转换为数据库 ID。然后，我们使用真正的主键获取客户并返回结果——在向用户显示之前，将 ID 编码为公共 ID。

现在，我们已经受益于标准自动递增整数主键带来的一切，不需要在数据库中瞎折腾了。

**关于前缀**的快速说明:想知道为什么使用`user-${databaseId}`翻译公共 ID 吗？那是因为在用户表上，有一个带`primary key = 1`的用户，在另一个表上，会有另一个带`primary key = 1`的对象。为了确保它们有不同的公共 ID，我们在将表名交给编码函数之前，在表名前添加了数据库 ID。

**关于分布式数据库的快速说明**:这种方法并不真正适用于分布式数据库场景，在分布式数据库场景中，由于体系结构的原因，您需要一个 UUID，并且只能处理它！

你应该使用哪种编码函数？在 Node 工作，我个人推荐 [Hashids](https://www.npmjs.com/package/hashids) 。(注意:Hashids 只支持整数编码，所以如果您不想从整数 id 编码，您需要找到另一个工具！)

# 进一步优化

当然，在真实的应用程序中使用这种类型的代码会很快变得非常冗长。这是我在一个项目中做的一个片段:

您可以看到，添加新类型的 ID 并在任何地方快速访问它变得非常简单，而不需要太多的样板文件。
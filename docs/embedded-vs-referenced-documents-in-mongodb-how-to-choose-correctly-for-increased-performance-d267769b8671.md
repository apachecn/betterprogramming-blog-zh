# MongoDB 中的嵌入式文档与引用文档:如何正确选择以提高性能

> 原文：<https://betterprogramming.pub/embedded-vs-referenced-documents-in-mongodb-how-to-choose-correctly-for-increased-performance-d267769b8671>

## 了解有效建模数据库模式所需的要点

![](img/37b45874ad492b7a55144456c55cbc37.png)

克里斯·利维拉尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

[MongoDB](https://www.mongodb.com/) 是 NoSQL 的一个数据库，它使用一种文档格式来存储数据。它将这些文档存储在集合中，集合相当于 MongoDB 的一个表。

我使用 MongoDB 已经很多年了，并且有很多用例。添加新集合时需要回答的一个关键问题是，是将相关文档存储为嵌入文档，还是在单独的集合中引用它们。

做出错误的决定可能会对性能产生重大影响，那么你应该选择哪个选项呢？

# 嵌入文档和引用文档有什么区别？

与关系数据库类似，MongoDB 允许您使用模式来定义集合中的字段和数据类型。虽然模式是可选的，但是强烈建议您使用它们来轻松理解文档的格式。通过确保必填字段存在，并且所有字段都符合各自的类型，模式还可以确保您拥有高质量的数据。

MongoDB 中一个强大的模式选项是通过嵌入或引用将文档相互关联。

## 嵌入文档

嵌入文档作为子文档存储在父文档中。这意味着它们都存储在一个集合中，每当您检索父文档时，也会检索其所有嵌入的文档。

例如，如果您有一个用户文档，它可能包含一个详细描述该用户地址的嵌入文档列表。在 JSON 格式中，它可能看起来像这样:

```
{
  "_id": 1,
  "name": "Ashley Peacock",
  "addresses": [
    {
      "address_line_1": "10 Downing Street",
      "address_line_2": "Westminster",
      "city": "London",
      "postal_code": "SW1A 2AA"
    },
    {
      "address_line_1": "221B Baker Street",
      "address_line_2": "Marylebone",
      "city": "London",
      "postal_code": "NW1 6XE"
    }
  ]
}
```

通过将我们的地址存储为嵌入式文档，我们只保存了一个文档。

## 参考文件

与嵌入文档不同，引用文档存储在独立于其父文档的集合中。因此，可以检索父文档*，而不需要*检索其引用的任何文档。

如果我们使用引用文档，我们的文档看起来怎么样？

```
// Stored in the user collection
{
  "_id": 1,
  "name": "Ashley Peacock",
  "addresses": [
    1000,
    1001
  ]
}// Stored in the address collection
{
  "_id": 1000,
  "address_line_1": "10 Downing Street",
  "address_line_2": "Westminster",
  "city": "London",
  "postal_code": "SW1A 2AA"
}// Stored in the address collection
{
  "_id": 1001,
  "address_line_1": "221B Baker Street",
  "address_line_2": "Marylebone",
  "city": "London",
  "postal_code": "NW1 6XE"
}
```

在使用证明人存储我们的地址时，我们存储了 3 个独立的文档。如果我们想和用户一起检索用户的地址，我们必须有效地运行两个查询:第一个检索用户，第二个检索他们的地址。

这是因为与 SQL 不同，MongoDB 没有连接的**性能**概念。它确实提供了一个[查找](https://docs.atlas.mongodb.com/schema-suggestions/reduce-lookup-operations/)操作，但是通常在实时生产环境中使用它的性能不够——在速度和资源方面都是如此。

相反，我们应该更好地设计我们的模式以适应 MongoDB，这是我们接下来要讨论的内容。

> 值得注意的是，当使用引用时，我们可以选择如何建立关系模型。如上所述，我们可以决定在父文档中存储一个引用列表。如果引用列表可能会变大，通常最好将父文档(本例中为用户)的 ID 存储在任何相关文档中(本例中为地址)。否则，每次添加新地址时，我们都必须更新用户文档来添加新的引用。

# 如何在嵌入文档和引用文档之间进行选择

一般来说，当父文档及其相关文档同时被读取或写入时，我们应该考虑使用嵌入式文档。此外，我们应该根据集合是读负载重还是写负载重来划分优先级。

例如，考虑以下文档:

```
{
  "_id": 1,
  "name": "Ashley Peacock",
  "account_balance": 150
  "stocks": [
    {
      "code": "AMZN",
      "amount": "3",
      "value": "1478.22"
    },
    {
      "code": "GOOGL",
      "amount": "5",
      "value": "2811.94"
    }
  ],
  "orders": [
    {
      "order_id": 1,
      "order_value": 102.5,
      "order_items": [...]
    },
    {
      "order_id": 2,
      "order_value": 22.75,
      "order_items": [...]
    }
  ]
}
```

我们有两个嵌入的文档:股票和订单。但是，它们应该被嵌入吗？

要回答这个问题，我们需要更多的背景知识——下面是这个虚构的股票交易应用程序在我们如何与我们的用户集合进行交互方面的样子:

*   登录后，用户 ID 将存储在 web 会话中。
*   股票包含用户当前拥有的股票。它们显示在网站的几乎每个页面上，并且出于合规性和准确性的原因，每次呈现页面时都必须从数据库中检索。与股票被阅读的频率相比，股票很少被更新。
*   账户余额也显示在每一页上。
*   订单仅显示在订单页面上，并且仅在下订单时更新。

在这种情况下，我会将股票存储为嵌入式文档，因为每当我们呈现一个页面时，我们都需要帐户余额和股票列表。如果我们分开存储它们，我们必须执行两个单独的查询——一个获取用户，另一个获取他们的股票。考虑到这些数据显示在每一页上，如果我们引用它们，那么每次页面加载的查询数量会翻倍。

继续讨论订单，我会将它们存储为引用文档，因为它们只在特定页面上使用。此外，我们可以查询订单，并添加新订单，只需使用存储在 web 会话中的用户 ID——根本不需要查询用户集合。

一个用户可能有 100 个订单。如果我们将它们存储为嵌入式文档，为用户查询返回的数据量将随着时间的推移而显著增长。此外，它会返回大量的数据，而这些数据在网站的大部分时间里都不会被使用。要添加新订单，我们需要读取整个用户文档，添加新订单，然后重写整个文档。

然而，使用引用的文档，我们可以简单地将一个新文档插入到 orders 集合中，绑定到用户 ID，并且我们根本不需要读取用户集合(或任何其他订单！).

# 摘要

这只是一个例子，根据您的上下文，决定是嵌入还是引用会有所不同。话虽如此，以下是一些适用于大多数情况的通用指南:

*   如果您正在编写大量数据，而不需要阅读整个父文档，请使用引用。我在工作中遇到过一个实例，我们将数百个文档存储为嵌入式文档，导致写入时间超过一秒钟(主要是因为每次都需要序列化/反序列化整个文档)。
*   如果您正在阅读和使用文档及其相关文档中的大部分数据，请将它们作为嵌入文档存储在一起。将它们存储为引用只会不必要地增加响应时间，因为执行的查询比需要的要多。
*   如果您经常需要两个相关文档中的数据子集，可以考虑混合使用这两种方法。用户地址是一个典型的例子:您可以选择将当前地址作为嵌入文档存储在用户文档中，并将很少使用的历史地址存储为引用文档。

```
**My book, Creating Software with Modern Diagramming Techniques, is out now!**[Click here](https://www.amazon.com/dp/1680509837?maas=maas_adg_265A9C302E256D26C2E10C30DA1AA728_afap_abs&ref_=aa_maas&tag=maas) to learn how to create diagrams to communicate information more directly and clearly than words ever can. Using only text-based markup, powered by Mermaid, create meaningful and attractive diagrams to document your domain, visualize user flows, reveal system architecture at any desired level, and much more!
```
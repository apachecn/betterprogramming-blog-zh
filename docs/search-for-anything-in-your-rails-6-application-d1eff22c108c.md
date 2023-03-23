# 在 Rails 6 应用程序中搜索任何内容

> 原文：<https://betterprogramming.pub/search-for-anything-in-your-rails-6-application-d1eff22c108c>

## Rails 活动记录备忘单

![](img/d386a52c577b0545044c3e0b48109f56.png)

乔希·卡拉布雷斯在 [Unsplash](https://unsplash.com/s/photos/speed?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

构建用户喜爱的应用程序的关键很简单——它必须运行良好，速度必须很快。

使用 Rails 5+时，确保小规模应用程序的速度是微不足道的。使用活动记录很容易从小型数据库中检索出一个对象。但是，当记录数量增长到成千上万时，应用程序如何处理伸缩呢？

问题是——Ruby 作为一种编程语言非常慢。然而，SQL 查询非常快。active recording 通过让开发人员用 Ruby 查询数据库来执行 SQL 搜索，弥补了这一缺陷。

活动记录是使 Rails 成为如此强大的框架的原因之一，因为它允许开发人员在不编写 SQL 的情况下编写 SQL。这是 Rails 魔力的一部分，非常棒。

直到您开始查看活动记录文档，并感觉自己又回到了起点。

在我做过的每一次 Ruby on Rails 编码面试中，第一个问题总是关于活动记录。活动记录是使 Rails 成为如此强大的框架的原因之一，因为它允许开发人员在不编写 SQL 的情况下编写 SQL。

所以，下面是它是如何发生的:

*   Rails 将活动记录请求转换成等价的 SQL 查询。
*   SQL 查询从数据库中检索相应的结果。
*   Rails 实例化了相应模型的等价 Ruby 对象。
*   应用程序运行任何相关的回调，如`after_find`或`after_initialize`。

# 一个简单的起点是在数据库中找到一条记录

## find()或 find_by()

因为 ID 是主键，`find`方法将总是返回一条记录，如果该记录不存在，它将返回 nil。

```
#returns the product with the id of 3
Product.find(3)#SQL query
SELECT * FROM products WHERE (products.id = 3) LIMIT 1
```

要查找多条记录，可以传递一个 id 数组。

```
#returns an array of products containing IDs 1,3, and 5
Product.find([1,3,5])#SQL query
SELECT * FROM products WHERE (products.id IN (1,3,5))
```

`find_by`有点复杂。它允许您使用任何属性搜索记录，并将返回数据库中第一个匹配的记录。

```
#finds the first product matching the name Awesome Product
Product.find_by(name: "Awesome Product")#finds the first product matching the author Maria 
Product.find_by(author: "Maria")#finds the first product matching the name and author criteria
Product.find_by(name: "Awesome Product", author: "Maria")
```

与`find`方法不同，如果没有找到匹配的记录，`find_by`将*而不是*返回 nil，这是一个更安全的选择，可以避免遇到异常错误。

# 如何查找多条记录

## 在哪里

`where`方法是一个很好的起点。它返回符合条件的记录数组。

```
Product.where(archived: true)OR Product.where('archived' => true)#SQL query 
SELECT * FROM products WHERE (products.archived = true)
```

在对来自 params 的查询使用`where`方法时需要小心，因为它经常是 SQL 注入的目标。您必须对字符串进行转义以避免这种类型的攻击。

```
DON'T DO THIS
Book.where(*"author = #{params[:author]}"*)DO THIS
Book.where("author = ?", params[:author])
```

## 哪里没有

为了得到与`where`搜索相反的结果，使用`where.not`方法从数据库中选择*不*匹配给定标准的对象。

```
Product.where.not(archived: true)ORProduct.where.not('archived' => true)#SQL query 
SELECT * FROM products WHERE (products.archived != true)
```

您也可以在上面的语句中使用`false`来得到相反的结果，但是对于更复杂的查询，`where.not`是一个巧妙的技巧。

## 在多个表格中搜索

如果多个表通过`has_many` / `belongs_to`关联连接，则使用`where`查询来搜索它们。

注意，当使用`joins`语句时，它指的是关联的表名，而不是模型名。在父关联中搜索时使用此选项。

```
Book.where(author: params[:author])Author.joins(**:books**).where(books: { author: params[:author]})
```

## 避免 N+1 次查询

当搜索多个记录时，Rails 很容易在每次 SQL 执行时添加越来越多的查询。通过预先加载关联来避免 n+1 陷阱。

```
Book.includes(:comments).where(comments: { visible: true })
```

# 就是这样！

这些是在 Rails 应用程序中进行搜索的基础。活动记录的速度快如闪电，但它所能做的仍然有限。

我推荐用 [pg_search](https://github.com/Casecommons/pg_search) 这样的宝石。Rails 中的过滤功能更多，包括限制、排序、分组等。如果你将来想写一篇关于它的文章，请告诉我！
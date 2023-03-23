# 如何处理 NoSQL 数据库中的一对多关系

> 原文：<https://betterprogramming.pub/how-to-handle-one-to-many-relationships-in-nosql-databases-23627d404cb0>

## 3 数据建模策略

![](img/7889ab9842c15cb1b43cafaed0412045.png)

在 [Unsplash](https://unsplash.com/s/photos/many?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由[zdenk Macháek](https://unsplash.com/@zmachacek?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片。

NoSQL 的数据建模比普通的 SQL 更复杂。有时候，这违背了我们的直觉。尤其是作为一名 SQL 出身的人，我发现很难理解像 AWS DynamoDB 或 Firebase 这样的 NoSQL 数据库中数据建模背后的各种原因。

在本文中，我将解释建模一对多关系的三种策略以及可以使用它们的场景。

# 反规范化+复杂属性

对于 SQL 开发人员来说，反规范化是一个肮脏的词。但在 NoSQL 世界，它有自己的使用案例。

假设你有一些客户在月底支付一定的费用。他们可以有各种支付选择，费用因客户而异。

你是做什么的？您是否要创建一个单独的表，将客户与付款方式对应起来？

不。相反，我们将付款方式作为一个数组存储在 customer 表中。

```
Customer
|
|-- Name
|-- Address
|-- PaymentMethods: ['Cash' , 'Credit Card', 'Debit Card']
```

这里，我们使用了一个名为`PaymentMethods`的复合属性，它包含一组可用的支付选项。

## 当它工作的时候

*   如果父表发生了变化，但子表没有变化。这意味着用户可以更新他们选择的方式，但支付方式本身保持不变。
*   如果我们需要过于频繁地访问子数据。在我们的案例中，如果我们需要了解客户支付账单的可用选项。

## 当它不起作用时

*   如果您想查找使用特定付款方式的所有客户。
*   如果您的付款方式变更太频繁。
*   如果你要询问付款方式。

# 反规格化+复制

现在我们要讨论在 NoSQL 维护一对多关系的另一种方法。

假设你正在设计一个订票系统。您有一个表，您想在其中存储客户订购的门票。现在，在您的`Tickets`表中，您需要一些关于客户的基本信息。

如果您保留客户 ID，并在每次需要查看票据详细信息时获取有关用户的信息，那么它将无法扩展。您可以做的是将客户的基本信息存储在`Tickets`表中。

```
Ticket
|
|-- TicketId
|-- JourneyDate
|-- CustomerName
|-- CustomerBirthDate
```

因此，在您的`Ticket`项目中，您有了关于客户的基本详细信息。这样，你可以避免每次想取票时都要回到`Customer`桌。

是的，您在数据库中复制了客户的姓名和出生日期，但这是为了一个好的理由！

## 当它工作的时候

*   您需要经常访问父表的信息。
*   父表信息不会经常改变(客户的出生日期不太可能改变)。

## 当它不起作用时

*   如果重复的数据经常改变。例如，客户的奖励积分。在这种情况下，用这些信息更新所有孩子的记录是不现实的。

# 复合主键+查询

如果以上两种方法都不能满足您的需求，那么还有另一种方法。但是理解和设计起来有点棘手。

例如，我们正在设计一个组织数据库，其中的雇员属于一个特定的部门。员工也有自己的角色。

现在您想查找一个部门的所有员工。

您将主键保留为`ORG#1234`，将员工记录的排序键保留为`EMP#DEPARTMENT#1234`。部门的排序关键字可以是`DEPT#1234`。

*注:这里的* `*1234*` *只是一个 ID。在现实生活中，这是随机的。*

现在，当您想要查询某个部门的所有员工时，您可以:

```
where :PK = ORG#{id} and begins_with( EMP#DEPARTMENT, :SK)
```

同样，您可以找到任何组织的所有部门:

```
where :PK = ORG#{id} and begins_with( DEPARTMENT#, :SK)
```

因此，你可以看到如何利用各种功能，如`begins_with`、`contains`等。

# 最后的想法

设计一个 NoSQL 数据库不是开玩笑的事。你必须花很多精力去设计一个，并且事先考虑所有可能的访问策略。否则，您不能很容易地恢复或更改数据建模。

我希望这篇文章对 NoSQL 数据库的数据建模思想有所启发。我鼓励你更多地了解这些话题！

编码快乐！

**通过**[**LinkedIn**](https://www.linkedin.com/in/56faisal/)**或我的** [**个人网站**](https://www.mohammadfaisal.dev/) **与我取得联系。**

## 资源

*   牛逼教程:[https://www.youtube.com/watch?v=fiP2e-g-r4g](https://www.youtube.com/watch?v=fiP2e-g-r4g)
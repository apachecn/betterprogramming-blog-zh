# 在提取湖泊数据的 SQL 查询中连接表

> 原文：<https://betterprogramming.pub/joining-tables-in-sql-queries-for-extracting-data-in-a-lake-9c0811267419>

## 为可持续 ETL 设置框架

![](img/ffcb162accd3261ed418b5acbe74cfc7.png)

在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上由 [Nastuh Abootalebi](https://unsplash.com/@sunday_digital?utm_source=medium&utm_medium=referral) 拍摄的照片

在一次与领导层的会议中，一位最终用户向我提出了她一直在考虑的一个分析工具的想法。她与我分享了一份电子表格，其中包括一些手动输入的数据和她的愿景。我采取了重要举措，向她提问，以了解这种新分析工具的业务目的。

我不处理抵押贷款，我的公司也不处理，但在我职业生涯的头几年，我帮助人们支付他们的业务改造或搬迁到一个新的地区。抵押贷款数据模拟了与最近提交给我的一个项目相关的业务挑战。

我现在有了最终用户希望获得的目标，所以是时候开始评估可用的数据了。在我的角色中，我通过 Microsoft SQL Server Management Studio 访问数据集市。

从那里，我开始为我的数据湖中的第一个 SQL 查询创建框架，写出以下语句:

```
SELECT
FROM
WHERE
GROUP BY
```

在我的绝大多数查询中，这四个语句提供了一个框架来编写一个查询，我可以在屏幕共享期间重新访问、与他人共享并轻松引用。

此时，我不是寻找特定的数据字段开始填充，而是评估数据库中的特定表，寻找那些与我的目标最相关的表。

```
SELECT
FROM 
MortgageLoanListings AS ML
JOIN MortgageRatesTable AS MRT
    ON ML.RatingIndex=MRT.RatingIndex
INNER JOIN OfficeBridge as OB
    ON OB.OfficeId=ML.OfficeIdJOIN AccountInformationTable AS AC
    ON ML.AccountNumber=AC.AccountNumberWHERE
GROUP BY
```

关于我的格式，请注意每个 SQL 语句都是大写的。任何审查您的代码的人都可以发现各种语句，以进行更改并提供反馈。此外，请注意缩进为您提供了另一种更易于阅读的工具。

我还必须提到我的模式。您经常会在湖中浏览数据表，发现没有一个数据元素与另一个表中的任何数据元素完全一致。

准备好花时间寻找数据的正确连接。通常，我们可以用桥表建立这些联系。这些将数据排列在多个链中，这些链仍然相互连接，这就形成了雪花模式的起点。

作为添加我想要查询的数据字段之前的最后一步，我定位将过滤我的数据的数据字段。这是运行查询的重要一步，因为您可能会节省几个小时等待查询返回与您无关的结果。

```
SELECTFROM 
MortgageLoanListings AS ML
JOIN MortgageRatesTable AS MRT
    ON ML.RatingIndex=MRT.RatingIndexJOIN AccountInformationTable AS AC
    ON ML.AccountNumber=AC.AccountNumberWHERE
   OB.OfficeName='Pittsburgh'GROUP BY
```

现在我们已经熟悉了可用的数据表，连接并过滤了正确的数据，我们可以开始选择我们需要的数据了。

但是，我可能会找到这个项目的一些表，但是我将对我的 SELECT 语句以及我连接的表进行一些更改。

我可能会在连接所有数据的雪花模式的中心更改主表，只是发现键排列得更好。但这是第一次迭代

```
SELECT
OB.OfficeName,
MRT.MortgageRatesTable,
AC.AccountCustomerName,
SUM(ML.MortgageNumber)FROM 
MortgageLoanListings AS ML
JOIN MortgageRatesTable AS MRT
    ON ML.RatingIndex=MRT.RatingIndexJOIN AccountInformationTable AS AC
    ON ML.AccountNumber=AC.AccountNumberWHERE
   OB.OfficeName='Pittsburgh'GROUP BY
OB.OfficeName,
MRT.MortgageRatesTable,
AC.AccountCustomerName,
SUM(ML.MortgageNumber)
```

通过以上内容，您会注意到我用层次结构对 select 语句进行了排序。

每当您发现数据在最终产品上的显示方式存在差异时，最好能够轻松识别哪个数据更好。客户可以有多个抵押贷款。

这对于不希望滚动查看 Stella Thompson 的第一笔抵押贷款、房屋净值信用额度和度假房屋抵押贷款来查看她总共欠了多少钱的最终用户来说可能很麻烦。

汇总这些数据是使用 SUM 完成的。从那里可以在`GROUP BY`语句中重复这个层次。

一旦我生成了 SQL 查询，我就将数据加载到 Power BI 中，并使用一个过程创建一个交互式可视化，在这个过程中，我定期会见几个最终用户，获得反馈，多次回去编辑我的查询，并在最终产品发布之前制作许多版本。

我想重新整理一下我在那次会议上与领导层和最终用户的对话。这是进行开放式对话的重要一步。这样做不仅能给我需要的反馈，还能让多个团队注意到我的工作。对我来说，有了这个通知，我就有能力掌控我设计工具的许多方式。我在那种力量中茁壮成长。
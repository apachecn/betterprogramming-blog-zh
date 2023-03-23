# 何时以及为什么选择 NoSQL 数据库？

> 原文：<https://betterprogramming.pub/when-and-why-choose-a-nosql-database-1a9aa2e2038e>

## 了解它与 SQL 数据库的不同之处

![](img/a4fabde24ddf14c79f93e15acf50b0d8.png)

本杰明·雷曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 介绍

长期以来，关系数据库是数据存储的普遍选择。作为一名架构师，您唯一的选择是使用哪种关系数据库，并且通常这已经由您的公司预先决定了。

在经历了这么长时间的关系数据库之后，现在可能是时候使用新的东西了，即非关系数据库。为什么会这样——我们想在这篇文章中深入探讨。

# 记忆

在我们深入研究(非)关系数据库之前，我们应该先看看不同的内存。

> 通常，在计算机体系结构中有两个内存区域。一个是快速易变的，另一个更大更慢。
> 
> 快一点的叫“主存”，空间有限。此外，这还存在一个问题，即一旦断电，数据就会丢失。出于这个原因，我们将数据写入更大更慢的存储器，称为“后备存储器”(第 1 章)。为什么是 NoSQL？)[【2】](https://www.amazon.com/-/de/dp/0321826620/ref=sr_1_1?__mk_de_DE=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=1E8GHIMPJ9D2P&keywords=NoSQL+Distilled+by+Pramod+J.+Sadalage+and+Martin+Fowler&qid=1644070607&sprefix=%2Caps%2C297&sr=8-1)。

后备存储器可以采取不同的形式，数据可以用不同的方式组织。例如，在大多数用户应用程序(如 Word)中，操作系统的文件系统负责管理数据。然而，数据库通常是企业应用程序中的后备存储。在存储大量数据方面，数据库比文件系统更灵活，在提供数据方面也更快。

# 什么是 SQL 数据库？

关系数据库支持使用一种称为结构化查询语言(SQL)的特定编程语言。这种语言用于在关系数据库环境中查询和操作数据。

值得注意的是，SQL 并不是查询关系模型的唯一语言，但它是最常见的语言 [[3]。](https://www.ibm.com/cloud/blog/sql-vs-nosql)

数据以行和表的形式存储。这些表由列(属性)和行(记录)组成。同时，条目具有逻辑关系。

关系数据库必须有四个具体特征。这些特征通常被称为“酸性”[原子性、一致性、隔离性和持久性]。要了解更多信息，请看这里的。

# 什么是 NoSQL 数据库？

非 SQL 或非关系数据库的名称是指这种形式的数据库以非结构化的方式存储数据，这与关系数据库有很大的不同，关系数据库以表和行的形式存储数据。

底层技术从 20 世纪 60 年代就已经存在了。由于不断增长和变化的数据 [[](https://azure.microsoft.com/en-gb/overview/nosql-database/) 2】，它现在变得越来越受欢迎。

非关系数据库帮助开发人员快速创建数据库系统和存储新信息。它们遵循[、【上限定理】、](https://www.ibm.com/cloud/learn/cap-theorem)、*一致性、可用性、*和*分区容差】、*，即在每个数据库中，只能保证上述特征中的两个。

# SQL 和 NoSQL 有什么区别？

有五个主要区别:

1.  语言
2.  可量测性
3.  结构
4.  特征
5.  支持和社区

## **1。语言**

SQL 已经存在了 40 多年，并且为许多人所熟知。它是一种安全且通用的语言，但是将用户限制在已定义的表模式中。这就是为什么在消费数据之前组织和理解数据更为复杂的原因。

NoSQL-数据库是基于一个动态的模式，允许我们提出替代的结构。因此为我们提供了更大的灵活性和更少的规划工作。此外，在添加新字段和新属性时，我们有更大的自由度。也可以在不同的数据库中使用不同的语法。

与 SQL 数据库不同，NoSQL 没有统一的标准接口，因此更复杂的查询可能难以执行。SQL 有许多不同的方言，但大多数使用标准语法和相似的语法。

## **2。可扩展性**

许多 SQL 数据库的纵向伸缩性很好，也就是说，通过添加更多或更好的硬件。由于 NoSQL 数据库的不同架构，它们的水平伸缩性更好。大量 NoSQL 数据库按照[主从架构](https://medium.com/bina-nusantara-it-division/the-master-slave-database-concept-for-beginners-8a3884896b14)工作。

SQL 数据库也可以水平扩展。但是，这也不受支持。

## **3。结构**

最显著的区别之一可能是在数据结构上。如上所述，SQL-Database-schema 通常表示关系的表格数据。它们包含带有列(属性)和行(条目)的表格。

NoSQL 数据库不需要保持特定的格式。有四种类型的 NoSQL 数据库:文档数据库、键值存储、面向列的数据库和图形数据库(更多信息，请看这里的)。

## **4。特性**

简单回顾一下，因为我在上面已经提到过了。NoSQL 数据库是基于“上限定理”相比之下，SQL 数据库的特征被称为“ACID”

## **5。支持和社区**

SQL 数据库拥有大型社区和定义的标准。万维网上有许多给初学者的指南。此外，SQL 语言是专有的，或者与大量的个人提供者相关联。

虽然 NoSQL 社区受益于开放系统，但通常情况下，这些社区更小，更分散。与 SQL 数据库相比，与 NoSQL 数据库的兼容性差异更大。因此，您必须更仔细地寻找依赖关系。

# 何时选择 NoSQL 而非 SQL？

从上面的解释中您可能已经注意到，这两个数据库系统是为不同的目的而设计的。

一般来说，每当涉及图形或层次数据时，建议使用 NoSQL。但是它们也适用于非常大且快速变化的数据集，以及在没有特定数据模式的情况下快速发展的公司。

如果您存储高度结构化的数据，并且这些数据之间以及系统中的关系对一致性至关重要，那么 SQL 更适合。如果您不确定，SQL 数据库是更好的选择。

# TL；DR；

**NoSQL 数据库:**

*   是开源的，
*   非关系的，
*   无模式，
*   灵活且适合水平缩放。

**SQL 数据库:**

*   面向单一供应商，
*   关系的，
*   基于模式，
*   更适合垂直缩放。

# 来源:

[](https://www.amazon.com/-/de/dp/0321826620/ref=sr_1_1?__mk_de_DE=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=1E8GHIMPJ9D2P&keywords=NoSQL+Distilled+by+Pramod+J.+Sadalage+and+Martin+Fowler&qid=1644070607&sprefix=%2Caps%2C297&sr=8-1) [## [1] NoSQL 精粹:多语言持久性新兴世界简要指南

### 亚马逊网站:NoSQL 精粹:多语言持久性新兴世界简要指南:9780321826626: Sadalage…

www.amazon.com](https://www.amazon.com/-/de/dp/0321826620/ref=sr_1_1?__mk_de_DE=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=1E8GHIMPJ9D2P&keywords=NoSQL+Distilled+by+Pramod+J.+Sadalage+and+Martin+Fowler&qid=1644070607&sprefix=%2Caps%2C297&sr=8-1) [](https://azure.microsoft.com/en-gb/overview/nosql-database/) [## [2] NoSQL 数据库-什么是 NoSQL？微软 Azure

### NoSQL 数据库可互换地称为“非关系型”、“NoSQL 数据库”或“非 SQL”，以突出事实…

azure.microsoft.com](https://azure.microsoft.com/en-gb/overview/nosql-database/) [](https://www.ibm.com/cloud/blog/sql-vs-nosql) [## [3] SQL 与 NoSQL 数据库:有什么不同？

### 看看 SQL 和 NoSQL 数据库，它们的主要区别，以及哪个选项最适合您的情况。

www.ibm.com](https://www.ibm.com/cloud/blog/sql-vs-nosql) [](https://www.mongodb.com/scale/types-of-nosql-databases) [## [4]NoSQL 数据库的类型

### NoSQL 是一个总括术语，用来描述传统 SQL 数据库的任何替代系统。NoSQL 数据库都是…

www.mongodb.com](https://www.mongodb.com/scale/types-of-nosql-databases)
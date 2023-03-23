# 优化 Django 数据库的 5 种方法

> 原文：<https://betterprogramming.pub/5-ways-to-optimize-databases-in-django-e17d31a2e92>

## 如何优化数据库以提高效率？

![](img/99f2c95be2dbc470b2b9e3d55b99c7ec.png)

照片由[克里斯·利维拉尼](https://unsplash.com/@chrisliverani?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

如何优化数据库？:这是软件相关工作面试中被问得最多的问题之一。在本教程中，我想分享一些在 Django 应用程序中执行数据库优化的方法。

# 数据库和关系数据库

数据库是存储在计算机中的一组结构化数据。另一方面，关系数据库是一种数据组织在表中的数据库。表格使得检索数据变得容易。

# 关系数据库管理系统(RDMS)

RDMS 是管理关系数据库的系统。一些常用的开源 RDMS 有:

*   MYSQL-这是最流行的 RMDS。它也很稳定，主要用于 PHP 应用程序。
*   PostgreSQL- PostgreSQL 也很容易使用，它有一个很大的社区。它在大多数 web 开发语言中使用。
*   SQLite — SQLite 是轻量级的，易于设置

# 数据库优化

如何让你的数据库更快？让我们来看看提高数据库性能的一些方法

## 1.数据库索引

想象一个没有字典的场景。找到任何东西都要花很长时间。数据库索引是加速数据库表上数据检索操作的一种方式。索引的缺点是需要额外的内存来维护索引结构。在标准查询中，您必须搜索每一行才能找到您想要的内容:但是，索引用于快速定位数据，而不是搜索数据库表中的每一行。

数据索引的标准规则是将索引放在查询最多的列上。每当执行查询时，索引通过匹配索引中的值来工作。

例如，考虑我们下面的数据库设计。

众所周知，人们会根据名字来搜索产品；因此，许多查询将发生在`product_name`列上。对于大型数据库，这可能会导致速度变慢。因此为了提高性能，我们将在`product_name`列上添加一个索引，如下所示。

```
product_name = models.CharField(max_length= 255,db_index=True)
```

## 2.Django 分页

分页是将内容分成页面并仅在需要时获取页面的过程。为了在产品表上创建分页，我们将使用 Django paginator 类对数据进行分页。

在上面的代码中，我们首先从数据库中获取所有产品。然后创建一个 paginator 对象，它接受产品数据和我们希望每页显示的商品数量，10。最后，在模板上，我们呈现页面对象。

现在在`prodcuts.html` 页面上，添加以下内容。

## 3.查询集是惰性的(查询优化)

优化数据库性能的另一种方法是减少在数据库上执行的查询集的数量。例如，如果您要执行一个 queryset 来获取所有价格小于 10 的产品，您可能会尝试获取所有产品并对它们进行循环，如下所示。

但是，这种查询方式会消耗大量内存；最好的方法是做到以下几点。

```
under_10_dollars = Product.objects.filter(product_price__lte=10)
```

`lte`代表小于或等于。

## 4.查询集缓存

Queryset 缓存是一种允许您在任何地方重用相同代码的技术，因为结果已经被缓存；当你需要数据时，你不需要一直查询数据库；您需要重用查询集。

例如，如果客户搜索一种产品，他们还需要知道更多关于它的细节以及价格，在这种情况下，您只需点击一次数据库就可以获得所有这些细节。

## 5.迭代器查询

缓存可能被证明是一个缺点，因为如果有很多对象，它会消耗大量内存。在这种情况下，您可能需要使用`[iterator()](https://docs.djangoproject.com/en/4.0/ref/models/querysets/#django.db.models.query.QuerySet.iterator)`。

例如，一个 queryset 返回我们数据库中的所有产品，为了获得更好的数据库性能，我们只需要访问一次

```
products = Product.objects.all().iterator()
```

# 结论

本教程介绍了一些优化数据库效率的方法。另一种方法是使用 [Django 调试工具栏](https://django-debug-toolbar.readthedocs.io)并跟踪查询是如何执行的。
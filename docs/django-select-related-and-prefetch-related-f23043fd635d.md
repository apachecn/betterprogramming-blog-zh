# Django 选择相关和预取相关

> 原文：<https://betterprogramming.pub/django-select-related-and-prefetch-related-f23043fd635d>

## 通过示例检查使用这些方法减少了多少查询

![](img/b519bbd33feefa299fed799de0277bed.png)

[艾蒂安·布朗热](https://unsplash.com/@etienneblg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/laptop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在 [Django](https://www.djangoproject.com/) 中，`select_related`和`prefetch_related`被设计用来阻止由于访问相关对象而导致的数据库查询的泛滥。

我基本上试图弄清楚它如何减少了多少查询，在本文中，我将描述我的发现。

你可以在 GitHub 上找到源代码。

在整篇文章中，我们将使用以下模型:

为了测试我们的功能，我们需要将数据插入到模型中。

为此，我编写了一个管理命令，其中插入了五个出版商、100 本书(每个出版商 20 本书)和 10 个商店(每个商店 10 本书)。跑`python manage.py load_items`就是了。

我编写了一个装饰器来测量函数的执行时间和执行的查询数量。

# 选择 _ 相关

当你要选择的对象是单个对象时，我们使用`select_related`，这意味着向前`ForeignKey, OneToOne`和向后`OneToOne`。

`select_related`的工作原理是创建一个 SQL 连接，并在`SELECT`语句中包含相关对象的字段。因此，`select_related`在同一个数据库查询中获取相关对象。

我们用一个例子来深入探讨一下。

运行该函数后，输出显示:

```
Function :  book_list
Number of Queries : 101
Finished in : 0.08s
```

一个用于填充所有书籍的查询，每次迭代时，我们访问另一个单独查询执行的外键`publisher`。

让我们用`select_related`如下修改查询，看看会发生什么。

运行该函数后，输出显示:

```
Function :  book_list_select_related
Number of Queries : 1
Finished in : 0.02s
```

是不是很神奇？这个查询将 101 减少到 1。这就是`select_related`的作用。

# 预取相关

当我们要得到一个`set`的东西时，我们用`prefetch_related`。

这意味着向前`ManyToMany`和向后`ManyToMany,` `ForeignKey`。`prefetch_related`对每个关系进行单独的查找，并在 [Python](https://www.python.org/) 中执行“连接”。

与`select_related`不同的是，`prefetch_related`使用 Python 而不是在数据库中进行连接。

我们用一个例子来深入探讨一下。

运行该函数后，输出显示:

```
Function :  store_list
Number of Queries : 11
Finished in : 0.02s
```

我们在数据库中有 10 个商店，每个商店有 10 本书。这里发生的是一个获取所有商店的查询，在遍历每个商店时，当我们访问`ManyToMany`字段`books`时，另一个查询正在执行。

让我们使用`prefetch_related`来减少查询的数量。

运行该函数后，输出显示:

```
Function : store_list_prefetch_related
Number of Queries : 2
Finished in : 0.01s
```

这里的查询性能提高了，11 比 2 的查询。我想让你明白`prefetch_related`在这里做什么。

再举一个`prefetch_related`的例子。

在管理命令代码中，我将图书价格从 50 到 300 随机设置。现在，我们会在每个商店里发现昂贵的书(价格 250 到 300)。

运行该函数后，输出显示:

```
Function :  store_list_expensive_books_prefetch_related
Number of Queries : 12
Finished in : 0.05s
```

尽管我们使用了`prefetch_related`，我们的查询增加了而不是减少了。但是为什么呢？

使用`prefetch related`，我们告诉 Django 给出所有要连接的结果，但是当我们使用`filter(price__range=(250, 300))`时，我们改变了主查询，然后 Django 没有为我们连接正确的结果。

这就是为什么我们有 12 个查询，11 个查询迭代存储，一个查询获取预取中的所有结果。

用`Prefetch`来解决问题吧。

运行该函数后，输出显示:

```
Function :  store_list_expensive_books_prefetch_related_efficient
Number of Queries : 2
Finished in : 0.03s
```

任务成功！两个问题，不是 12 个。

# 资源

*   [https://docs.djangoproject.com/en/2.2/ref/models/querysets/](https://docs.djangoproject.com/en/2.2/ref/models/querysets/)
*   [https://docs . django project . com/en/2.2/ref/models/query sets/# django . db . models . query . query set . select _ related](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet.select_related)
*   [https://docs . django project . com/en/2.2/ref/models/query sets/#预取相关](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#prefetch-related)
*   [https://docs . django project . com/en/2.2/ref/models/query sets/# prefetch-objects](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#prefetch-objects)
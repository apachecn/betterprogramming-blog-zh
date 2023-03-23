# Django 数据库优化技巧

> 原文：<https://betterprogramming.pub/django-database-optimization-tips-4e11631dbc2c>

## Django ORM 优化备忘单

![](img/e7c8c7b2a5e1260413be3b1f0c25efb5.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com/s/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在这篇文章中，我将浏览一些关键的优化技巧，而不是讨论每一点。请阅读 Django 官方文档了解全部细节。

在整篇文章中，我们将使用以下模型:

```
class Author(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()

    def __str__(self):
        return self.name

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def __str__(self):
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    body_text = models.TextField(blank=True)
    likes = models.IntegerField(blank=True, default=0)
    authors = models.ManyToManyField(Author, blank=True)

    class Meta:
        default_related_name = 'entries'

    def __str__(self):
        return self.headline
```

# 了解查询集计算和缓存

Django 开发人员必须了解 QuerySet 评估和缓存，以优化数据库命中。我已经写了一篇关于它的详细文章[这里](https://medium.com/@goutomroy/understanding-django-database-querysets-and-its-optimizations-1765cb9c36e5?postPublishedType=repub)。

# 明智地使用 get()

当您知道只有一个对象匹配您的查询时，请使用 get。如果没有条目与查询匹配，`get()`将引发一个`DoesNotExist`异常。如果多个条目匹配查询，`get()`将引发一个`MultipleObjectsReturned`异常。像这样使用`get()`:

```
try:
    one_entry = Entry.objects.get(blog=2000)
except Entry.DoesNotExist:
    # query did not match to any item.
    pass
except Entry.MultipleObjectsReturned:
    # query matched multiple items.
    pass
else:
    # query matched to just one item
    print(one_entry)
```

# 使用可用的调试工具

使用 [django-debug-toolbar](https://github.com/jazzband/django-debug-toolbar) 和 [QuerySet.explain()](https://docs.djangoproject.com/en/3.0/ref/models/querysets/#explain) 来确定代码的效率。了解`django.db.connection`，它记录了用[当前连接](https://docs.djangoproject.com/en/2.2/faq/models/#faq-see-raw-sql-queries)进行的查询。我已经编写了一个查询调试器 [decorator](https://gist.github.com/goutomroy/d61fc8a8445954c71b5585af042e5cf4) 来获取函数中的查询数——您可以用它来检查查询的效率。还有一个很好的包`[django-silk](https://github.com/jazzband/django-silk)`也很好用。

# 尽可能使用迭代器

QuerySet 通常在计算发生时缓存其结果，并且对于使用该 QuerySet 的任何进一步操作，它首先检查缓存的结果。但是当你使用`iterator()`时，它不检查任何缓存，而是直接从数据库中读取结果。它不会将结果保存到 QuerySet。

对于一个 QuerySet，它返回大量具有大量内存的对象，您只需要访问一次，您可以使用`iterator()`。

在下面的代码中，所有条目都将从数据库中取出并加载到内存中，然后遍历每一个条目。

```
q = Entry.objects.all()
for each in q:
    do_something(each)
```

当我们使用`iterator()`时，Django 将保持 SQL 连接打开，读取每一行，并在读取下一行之前调用`do_something()`。

```
q = Entry.objects.all().iterator()
for each in q:
    do_something(each)
```

# 使用持久性数据库连接

每次请求到来时，Django 都会打开一个新的数据库连接，并在请求完成时关闭它。`[**CONN_MAX_AGE**](https://docs.djangoproject.com/en/2.2/ref/settings/#std:setting-CONN_MAX_AGE)` 负责此行为——默认值为 0。但是应该设置为多少秒呢？这取决于你网站的流量——流量越大，保持连接所需的时间就越长。我建议设置一个相对较小的值，如`60`。

# 使用`select_related()`和`prefetch_related()`

在 Django 中,`select_related`和`prefetch_related`都是为了阻止由于访问相关对象而导致的大量数据库查询。我已经写了一篇详细的文章[在这里](https://medium.com/@goutomroy/django-select-related-and-prefetch-related-f23043fd635d)关于它。

# 使用 F 表达式

```
# Don't
for entry in Entry.objects.all():
    entry.likes += 1
    entry.save()

# Do
Entry.objects.update(likes=F('likes') + 1)
```

# 使用聚合

```
# Don't
most_liked = 0
for entry in Entry.objects.all():
    if entry.likes > most_liked:
        most_liked = entry.likes
# Do
most_liked = Entry.objects.all().aggregate(Max('likes'))['likes__max']
```

# 直接使用外键值

Django ORM 自动检索和缓存外键，所以使用它们而不是点击不必要的数据库查询。

```
# Don't. Needs database hit
blog_id = Entry.objects.get(id=200).blog.id

# Do. The foreign key is already cached, so no database hit
blog_id = Entry.objects.get(id=200).blog_id

# Do. No database hit
blog_id = Entry.objects.select_related('blog').get(id=200).blog.id
```

# 不在乎就不要点结果

排序不是免费的-要排序的每个字段都是数据库必须执行的操作。如果一个模型有一个默认的排序(`[Meta.ordering](https://docs.djangoproject.com/en/2.2/ref/models/options/#django.db.models.Options.ordering)`)并且你不需要它，通过调用没有参数的`[order_by()](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet.order_by)`在`QuerySet`上移除它。向数据库添加索引可能有助于提高排序性能。

# 使用`count()`和`exists()`

如果不需要 QuerySet 的内容，使用 count()和 exists()。

```
# Don't
count = len(Entry.objects.all())  # Evaluates the entire queryset

# Do
count = Entry.objects.count()  # Executes more efficient SQL to determine count

# Don't
qs = Entry.objects.all()
if qs:
   pass

# Do
qs = Entry.objects.exists()
if qs:
   pass
```

# 使用批量添加(*objs)到`ManyToManyField`字段

```
author1 = Author(name='author1')
author2 = Author(name='author2')
author3 = Author(name='author3')
entry = Entry.objects.get(id=1)

# Don't
entry.authors.add(author1)
entry.authors.add(author2)
entry.authors.add(author3)

# Do
entry.authors.add(author1, author2, author3)
```

## 对批量操作使用 Delete()和 Update()

如果您想一次删除或更新一组模型实例，请分别使用 delete()和 update()。

```
# Don't.Delete one by one.
for entry in Entry.objects.all():
    entry.delete()

# Do.Delete all at once.
Entry.objects.all().delete()

# Don't
for entry in Entry.objects.all():
    entry.likes += 1
    entry.save()

# Do
Entry.objects.update(likes=F('likes')+1)
```

## 使用 bulk_create()

```
# Don't
for i in range(20):
    Blog.objects.create(name="blog"+str(i), headline='tagline'+str(i))

# Do
blogs = []
for i in range(20):
    blogs.append(Blog(name="blog"+str(i), headline='tagline'+str(i)))
Blog.objects.bulk_create(blogs)
```

# 使用 values()，values_list()，`defer(), only()`

当您需要`QuerySet`结果中的某些字段，并且想要列表、元组或字典中的结果时，使用`[values()](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#values)`和`[values_list()](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#values-list)`。

当您需要`QuerySet`结果中的某些字段，并且想要 QuerySet 中的模型字段而不是列表、元组或字典时，请使用`[defer()](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#defer)`和`[only()](https://docs.djangoproject.com/en/2.1/ref/models/querysets/#only)`。
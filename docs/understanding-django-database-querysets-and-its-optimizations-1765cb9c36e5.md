# 了解 Django 查询集评估和缓存

> 原文：<https://betterprogramming.pub/understanding-django-database-querysets-and-its-optimizations-1765cb9c36e5>

## 通过示例深入探究 QuerySet 的评估和缓存

![](img/dae2a845069ffd68d496a67513e9d054.png)

由[克里斯托弗·高尔](https://unsplash.com/@cgower?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

一个`[**QuerySet**](https://docs.djangoproject.com/en/3.2/ref/models/querysets/#django.db.models.query.QuerySet)`代表数据库中的一组对象。它可以有零个、一个或多个*过滤器*。过滤器根据给定的参数缩小查询结果的范围。在 SQL 术语中，`[**QuerySet**](https://docs.djangoproject.com/en/3.2/ref/models/querysets/#django.db.models.query.QuerySet)`相当于一个`**SELECT**`语句，过滤器是一个限制子句，如`**WHERE**`或`**LIMIT**`。

你通过使用你的模型的`[**Manager**](https://docs.djangoproject.com/en/3.2/topics/db/managers/#django.db.models.Manager)`得到一个`[**QuerySet**](https://docs.djangoproject.com/en/3.2/ref/models/querysets/#django.db.models.query.QuerySet)`。每个型号至少有一个`[**Manager**](https://docs.djangoproject.com/en/3.2/topics/db/managers/#django.db.models.Manager)`，默认叫`[**objects**](https://docs.djangoproject.com/en/3.2/ref/models/class/#django.db.models.Model.objects)`。

查询集可以被构造、过滤、切片，并且通常在不实际访问数据库的情况下传递。在您对查询集进行评估之前，实际上不会发生任何数据库活动。

在整篇文章中，我们将参考以下模型:

```
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def __str__(self):
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    body_text = models.TextField(blank=True)

    class Meta:
        default_related_name = 'entries'

    def __str__(self):
        return self.headline
```

看看下面的例子来理解懒惰:

```
q1 = Entry.objects.filter(blog=2)
q2 = q1.filter(headline__contains='food')
entry_list = list(q3)
```

虽然这看起来像两次数据库命中，但实际上它只命中数据库一次，在最后一行(`entry_list = list(q3)`)。

每次细化一个查询集，都会得到一个独立且不同的查询集，它不绑定到前一个查询集，可以存储、使用和重用。

在下面的代码中，`q`和`q2`执行相同的数据库操作。注意，`q, q1, q2`是三个不同的查询集。

```
q =Entry.objects.filter(blog=2).exclude(body_text__icontains="food")

q1 = Entry.objects.filter(blog=2)
q2 = q1.exclude(body_text__icontains="food")
```

现在我们来谈谈`evaluation`和`caching`。

`Evaluation`表示实际命中数据库。当您开始迭代一个 QuerySet 时，QuerySet 匹配的所有行都从数据库中取出，并转换成 Django 模型。这叫`evaluation`。然后，这些模型被 QuerySet 的内置缓存存储，这样，如果您再次遍历 QuerySet，就不需要再次访问数据库。

# 启用缓存

要在 QuerySet 中启用缓存，只需将 QuerySet 保存在一个变量中并重用它。Django QuerySet 类有一个`_result_cache`变量，它将查询结果(Django 模型)保存在`list`中。`_result_cache`是`None`如果 QuerySet 没有任何缓存(还没有求值)，否则是模型对象列表。当您迭代一个缓存的 QuerySet 时，您基本上是在迭代一个`_result_cache`，它是一个列表。

```
# The following will create two QuerySet's, evaluate them, and throws them away, because they are not saving the queryset anywhere to reuse them later.
print([e.headline for e in Entry.objects.all()])
print([e.pub_date for e in Entry.objects.all()])

# Following code saves QuerySet in a variable. When it evaluates, 
# it saves the results to its cache(_result_cache). 
queryset = Entry.objects.all()
# evaluation with iteration.
for each in queryset:
    print(each.headline)

# Using cache from previous evaluation.
for each in queryset:
    print(each.id)
```

迭代并不是评估的唯一方式，在评估发生时还有很多其他方式，让我们用例子来讨论一下。

# 循环

QuerySet 对象是可迭代的，在开始迭代第一行之前，数据库命中，结果保存在缓存中。在以下示例中，数据库点击和缓存发生在打印第一个标题之前:

```
queryset = Entry.objects.all()    
# Evaluated and cached
for each in queryset:
    print(each.headline)

# Using cache from previous evaluation.
for each in queryset:
    print(each.headline)
```

# **切片**

对未计算的 QuerySet 进行切片会返回一个新的 QuerySet。返回的 QuerySet 不允许进一步修改(例如，添加更多过滤器，或修改排序)，但它允许更多切片。如果对 Queryset 进行迭代，query set(切片或非切片)会将结果保存到其缓存中:

```
# You can't use filter to queryset anymore.
queryset = Entry.objects.all()[10:100]
# You can use filter to q1 but not to q2, q3.
q1 = Entry.objects.all()
q2 = q1[1:10]
q3 = q2[1:5]

# saves results to cache of q1
lst1 = [each.blog.id for each in q1]
# saves results to cache of q2
lst2 = [each.blog.id for each in q2]
```

如果对已经计算过的 Queryset 进行切片，它将返回对象列表，而不是 QuerySet 对象，因为在计算之后，当您再次迭代时，QuerySet 将使用其缓存的(`_result_cache`)值，这是一个列表。

```
queryset = Entry.objects.all()
lst = list(queryset)
# returns a list of entry objects
first_ten = queryset[:10]
# list slicing not queryset slicing because first_ten is a list.
first_five = first_ten[:5]
```

如果您使用 index 从未评估的查询集中选取一个元素，它会导致数据库命中，但是如果您从已经评估的查询集中选取，它会使用缓存。

```
queryset = Entry.objects.all()
# Queries the database because queryset hasn't been evaluated yet.
print(queryset[5])lst = list(queryset)
# Using cache because evaluation happened in previous list() operation.
print(queryset[5])
print(queryset[10])
```

一个例外是，如果您对未计算的 QuerySet 使用 Python slice 语法的`step`参数。在这种情况下，它立即执行查询并返回模型对象的`list`,而不是 QuerySet 对象。

```
entry_list = Entry.objects.all()[1:100:2]
```

# **酸洗/缓存**

如果您 pickle 一个 QuerySet，它将被求值。如果您将 QuerySet 保存在缓存中，它将被求值，下次您使用它时，它将从缓存中为您提供模型对象列表。

# **repr()**

方法返回给定对象的一个可打印的表示字符串。当您对 QuerySet 调用`repr()`时，会对它进行计算，但它不会将结果保存到缓存中。

```
# repr() evaluates but does not saves results to cache.
queryset = Entry.objects.all()
str_repr = repr(queryset)
# Not using cache.Hitting database again.
for each in queryset:
    print(each.headline)
```

注意:`print()`函数也计算 QuerySet，但不将结果保存到缓存中。

# **len()**

当您调用`len()`时，会对 QuerySet 进行评估，并将评估结果保存到缓存中。

```
# len() evaluates and saves results to cache.
queryset = Entry.objects.all()
ln = len(queryset)
# Using cache from previous evaluation.
for each in queryset:
    print(each.headline)
```

注意:如果您只需要知道 QuerySet 中的项数，请不要使用这个选项。Django 为此提供了一个`count()`。

# **列表()**

通过对 QuerySet 调用`list()`来强制对其进行评估，这会返回模型对象列表并将结果保存在缓存中。

```
# Evaluates the queryset and saves results in cache.
queryset = Entry.objects.all()
lst = list(queryset)
# Using cache from previous list() evaluation.
for each in queryset:
    print(each.headline)
```

# **If 语句**

if 语句将导致执行查询并将结果保存在缓存中。如果至少有一个结果，QuerySet 为`True`，否则为`False`。例如:

```
# The `if` statement evaluates the queryset and saves results in cache.
queryset = Entry.objects.all()
if queryset:     
    # Using cache from previous if statement evaluation.     
    for each in queryset:         
        print(each.headline)
```

# 相关的模型属性不会被缓存

当 Django 计算 QuerySet 时，向前或向后关系字段不包括在查询中，因此也不包括在缓存中，除非您使用`select_related`或`prefetch_related`。检查[这个](/django-select-related-and-prefetch-related-f23043fd635d)。

```
queryset = Entry.objects.all()
    for each in queryset:
        print(each.headline)
        # Hits database for blog.
        print(each.blog.name)

    for each in queryset:
        # uses cache
        print(each.headline)
        # No cache, hits database again for blog.
        print(each.blog.name)

    # Use select_related or prefetch_related to cache related objects
    queryset = Entry.objects.select_related('blog')
    for each in queryset:
        print(each.headline)
        print(each.blog.name)

    for each in queryset:
        # uses cache
        print(each.headline)
        # uses cache
        print(each.blog.name)
```
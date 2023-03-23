# 3 保持 Django 代码库干净、简单和可读的重构技术

> 原文：<https://betterprogramming.pub/refactors-you-need-to-know-to-for-your-django-project-8a56b0dee34f>

## 利用姜戈？你需要重构这些东西！

![](img/dc1ef9634f69e2e4341f7448413b22ff.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

> 这篇文章是为了 CSUI 的软件工程项目课程 2022 的个人复习而写的。

# 介绍

重构是你必须为所有项目做的最重要的实践之一。即使当您的项目相对较小和简单时，重构代码总是被鼓励的，并且是更可读、简单和更整洁的代码的最佳实践。在本文中，我将向您展示一些当您选择使用 Django(尤其是 Django REST 框架)作为框架时可以重构的东西！我们走吧！

# 目录

```
1\. [(Django Models) Custom Model Manager](#9754)
2\. [(Django Models) Select Related and Prefetch Related](#fd3d)
3\. [(Django REST Views) Class Based Views with Mixins and Generics](#7a88)
[Conclusion](#4376)
```

在这些例子中，我将使用我为我的软件工程项目课程 2022 制作的这些模型。

# 1.Django 模型—定制模型管理器

> 根据 [Django 文档](https://docs.djangoproject.com/en/4.0/topics/db/managers/)，“Django 中的模型管理器是一个接口，通过它向 Django 模型提供数据库查询操作。”

您可能没有意识到这一点，但是您可能在使用 Django 模型时使用过 Django 的模型管理器。当你写`Term.objects.all()`的时候，默认管理器中间的‘对象’是 Django 提供给所有模型的。

事情变得有趣起来:您可以创建自己的定制模型管理器，它可以为您和您的模型做其他方便的事情。

例如，如果我想获取某个特定学期的所有课程(即学期“2021/2022–1”)，并且我想按课程名称对它们进行排序，那么通常我们可能会这样写:

```
Course.objects.filter(term__name="2021/2022-1").order_by("name")
```

老实说，这没有什么大问题，但是如果你在代码中多次使用这种精确的过滤和排序，你会发现你会一遍又一遍地重复代码。这种冗长的筛选和排序会降低代码的可读性。如果您使用自定义模型管理器和 QuerySet，情况会有所改善，例如:

不要忘记将新的定制管理器分配给模型。您可以覆盖默认的“对象”管理器，或使用不同的名称创建新的自定义管理器。为此，我将覆盖默认的“对象”管理器。

有了这个，你可以像以前一样写这个:

```
Course.objects.get_by_term_name_with_ordered_name("2021/2022-1")
```

您可以看到它比之前的代码可读性更好，而且更简单，没有重复`.filter`和`.order_by`。当然，这个例子可能看起来并没有改善多少代码，但是您可以根据自己的需要使用定制管理器。这可以显著提高代码的可读性，并防止重复代码。

# **2。Django 型号选择相关和预取相关**

你可能还不知道`select_related`和`prefetch_related`，但是 Django 提供的这些功能真的很有用。在我看来，这是提高数据库查询性能的必备工具。这是因为当您已经向 QuerySet 查询了一组对象，然后您想要访问它的外键对象的属性时，Django 将再次查询数据库，以获得您正在调用的每个对象的每个外键属性。

猜一猜:如果执行下面的代码，Django 会对数据库进行多少次查询？

```
courses = Course.objects.filter(credit=4) #for example returns 2 objfor course in courses:
  print(course.term.name)
```

你可能认为应该是一个，对吗？在执行`Course.objects.filter`时应该查询数据库？对不起，但是那个答案是不正确的。正确答案是将有三次数据库查询调用。

怎么会？显然，第一个查询在过滤器上。之后，因为 queryset 中有两个`Course`对象，`courses`，并且代码要求外键的属性，(`course.term.name`，那么对于`for`循环的每一次迭代，Django 将对数据库进行一次额外的查询调用。

这是因为首字母`Course.objects.filter`没有术语名称的信息。想象一下，如果你正在做 1000 次或者更多的迭代。光是这个你就要查询数据库 1000 次。

为了防止这种情况发生，您可以使用`select_related`和`prefetch_related`来获取初始过滤器查询的外键属性。您可以搜索这两者之间的差异，但简而言之，当对象属性是单个对象(如`OneToOneField`或`ForeignKey`)时，您可以使用`select_related`，而当您要获得多个单个对象或一组事物时，您可以使用`prefetch_related`(`ManyToManyFields`或反过来`ForeignKey`)。

```
courses = Course.objects.filter(credit=4).select_related("term")for course in courses:
  print(course.term.name)
```

通过添加`select_related("term")`，您将在第一次查询中查询`courses`对象的外键“term”的所有信息。因此，Django 不需要为`fo` r 循环的每次迭代查询数据库。

这将显著提高您的数据库查询性能(更像是防止大量的数据库查询)，并且我个人认为，通过明确地告诉读者您稍后将需要某个外键的这些属性，这将使您的代码更具可读性。

# **3。Django REST 视图——带有混合和泛型的基于类的视图**

> 注意:我将讨论 Django REST 框架的基于类的视图。Django 的基于类的视图有点不同，你可以查看 Django 文档[了解更多信息](https://docs.djangoproject.com/en/4.0/topics/class-based-views/)

许多 Django 用户可能已经熟知基于类的视图。但在我看来，你应该适度使用基于阶级的观点。对于复杂的视图，我认为基于类的视图可能会降低可读性(对于那些不熟悉基于类的视图的人来说)并且缺乏灵活性。

但是对于简单的视图，尤其是 Django REST Framework (DRF)中的 REST 视图，您肯定应该使用基于类的视图和所提供的 mixins 和泛型，因为这要简单得多，需要的代码更少，并且提高了可读性(对于那些已经知道基于类的视图的人来说)。

例如，对于基于函数的视图，我会像这样写`GetAndDeleteTermViews`，记住你应该首先为你的模型创建序列化器来序列化数据。在这种情况下，我将我的名字命名为`TermSerializer`:

上面的代码在功能上没有大问题。它应该能正常工作。但是你可以看到更复杂的逻辑，它会变得更加混乱。将会有更多的代码行，和/或更多的 if 和 elifs。这将降低代码的可读性。下面是基于类的视图、混合和泛型的相同功能:

如您所见，它更加简单，可读性更好。另一个优点是所有可能发生的错误(比如对象不存在时的 404)都已经被 Django 处理了。对于对基于类的视图有基本了解的读者来说，这将花费更少的开发时间并提高可读性。

# 结论

我认为这些是你在 Django 项目中必须考虑的基本重构。这些绝对不是你需要重构的所有东西。你应该搜索更多需要重构的东西，但是我发现很多刚接触 Django 的人并不了解这些东西——比如 Django 中的 model manager 和`select_related` / `prefetch_related`。

你在重构代码上付出的所有努力肯定会得到回报，得到更干净、简单、可读和改进的代码。

# 参考

 [## 经理| Django 文档| Django

### 默认情况下，Django 会向每个 Django 模型类添加一个名为的。但是，如果您想将用作字段名，或者如果…

docs.djangoproject.com](https://docs.djangoproject.com/en/4.0/topics/db/managers/)  [## QuerySet API 参考| Django 文档| Django

### Django 提供了一系列的细化方法，可以修改返回的结果类型，也可以修改

docs.djangoproject.com](https://docs.djangoproject.com/en/4.0/ref/models/querysets/#select-related)  [## 基于 3 类的视图- Django REST 框架

### 我们也可以使用基于类的视图来编写 API 视图，而不是基于函数的视图。正如我们将看到的，这是一个…

www.django-rest-framework.org](https://www.django-rest-framework.org/tutorial/3-class-based-views/)
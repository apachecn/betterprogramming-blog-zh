# 增强 Django 开发技能的 4 个中级技巧

> 原文：<https://betterprogramming.pub/4-intermediate-tips-to-power-up-your-django-development-skills-7cf91579aaba>

## 通过这些技巧成为更好的 Django 开发人员

![](img/c560b663746027d57b822449c9817aa8.png)

由[费萨尔](https://unsplash.com/@faisaldada?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Django 是全球 Python 开发者中最受欢迎的框架之一，许多知名网站都建立在它的基础上，如 Instagram、Pinterest、Mozilla 和 [more](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Introduction#how_popular_is_django) 。

虽然有很多很棒的教程可供学习成为更好的 Django 开发人员，但大多数都是面向初学者的。

在本文中，我将与您分享一些中级技巧，让您成为更好的 Django 开发人员，同时为您提供可操作的步骤或资源，我发现这些对我的 Django 学习之旅非常有帮助。

# TL；速度三角形定位法(dead reckoning)

*   编写测试。
*   利用`select_related`和`prefetch_related`。
*   利用调试工具。
*   避免重新发明轮子。

# 开始编写测试

当我开始编程时，我没有意识到[编写测试](https://docs.djangoproject.com/en/3.2/topics/testing/overview/)的重要性。我早期学习 Django 的教程也没有强调这一点。

一开始，编写测试可能会超级烦人和乏味。通常情况下，编写测试并不能立即帮到你很多。

然而，当添加新功能或重构代码时(相信我，这是会发生的)，特别是当它已经过去了几个月的时候，这些测试将为开发人员提供一个安全网，这样他们就不会在不知不觉中给你的软件带来新的错误。

迄今为止，我无法计算单元测试和集成测试为我和我的团队节省了多少时间。

## “服从试验山羊”

就我个人而言，我强烈推荐阅读臭名昭著的 [*服从测试山羊*](https://www.obeythetestinggoat.com/pages/book.html#toc) 的书。这是一本很好的书，除了使用 Django 的测试驱动开发( [TDD](https://en.wikipedia.org/wiki/Test-driven_development) )之外，它还教了你很多东西。

我注意到很多人经常因为这本书使用了 Django 的旧版本而感到沮丧。然而，我仍然推荐使用 Django 的最新版本继续阅读这本书，因为你可以同时学习如何更好地调试。你会在某个时候陷入困境，但是相信我，这种挣扎是非常值得的。

# 使用 select_related 和 prefetch_related 优化查询

如果您正在走出 Django 教程的地狱，这是一个学习和思考 SQL 查询效率的好时机。他们的执行速度有多快？他们如何执行得更快？

当数据库表包含大量行时，使用 Django ORM 进行高效的 SQL 查询是至关重要的，因为这极大地影响了应用程序的整体性能。

如果您还没有遇到过`select_related`和`prefetch_related`(或者您可能还没有习惯使用它们)，您肯定应该遇到，因为这两种方法都旨在放弃不必要的数据库查询。

## 选择 _ 相关

*   简而言之，`select_related`通过用 SQL `SELECT`语句中相关对象的列创建 SQL `JOIN`来工作。
*   当您查询的对象是带有“forward”`ForeignKey`或`OneToOne`的单个对象时，使用`select_related`。

## 预取相关

*   另一方面，`prefetch_related`为每个关系执行单独的查找，并在 Python 中执行“连接”。
*   当你要得到一些东西的时候用这个。
*   遇到`ManyToManyField`或倒车`ForeignKey`时使用`prefetch_related`。

如果你对“向前”或“向后”`ForeignKey`的含义感到困惑，这里有一个例子:

“正向”和“反向”外键示例

## 举例学习

我强烈推荐阅读并尝试下面由 Goutom Roy 写的文章中的例子。

本文提供了简明实用的示例，说明如何使用`prefetch_related`和`select_related`在执行时间和执行的查询数量方面优化数据库查询:

[](/django-select-related-and-prefetch-related-f23043fd635d) [## Django 选择相关和预取相关

### 通过示例检查使用这些方法减少了多少查询

better 编程. pub](/django-select-related-and-prefetch-related-f23043fd635d) 

# 利用调试工具

今天，Django ORM 经常被指责进行非优化查询。为了确定解决这个问题的正确方法，开发人员通常需要用手头的正确工具来确定瓶颈。

## 打印 qs.query

首先，我们可以简单地看到 Django ORM `queryset`通过使用:

如何打印 SQL 语句的示例

## 使用 django-调试-工具栏

我强烈推荐使用`[django-debug-toolbar](https://github.com/jazzband/django-debug-toolbar)`。这个简单的包可以提供关于底层请求的各种调试信息。

## 奖励:使用姜戈丝绸

这里的另一个选择是使用 `[django-silk](https://github.com/jazzband/django-silk)`来分析 Django web 应用程序，以寻找高影响性能的瓶颈，这样我们就可以开始优化它们以获得更好的性能结果。我强烈推荐阅读[这篇文章](https://medium.com/hackernoon/finding-high-impact-performance-bottlenecks-django-tips-237a896e0f91)来学习更多关于使用`django-silk`的知识。

# 避免重新发明轮子

在处理一系列问题时，人们通常会先写出自己的解决方案。从我卑微的经历来说，我总是先尝试谷歌一下手头的问题。

由于 Django 是一个拥有活跃社区的免费开源框架，因此很可能已经存在一个功能丰富的解决方案。

最后，如果你正在开始一个新的 Django 项目，我建议看看`[cookiecutter-django](https://github.com/pydanny/cookiecutter-django)`，因为它附带了一个定制用户模型，通过`[django-allauth](https://github.com/pennersr/django-allauth)`注册，通过 [Anymail](https://github.com/anymail/django-anymail) 发送电子邮件，以及许多其他有用的生产就绪功能。

查看与 Django 相关的令人敬畏的图书馆列表。

# 最后的想法

像许多其他人一样，我做了太多的教程。不要。相反，尝试从头构建一个 Django 项目，因为这是精通 Django 的最快方法。

## 应对“我不知道该建什么”

寻找项目想法的最好方法是想一个能解决你身边问题的个人项目。是的，你的项目可能会很糟糕。我知道我的有。

尽管如此，随着时间的推移，你会很快注意到你寻找答案的方式会发生变化。你将开始在 Django 的文档中搜索特定的关键字，而不是在 Google 上用模糊的问题反复推敲，最终更有效地在 Stack Overflow 和其他任何地方找到正确的答案。

通过练习和投入时间，你会变得擅长(几乎)任何事情。我希望你喜欢阅读这篇文章。保重！

## [在 jerrynsh.com 阅读我的其他 Python 文章](http://jerrynsh.com/)

[](/how-to-write-clean-code-in-python-5d67746133f2) [## 如何用 Python 写干净的代码

### 使用 Python 示例编写干净代码的 3 个技巧

better 编程. pub](/how-to-write-clean-code-in-python-5d67746133f2) [](https://medium.com/geekculture/top-3-backend-developer-productivity-tools-that-you-should-check-out-bc16bff11133) [## 你应该看看的三大后端开发人员生产力工具

### 在 5 分钟内阅读我作为后端开发人员用来提高生产力的工具

medium.com](https://medium.com/geekculture/top-3-backend-developer-productivity-tools-that-you-should-check-out-bc16bff11133)
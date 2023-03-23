# Django 注释和聚合

> 原文：<https://betterprogramming.pub/django-annotations-and-aggregations-48685994d149>

## 让我们修改我们的注释和聚合知识

![](img/7380917bdebc763ad3e4da61eb33537b.png)

[Adeolu Eletu](https://unsplash.com/@adeolueletu?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 议程

Django 执行 SQL `group by`的机制是通过`annotate`和`aggregate`。

在这篇文章中，让我们复习我们的注释和汇总知识。

这篇文章是初学者友好的，即使你以前没有使用过 Django 注释，你也应该能够理解。

# 模型

让我们考虑本教程的以下模型:

模型

我们计划在这一块计算以下内容:

*   问题数量
*   多个选项
*   每个问题的选择数量
*   根据 question_text 过滤后每个问题的选择数量
*   每个问题的投票数
*   最高票数的问题
*   最低票数的问题
*   没有选择的问题
*   投票总数
*   每个选项的平均投票数
*   每个问题的平均投票数
*   每个`question_text`的问题数量
*   每个`question_text`的选择数量

# 聚合和注释

我们可以通过运行以下命令来查找问题的数量:

问题数量

要计算选项的数量:

多个选项

要计算每个问题的选项数量:

每个问题的选择数量

现在每个问题都有一个属性叫做`choice_count`。

现在让我们在`question_text`上过滤后应用分组:

过滤问题后每个问题的选择数量

要计算每个问题的投票数:

每个问题的投票数

这将对每个问题的所有选项进行分组，然后对每组的投票进行求和。

让我们找出最高票数的问题:

得票最多的问题

在`num_votes__isnull=False`上过滤很重要。我们过滤掉没有任何选择的问题，因为它们也出现在`Question.objects.annotate(num_votes=Sum('choice__votes'))`的结果中。

让我们找出票数最少的问题:

最低票数的问题

要找到没有任何选择的问题:

没有选择的问题

要计算投票总数:

投票总数

要计算每个选项的平均投票数:

每个选项的平均投票数

要获得每个问题的平均投票数:

每个问题的平均投票数

我们可以用同一个`question_text`提出多个问题。让我们按`question_text`对问题进行分组，并找出每个`question_text`的问题数量:

每个问题的问题数量 _text

# 签署

感谢阅读！请继续关注更多 Django 的帖子。

在 [Twitter](https://twitter.com/_akshar) 上与我联系，我[在 Twitter 上发布](https://twitter.com/_akshar)关于信息丰富且有价值的编程文章和建议。
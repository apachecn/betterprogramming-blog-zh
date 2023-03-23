# Django 快速提示:上下文处理器

> 原文：<https://betterprogramming.pub/django-quick-tips-context-processors-da74f887f1fc>

## Python 中更好的模板

![](img/8eb0e82f66eb37396e05528886e296bc.png)

[毗湿奴 R 奈尔](https://unsplash.com/@vishnurnair?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

让我们讨论一下上下文处理器，看看如何在您的 Django 项目中有效地使用它们。

> 上下文处理器有一个简单的接口:它是一个 Python 函数，接受一个参数，一个 HttpRequest 对象，并返回一个添加到模板上下文中的字典。每个上下文处理器必须返回一个字典。
> 
> 自定义上下文处理器可以位于代码库中的任何位置，只要它们被引擎的 context_processors 参数指向。“— [姜戈的文件](https://docs.djangoproject.com/en/3.0/ref/templates/api/)

当使用多个独立的`views`时，您可能会将不同的数据对象加载到不同的视图中，但是有时用例要求您在整个应用程序中使用相同的数据——我指的是在应用程序的所有模板中。在那种情况下你会怎么做？初学者可能会重复将数据加载到每个视图的模板上下文中的过程，这不是很聪明，对吗？

这正是自定义上下文处理器非常有用的地方。

我们马上就会看到如何将它们付诸实践，但让我们定义一个用例，让您轻松掌握我们将要实现的内容。

# 用例

您正在开发一个课程管理系统或 LMS，您需要为可用的课程主题实现一个搜索栏，并且该搜索栏应该在整个应用程序中都可用。为了这个快速教程，我们将简单地使数据在 HTML `<option>`或`<ul>`标签中可用，以列出所有可用的主题。

如果你是一个中级 Django 开发人员，你可能知道 Django 模板语言[，但是如果你不知道也不用担心。请记住，Django 让您能够创建一个基础模板，然后通过简单地扩展基础模板来添加其他模板。这是您通常加载所有静态文件的地方。我包含了链接，所以你可以查找它。](https://docs.djangoproject.com/en/3.0/ref/templates/language/)

# 履行

要创建自定义上下文处理器，请在应用程序文件夹中添加一个新文件。我们称之为`custom_context_processor.py`:

```
$ touch your_app/custom_context_processor.py
```

现在，让我们导入我们的模型并定义我们的函数:

```
from .models import Subjectdef subject_renderer(request):
    return {
       ‘all_subjects’: Subject.objects.all(),
    }
```

这再简单不过了。你可能会问，“到底是怎么回事？”是的，就这么简单。

让我们回顾一下这个定义，这样你就会明白它有多简单。

上下文处理器是一个接口:

*   `subject_renderer(request)`:一个 Python 函数，它接受一个参数，这个参数是一个请求对象。
*   `all_subject` : 一个的可用科目列表作为字典返回。

这就是我们的上下文处理器实现，这对于我们的用例来说已经足够了。

现在，为了使它在我们的模板中可访问，我们需要将它添加到我们模板的上下文中，所以打开`settings.py` 并像这样添加它:

我们已经在所有模板中提供了该功能。现在是渲染`_base.html`并显示主题列表的时候了:

现在，您可以浏览所有的应用程序模板，并且所有的主题仍然可见或可访问。

我希望这足够简单。如果事实并非如此，请随意回复。我很乐意再给你演示一遍。

干杯。
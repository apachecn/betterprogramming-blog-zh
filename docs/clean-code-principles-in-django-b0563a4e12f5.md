# Django 的 4 项廉洁守则原则

> 原文：<https://betterprogramming.pub/clean-code-principles-in-django-b0563a4e12f5>

## 在 Django 中清理(您的)代码

![](img/4f344d29d14e1350fa409912ad8d8df0.png)

[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

> *这篇文章是为了 CSUI 的软件工程项目课程 2022 的个人评论而写的。*

# 介绍

打扫卫生可能不是你最喜欢的消遣活动，当然也不是我的。不管是打扫你的房间还是清理你的代码，这可能都不是一个好时机。但是他们两个有一个共同点。这很重要。

# 内容

1.  [干净代码的一些原则](https://medium.com/@rico098098/clean-code-principles-in-django-b0563a4e12f5#810d)
2.  [姜戈之吻](https://medium.com/@rico098098/clean-code-principles-in-django-b0563a4e12f5#7ed5)
3.  [干在姜戈](https://medium.com/@rico098098/clean-code-principles-in-django-b0563a4e12f5#f190)
4.  [姜戈的 YAGNI](https://medium.com/@rico098098/clean-code-principles-in-django-b0563a4e12f5#c86c)

# 1.干净代码的一些原则

正如你可能已经知道的，干净的代码与计算机或编译器无关，它与人类有关。对于计算机来说，只要语法正确，逻辑正确，你怎么写代码一点都不重要。但人类不是计算机。你的同事和其他程序员不一定能读懂你的代码，尤其是如果你没有写出干净的代码。

一些最著名的干净代码原则是:

1.  亲吻(保持简单和愚蠢)
2.  干(不重复)
3.  YAGNI(你不会需要它)

在接下来的部分中，我将尝试在 Django 中展示这些原则的例子。

# 2.姜戈之吻

KISS(保持简单(和)愚蠢)基本上是告诉你保持你的代码简短，简单，并且容易被其他人阅读。

## 使用 Django 表单、序列化器、权限和装饰器，而不是手工替代

当学习 Django 时，你可能已经学习了 Django 格式、序列化器和许多其他东西，就像我很久以前做的那样。

但有时，我发现自己质疑使用这些东西的好处。你就不能为表单写几行 HTML 代码吗？或者在视图中编写更多的代码行，而不是制作序列化器或权限？

是的，你可以做同样的事情。但是因为你能做到，并不意味着你应该去做。

首先，手工 HTML 表单是不安全的！通过使用 Django 表单，您的表单会更安全，因为它使用 CSRF 令牌作为跨站点请求伪造保护。您可以自己搜索，但是从 clean code 的角度来看，它也不适合手工 HTML 表单。请看这里的对比:

没有 Django 表单:

app/views.py

使用 Django 表单:

app/forms.py

app/views2.py

你可能会想，“等一下，Django 格式的代码不是更长吗？”。是的，如果包括 forms.py 文件，时间会更长。但是如果只比较 views.py 文件，您会发现带有 Django 表单的文件比没有 Django 表单的文件更简单、更容易阅读。这是因为即使第一个代码片段中的代码(组合)较长，它也会被整齐地分开。这意味着你可以更容易地推断出应该做什么。

这就是接吻原则的精髓。您需要使代码简单，易于他人阅读，即使您最终会编写更多的代码或不得不考虑更多。从这里学到的最重要的事情是为他人编写简单易读的代码。

## 使用基于类的视图，而不是基于函数的视图

必要时使用基于类的视图，而不是基于函数的视图。这是因为即使基于函数的视图比它的对应部分更灵活，基于类的视图在长的和冗余的函数/视图上更具可读性。

我不会解释太多，因为大多数人可能都知道基于类的视图，但是你可以在 Django [这里](/refactors-you-need-to-know-to-for-your-django-project-8a56b0dee34f)看看我关于重构的另一个故事。其中有一节对 Django REST 框架中基于类的视图做了进一步的介绍。

# 3.在姜戈干

干还是不要重复自己是一个不言自明的道理。你不应该在你的代码中重复你自己。你可以在 Django 做一些事情来减少冗余:

## 使用装饰器来限制对视图的访问

如果你以前没有使用过装饰器，你可能以前在你的视图中写过这个:

```
if request.user.is_authenticated:
```

如果在一个文件中有多个视图，它可能看起来像这样:

app2/views.py

那么，如果我告诉你有一个更好的方法，不需要一遍又一遍地复制粘贴和重复你自己，会怎么样呢？以下是装饰者的做法:

app2/decorators.py

app2/views2.py

哇！酷吧？您还可以看到，在装饰器中，您可以编写任何您想要的定制代码。所以这不仅仅是为了限制对视图的访问或者将你重定向到某个地方。你可以在用户进入视图之前保存一些东西到用户的会话中，比如 referer URL 路径，或者你能想到的其他东西。天空才是极限！

## 将经常使用的代码放在单独的助手函数和单独的文件中

这是另一个对我们所有人来说都很容易实现的方法！将经常使用的代码提取到一个单独的 helper 函数中，并放入`utils.py`文件中。

不使用助手功能发送电子邮件的示例:

app3/views.py

使用助手功能发送电子邮件的示例:

app3/utils.py

app3/views2.py

老实说，差别非常明显，越复杂的函数越需要它。

# 4.姜戈的 YAGNI

YAGNI 或者你不需要它这是一个告诉我们不要超越自己的原则。有时我，也许还有你们中的一些人会为“未来”写代码。也许我们以后会需要这个或那个，所以最好现在就写，对吗？没有。对于干净的代码来说，这确实是一个坏习惯，因为大多数情况下，**你不需要那些代码**。

例如，我将回到我们之前讨论过的装饰者。可能在得知我们可以做`@login_required`装修工之后，我们想再做一个给`qualified`的用户。

嗯，我们还没有真正实现这个`qualified`的东西，但也许我们现在会为‘未来’写它。

app4/decorators.py

嗯…..结束了。

两年后…

“这个‘合格的’装修工是什么东西？”

是的，你可能已经忘了，也从来没用过。那是因为你可能一开始就不需要它。因此，从中得出的结论是，除非认为有必要，否则不应该添加功能或任何其他代码，除非您希望您的项目有一个臃肿而混乱的代码库。

# 参考资料和资源

 [## 跨站点请求防伪| Django 文档| Django

### CSRF 中间件和模板标签提供易于使用的保护，防止跨站点请求伪造。这种类型的…

docs.djangoproject.com](https://docs.djangoproject.com/en/4.0/ref/csrf/)  [## 发送电子邮件| Django 文档| Django

### 虽然 Python 通过模块提供了邮件发送接口，但是 Django 在它上面提供了几个轻量级的包装器…

docs.djangoproject.com](https://docs.djangoproject.com/en/4.0/topics/email/)  [## 查看装饰者| Django 文档| Django

### Django 提供了几个装饰器，可以应用于视图来支持各种 HTTP 特性。请参见装饰…

docs.djangoproject.com](https://docs.djangoproject.com/en/4.0/topics/http/decorators/)  [## 基于类的视图| Django 文档| Django

### 视图是一个接受请求并返回响应的可调用对象。这不仅仅是一个函数，Django…

docs.djangoproject.com](https://docs.djangoproject.com/en/4.0/topics/class-based-views/)
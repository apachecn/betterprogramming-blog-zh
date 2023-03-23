# 作为 Python 变量的表情符号？当然，为什么不呢！

> 原文：<https://betterprogramming.pub/emojis-as-python-variables-sure-why-not-96ce955dada1>

## 利用 pythonji 库编写 python 代码的一种奇怪的令人满意的方式

![](img/67e449f5b633149e272a8ed5f44b0e3a.png)

照片由[吴仪](https://unsplash.com/@takeshi2?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

表情符号可以表达很多，为什么不用它们来写代码，听起来很可笑吧？有很多文章提倡在 git commit messages 和 [comments](https://dev.to/fbnlsr/lets-talk-about-emojis-2788) 中使用[表情符号，以提高整体沟通效能。](https://gitmoji.dev/)

Python 不是用来处理作为变量名的表情符号的——开发者不会优先考虑这个特性。然而，名为`[pythonji](https://pypi.org/project/pythonji/)` [](https://pypi.org/project/pythonji/)的库使用户能够这样做。用户不仅可以使用表情符号作为变量名，还可以在导入语句中用作别名。

在本文中，你将学习如何使用表情符号编写代码并执行它。除此之外，您还将看到代码是如何被 python 解释器理解的。

# 装置

Pythonji 仅适用于 3.6 ≤ Python ≤ 3.8 版本。

```
python -m pip install pythonji
```

# 编写表情代码

编写包含表情符号的代码相当简单，请看下面的例子。

巨蟒龟代码创建一个广场

将此文件另存为`square.🐍` 是的，你必须使用 python 表情符号作为扩展才能使用`pythonji`。这里，我使用了`.py`扩展来突出显示语法。这个文件不能被 Python 解释器直接理解，`pythonji`充当中间件，用代码中的表情符号代替变量名。

使用`pythonji`执行文件:

```
pythonji square.🐍
```

# 例子

## 1.使用枕头阅读和显示图像

pythonji PIL 代码打开一个图像文件

## 2.将图像转换成 JPG

pythonji 代码来转换 jpg 格式的图像

# 理解表情代码

正如我们所知，python 无法理解我上面展示的任何代码，所以`pythonji`必须通过用变量名替换所有表情符号来进行翻译。让我们以这个片段为例:

来源:[巨蟒实例](https://pypi.org/project/pythonji/)

通过对`pythonji`源代码做一些修改，我们可以看到上面的代码是如何被 python 解释器理解的:

pythonji 的翻译版本

# 结论

这是一种非常规的编写代码的方式，我相信没有人在他们的生产环境中有这种方式。

我不知道`pythonji`的维护者是否会把这当成一个严肃的项目，或者这只是为了好玩，但是我们可以从他们的源代码中学习，即使对于新手来说，这也是清晰易懂的。

感谢阅读。下次见！
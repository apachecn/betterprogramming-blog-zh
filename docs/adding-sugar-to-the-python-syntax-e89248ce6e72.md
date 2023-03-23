# 扩展 Python 语法

> 原文：<https://betterprogramming.pub/adding-sugar-to-the-python-syntax-e89248ce6e72>

![](img/49fc0988dc3ffcb97a7ced5287dfc441.png)

([来源](https://unsplash.com/photos/72vLY-QMd3w))

## 如何用主包装器给 Python 语法添加糖

我爱 Python。这是一种非常强大的语言，很容易编写和理解。它也有一个广泛的图书馆生态系统，允许你做你能想到的几乎任何事情([甚至飞行](https://xkcd.com/353/))。最近，它已经成为数据科学和机器学习的通用语言。我在日常工作中经常使用它，所以我可以说我相当精通它(毕竟我在 Google 有 Python 可读性😏 ).

然而，Python 并不完美。我最近开始在空闲时间学习 Lisp。在学习的时候，我偶然发现了 Lisp 的一个我认为非常酷(但是很小)的特性。在定义函数时，Lisp 允许使用以前的参数作为默认值。

例如，假设您正在编写一个`make_rectangle`函数，它将左上角的`x, y`坐标、`width`和`height`作为输入，并返回矩形四个角的`x, y`坐标。在 Python 中，我们可以很容易地做到这一点:

函数在 Python 中制作一个矩形。

现在假设我们希望函数返回 squares，如果它只通过了`width`参数。如果您以前从未编程过，您可能会认为合理的做法是设置`height=width`，就像这样:

允许“make_rectangle”处理正方形的合理但无效的语法。

不幸的是，它不是有效的 Python。如果您尝试运行它，您会得到一个错误:

```
NameError: name 'width' is not defined.
```

我个人觉得这是语言的败笔。当然，绕过语法并获得类似的行为是很容易的:

允许“make_rectangle”处理正方形的 Pythonic 方式。

但那让我很生气！语言应该做我想做的事情，而不是相反。我没有屈从于 Python 的意志，而是创建了一个`__main__`包装器，让我编写自己想要的代码。

和往常一样，我的 GitHub 上有完整的工作代码[。](https://github.com/EugenHotaj/python-meta)

# *主包装纸*

`__main__`包装器是一个 Python 程序，它包装另一个 Python 程序的`__main__`函数。包装器可以在调用主程序之前运行任何它想要的代码。原来很多工具都是这么写的，比如 [pdb](https://docs.python.org/3/library/pdb.html) 、 [profile](https://docs.python.org/2/library/profile.html#module-profile) 等。如果你对所有的细节感兴趣，大卫·比兹利有一个关于这个话题的有趣的演讲。

因为包装器在主程序之前运行，所以它有可能在执行主程序之前修改主程序的代码。这意味着我们可以使用`__main__`包装器将类似`improved_make_rectangle.py`的代码转换成类似`valid_make_rectangle.py`的有效 Python。这样，我们可以用更令人愉快的语法编写代码，同时 Python 解释器仍然可以正确地执行它。

## 论方法

*(其他可能方法的快速切线；随意跳过。)*

我想了很多如何在没有包装器的情况下添加这个功能，也许是通过函数装饰器、上下文管理器等等。然而，我意识到这是不可能的，因为`improved_make_rectangle.py`不是有效的 Python。

我考虑探索的另一个途径是修改 Python 解释器本身。我不采用这种方法主要有两个原因:

1.  这听起来要乏味得多，因为您必须修改底层的 C 代码。
2.  它的可移植性较差，因为它涉及到派生 Python 解释器。任何想要使用新语法的人都只能通过分叉解释器来实现。相反，`__main__`包装器只是普通的 Python。

如果你知道实现这个功能的更好的方法，请在评论中告诉我。我很有兴趣一探究竟！

# 扩展 Python 语法

我们需要做的第一件事是创建一个能够运行其他程序的包装器。如[1]所示，这相当简单:

编写` __main__ '包装器的通用模板。

当我们执行第 17 行的主程序时，大部分代码只是确保 Python 环境设置正确。有趣的部分发生在第 7–10 行:这里我们读取主程序代码，`preprocess`(即重写)它成为有效的 Python，并编译它以执行。我们现在可以像这样运行任何 Python 程序来包装它:

```
python3 -m main_wrapper some_random_program.py
```

当然，`preprocess`函数是我们自己写的。我们要做的是遍历主程序中的每一行代码，并找到所有函数头，即所有以`def` 开头的行(忽略任何前导空格)。然后，对于我们找到的每个头，我们想看看它的参数是否引用了以前定义的参数。如果是这样，我们想通过将它们设置为`None`并将`argument = argument or prev_argument`添加到函数定义的顶部，将它们转换成`make_rectangle_valid.py`的等价物。这在代码中是这样的:

将我们的扩展语法转换成有效 Python 的预处理器。

我们做了很多隐含的假设，这些假设需要在更健壮的实现中解决。例如，我们假设主程序的代码为每个缩进级别使用 4 个空格字符(第 9 行)，并且整个代码遵循 PEP 8 样式指南(否则，如果`kwargs`在`=`周围有空格，比如`some_arg = 12`，第 12 行将失败)。虽然我们可以通过一些努力来放松这些假设，但我们在这里已经迈出了良好的第一步。

最后，您可能已经注意到了第 10 行对`_buid_arg_to_prev_arg`函数的神秘调用。该函数负责解析函数头，并提取引用先前定义的参数的所有参数。为此，它首先删除了所有不是函数参数的内容，比如空格、括号、`def`语句等。然后，对于每个参数，它检查是否引用了任何以前定义的参数，如果是，就将其添加到字典中。最后，字典被返回。这转化为以下代码:

一种从参数到先前定义的参数建立字典的函数。

我们现在已经有了让包装器工作的所有组件。这里是`improved_make_rectangle.py`代码，同样带有一个简单的`__main__`方法来检查矩形和正方形的实现:

把这些放在一起。

和以前一样，在没有包装器的情况下运行会引发`NameError:`

```
$ python3 wrapper_test.py
Traceback (most recent call last):
  File "wrapper_test.py", line 1, in <module>
    def make_rectangle(x, y, width, height=width):
NameError: name 'width' is not defined
```

但是，当我们使用包装器运行时，消息会在终端中正确地打印出来:

```
$ python3 -m wrap_main wrapper_test.py
Rectangle: ((0, 0), (100, 0), (100, 200), (0, 200))
Square: ((0, 0), (100, 0), (100, 100), (0, 100))
```

# 结论

在本文中，我们通过编写一个`__main__`包装器给 Python 添加了一些额外的语法糖。就目前的情况来看，这个包装器相当脆弱，如果您试图在现实世界中使用它，它会非常失败。此外，为了去掉一些多余的行，我们不得不编写大量的代码。所以，综合考虑，包装可能不值得。然而，`__main__`包装器本身是一种通用的元编程技术，它允许您扩展 Python 来做非常酷和有趣的事情。如果没有别的，他们写起来很有趣🙂。

感谢阅读！

欧根·霍塔吉
2019 年 7 月 15 日

为了简洁起见，我在这篇文章中跳过了一些次要的东西。如果你想要完整的细节，请在我的 GitHub 上查看完整的工作代码[。](https://github.com/EugenHotaj/python-meta)

如果你喜欢这篇文章，那就关注我，让我通知你有新的帖子！

# 脚注

在所有值得夸耀的事情中，在谷歌拥有 Python 可读性可能是最愚蠢的。以防不明显，我只是在开玩笑。

如果上帝[用 Lisp](https://xkcd.com/224/)创造了宇宙，我想找出所有大惊小怪的原因。如果你也好奇的话，[实用公共 Lisp](http://www.gigamonkeys.com/book/) 是一本很棒的免费书籍。

值得指出的是，以这种方式寻找函数定义是非常脆弱的。虽然这对于快速原型来说是可以的，但是更健壮的东西需要遵循 [Python 参考文献](https://docs.python.org/3/reference/compound_stmts.html#function-definitions)。

# 参考

[1] D. Beazley，[模块和包:生与死！](https://speakerdeck.com/dabeaz/modules-and-packages-live-and-let-die?slide=70)，PyCon (2015)。
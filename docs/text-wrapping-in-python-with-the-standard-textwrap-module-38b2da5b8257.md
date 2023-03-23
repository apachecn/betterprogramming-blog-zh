# 使用 Python 中的标准“textwrap”模块进行文本换行

> 原文：<https://betterprogramming.pub/text-wrapping-in-python-with-the-standard-textwrap-module-38b2da5b8257>

## 利用标准`textwrap` 库的强大功能，像专家一样格式化文本！

![](img/a7f5416f8a208df566c411697adecc37.png)

照片由 [Kira auf der Heide](https://unsplash.com/@kadh?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当在自然语言处理、数据分析、甚至艺术或设计工作的应用中处理文本语言时，文本换行是一种有用的工具。Python `textwrap` 模块提供了方便的函数来帮助创建文本换行效果。

这个模块还提供了几个相关的函数，可以用来规范化空白、删除或添加缩进、前缀和后缀文本，以及许多其他用途。此外，`textwrap`模块还提供了一个`TextWrapper`类，它提供了类似的功能，但考虑到了更高的内存效率。

# Python 中的文本换行

本文中的例子使用了亚历山大·杜马斯*的《基督山伯爵*中的文本，该文本现在是公共领域的一部分，可以从古腾堡计划中以多种格式免费获得。下面的文字来自第一章的前三段。

```
On the 24th of February, 1815, the look-out at Notre-Dame de la Garde
signalled the three-master, the _Pharaon_ from Smyrna, Trieste, and
Naples.As usual, a pilot put off immediately, and rounding the Château d’If,
got on board the vessel between Cape Morgiou and Rion island.Immediately, and according to custom, the ramparts of Fort Saint-Jean
were covered with spectators; it is always an event at Marseilles for a
ship to come into port, especially when this ship, like the _Pharaon_,
has been built, rigged, and laden at the old Phocee docks, and belongs
to an owner of the city.
```

使用下面的代码，我很快了解了这段文本的平均行长度。

```
import statistics# Calculate the average line length
avg_length = statistics.mean([len(x) for x in text.splitlines()])>>> 48.083333333333336
```

这与我正在做的事情无关，但将有助于指导在包装期间为宽度传递的值，以获得更好的可视化演示。毕竟，将 48 个字符宽的文本串包装成 50 个字符的效果在视觉上不会很有戏剧性。

# 独立函数与 Textwrapper 类

[Python textwrap 模块](https://docs.python.org/3/library/textwrap.html)提供了一系列方法，作为独立的函数或者通过`TextWrapper`类作为类方法。官方文档建议这样使用`TextWrapper`类:

“如果你只是包装或填充一个或两个文本字符串，方便的功能应该足够好；否则，为了提高效率，应该使用 TextWrapper 的实例。

换句话说，如果你只是在修改一两个句子，没有必要强占记忆。我们将从 TextWrapper 类开始研究每种类型的用法，因为其中定义的几个实例属性与独立函数相关。

# 独立功能

以下函数是独立提供的，它们提供了有用的文本实用程序。与模块名称可能暗示的相反，这些函数解决了许多常见的用例，而不是将 python 中的文本行换行到固定长度。然而，这就是我们要开始的地方！

# 包

*"返回输出行列表，没有最后的换行符。如果包装的输出没有内容，则返回的列表为空。—文本包装文档*

该函数提供了`textwrap` 模块最具特征的实用程序——通过*将*文本换行到有限数量的字符。该函数接受文本字符串或文本集合、宽度参数以及 TextWrapper 类可用的任何其他实例参数。

使用示例文本、wrap 函数和宽度参数 25 会产生以下输出:

# 充满

*"将文本中的单个段落换行，并返回包含换行段落的单个字符串。"—文本包装文档*

在使用了 wrap 函数之后，人们可能会考虑使用子字符串集合。我倾向于发现我的最终用例是文本的*块*,而不是子字符串的集合。

换句话说，我希望文本换行产生平均行长度更短的单个文本块。我倾向于使用类似于`text_block = "\n".join(lines)`的东西来完成这个目标——这正是`fill`函数所做的*:*

**注意:**fill 函数与 wrap 函数采用相同的参数。唯一的区别是它将结果行连接成一个文本块。

# 缩短

*“折叠和截断给定的文本以适合给定的宽度。”— TextWrapper 文档*

`shorten`函数可以被视为一个摘录生成函数。它将一个字符串作为参数，并将该字符串截断为特定数量的字符。在截断过程中，shorten 函数还会对空白进行规范化，以使结果文本只包含单个空格字符。查看这个 [ASCII 查找表](https://www.alpharithms.com/ascii-table-512119/)以获得空白字符值的参考。

`shorten`函数是独立函数的第一个例子，其中除了`text`和`width`之外的关键字参数非常有用。在这里使用`placeholder`参数指定什么字符将被附加到结果文本中。个人觉得椭圆(...)在视觉上合适，如下例所示:

```
# Use the shorten function to create a concatenated string
textwrap.shorten(text=sample_text, width=75, placeholder="...")# The resulting concatenated text with the ellipsis added
# via the "placeholder" argument.
>>> On the 24th of February, 1815, the look-out at Notre-Dame de la Garde...
```

**注意**:如果空白规范化导致文本短于指定的宽度，那么文本将被完整返回。全空白文本将返回一个空字符串，也不添加占位符。

# 德登特

*“删除文本中每行常见的前导空格。”—文本包装文档*

dedent 函数花了我一点时间来理解。它的目的是规范整个文本块的前导空格**——而不是单独的行**。这里常见的情况是在方法文档、字符串格式等东西中找到预期的文本。此函数不适用于当前的示例文本，但是考虑来自重复源代码的缩进文档字符串的以下情况:

复制/粘贴这个*缩进的*文档字符串文本会导致文本行共享四个单空格字符的前导序列(不是制表符！).手动删除这个缩进非常容易，并且几乎不能证明依赖函数的存在。

想象一下，使用标准库中每个函数的 docstring(可能用于 NLP 分析),需要移除缩进。手动移除在功能上是难以处理的。使用 dedent 函数将确保在预处理过程中删除所有缩进。一个简单的例子:

注意，大仲马作品的这个例子在开头和结尾都有一个换行符，没有被删除。**换行符并不是所有文本行**都通用的，因此在 dedent 函数算法中不会被删除。仅删除所有行共享的缩进空间。

# 缩进

*“在文本中所选行的开头添加前缀。”—文本包装文档*

缩进函数的工作方式和预期的一样；它将指定为后缀参数的任何字符串添加到文本正文中。考虑对原始示例文本使用缩进功能的结果:

默认情况下，indent 函数会将 prefix 的值添加到除纯空白行之外的任何行。换句话说，前缀不会添加到示例文本中段落之间的空行中。

indent 函数还允许将一个可调用的参数作为一个`predicate`参数传入。这旨在为被赋予`prefix`的线路提供逻辑控制。例如，如果坚持在空白行前加上前缀，可以使用下面的语法:

其他创造性的方法可能适用于这样的情况，其中一行包含一系列字符，这样人们会想要视觉通知。例如，每行包含单词“and”:

我希望将来能看到这个函数的一个可能的扩展是支持在多个前缀之间进行有条件的选择。例如，添加一个四个空格的序列来缩进没有单词*和*的每一行，或者添加一个带有前导星号的三个空格的序列。目前，我们需要做类似的事情:

这里的最终结果是一个更加视觉平衡的*表示，表示在我们的示例文本的一行中包含单词*和*。

# TextWrapper 类

TextWrapper 类用于可能与内存效率相关的较大文本体。例如，一个一百万字的语料库正在为 NLP 分析进行预处理。实例化期间传递的关键字参数可作为实例属性用于类对象。TextWrapper 类还提供了`wrap`和`fill`作为公共方法。

# 最后的想法

`textwrap`模块包含一些非常基本的文本操作工具。我很少发现一个基于文本的项目可以通过这个模块完全解决。然而，它的`wrap`和`dedent`函数都非常有用，并提供了包含在标准 Python 库中的好处。我发现自己经常使用它们，足以熟悉它们的内部运作。

如果我可以改变这个模块的任何东西，那就是将`placeholder`参数命名为*后缀*、*追加、*或*结束*，以更好地传达用例。`placeholder`语法意味着在输入时删除——就像 HTML 表单字段中的*占位符*属性。奇怪的是，`indent`方法选择使用`suffix`语法。

我还希望看到一种更简洁的方法来揭示 TextWrapper 对象的包装版本。例如，要查看包装形式的文本，必须将文本传递给公共方法:`text.wrap(text=self.text)`。我希望看到更多类似于`text.show()`的东西，这仍然能够带来通过`text.text`属性访问原始文本的好处。我估计大家都是评论家吧！

文章 [*使用 Python 中的标准 textwrap 模块*](https://www.alpharithms.com/python-text-wrapping-with-textwrap-module-321615/) 进行文本换行首次出现在[αlphα算法](https://www.alpharithms.com)网站上，并经许可在此重新发布。
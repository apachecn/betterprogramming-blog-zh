# 在 Python 中声明函数时最容易犯的 5 个错误

> 原文：<https://betterprogramming.pub/top-5-mistakes-you-make-when-declaring-functions-in-python-b7a0747711a4>

## 编写更好的函数

![](img/db7178902abf6f075f1b021d20bbe08b.png)

由[拉蒙·萨利内罗](https://unsplash.com/@donramxn?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在任何编程项目中，函数都是至关重要的组成部分。如果做得正确，这是编写可读和可维护代码的实用方法。然而，当函数声明不正确时，您的代码变得难以阅读，长期可维护性也很低——即使我们假设您编写了维护相同项目的代码，因为任何程序员都可能忘记他们做了什么。

鉴于函数在编程中的重要性，Python 当然也包括在内，我想指出一些 Python 程序员在声明函数时可能犯的最常见的错误。通过了解这些缺陷，我们可以实现相应的最佳实践，这不仅会提高代码的可读性，还会使代码更易于维护。

> "可读性很重要。"—[Python 的禅](https://www.python.org/dev/peps/pep-0020/)

# 1.不正确的函数名

起名字并不是只有当你有了新的婴儿或宠物时才会困扰你的事情。你可能不同意，但作为一名程序员，这将是你整个职业生涯中一项具有挑战性的任务。挑战来自我们对函数名的标准，它应该是唯一的、信息丰富的和一致的。

## **独特的**

这是一个非常简单的要求。与任何 Python 对象一样，我们使用名称来标识函数。如果我们用相同的名字声明函数，要么你的 Python IDE(集成开发环境，比如 PyCharm，Visual Studio 代码)会抱怨，要么后来者成为赢家。考虑下面的例子。我们声明了两个名为`say_hello`的函数，如你所见，当我们调用这个函数时，我们声明的第二个函数被调用了:

函数名应该是唯一的。

## **信息丰富**

编写函数是为了执行某些已定义的操作，因此它们的名称应该反映它们的职责。如果名字不能明确地告诉我们这些职责，我们将很难理解别人的程序或我们自己上个月写的代码。信息丰富意味着对功能的预期目的而言是具体和准确的。考虑下面的例子:

函数名应该是信息性的。

## 一致的

Python 编程鼓励模态，这意味着我们希望在某些模块中对相关的类和函数进行分组。在模块内部和模块之间，您希望一致地命名您的函数。就一致性而言，我们的意思是对特定类型的对象和操作使用相同的约定。考虑下面的小例子。前三个函数都使用两个数字执行相似的操作，因此我使用相同的格式:动词+下划线+数字。在自定义类`Mask`中，两个函数`promotion_price`和`sales_price`具有相似的名称结构，第一部分定义价格的种类，第二部分表示返回值的性质(即以浮点数表示的价格)。

函数名应该一致。

# 2.混合职责和过长的长度

另一个常见的错误是，一个特定的功能有太多的混合职责——如果他们没有持续地重构他们的程序，甚至一些高级程序员有时也会犯这个错误。就给定功能的职责而言，最佳实践是该功能只有一个明确定义的职责，可以通过其合理的名称轻松反映出来。

具有混合职责的函数的另一个常见伴随症状是它们往往过长，因此如果出现任何错误，理解函数和调试就更加困难。让我们考虑下面这个假设的例子。我们使用流行的`[pandas](https://pandas.pydata.org/)` [库](https://pandas.pydata.org/)来处理我们在实验中收集的生理数据。对于每个受试者，我们有四个 CSV 格式的数据。我们可以编写一个名为`process_physio_data`的函数，包含所有三个数据处理步骤。然而，由于数据的复杂性，该函数将超过 100 行代码。

混合职责的长期职能。

我们可以不写这么长的函数，而是写下面的函数来更好地展示处理这些数据的步骤。如下面的代码片段所示，我们创建了三个助手函数，分别负责这三个步骤。值得注意的是，这些功能中的每一个都有一个*职责。更新后的`process_physio_data`功能更薄更清晰，它唯一的职责就是提供一个处理生理数据的管道。通过对这些作业的重构，代码的整体可读性得到了很大的提高。*

具有专用单一职责的简短功能。

# 3.没有文档

这是一个常见的错误，从长远来看，程序员必须从中吸取教训。从表面上看，您的代码仍然像预期的那样运行似乎非常好——即使您没有任何文档。例如，当你连续几周都在做一个项目时，你确切地知道你在用每个功能做什么。然而，当你需要修改代码来更新一些特性时，你需要花多少时间来弄清楚你做了什么？我得到了惨痛的教训，你可能也有过类似的经历。

在人们共享 API 的团队环境中，或者当你正在创建开源库时，缺少文档可能是一个更大的问题。当我们使用别人的函数，尤其是复杂的函数时，并不知道函数内部的具体操作。然而，我们只需要阅读文档就可以知道如何调用函数以及预期的返回值是什么。您能想象如果您的工作所依赖的库或框架都没有任何文档吗？

我并不是说你应该为你的函数编写大量的文档字符串。我相信，如果您遵循了命名标准来保持函数名的唯一性、信息性和一致性，并且每个函数只执行一项任务，并且具有适当的长度，那么您就不需要为函数编写太多的文档。然而，如果你在公司或开源社区中作为一个大团队的一部分工作，为了自己和他人的利益，你必须实现标准的文档约定。

我不打算在这个话题上展开，但这里有一篇关于写好 Python docstrings 的[中型文章](https://medium.com/better-programming/the-guide-to-python-docstrings-3d40340e824b)。

# 4.默认值使用不正确

当我们编写函数时，Python 允许我们为某些参数设置一些默认值。许多内置函数也使用这个特性。考虑下面的例子。我们可以使用`range()`函数创建一个列表对象，它具有通用语法`range(start, stop, step)`。当它被省略时，默认的`step`参数将使用一个。但是，我们可以在下面的代码中显式设置 step 参数(比如，`2`):

带默认参数的范围函数。

然而，当我们编写包含可变数据类型默认值的函数时，事情就变得棘手了。当我们说可变数据时，我们指的是 Python 对象在创建后可以更改，比如列表、字典和集合。要了解更多关于 Python 数据可变性的知识，可以参考[我之前的文章](https://medium.com/swlh/6-things-to-understand-python-data-mutability-b52f5c5db191)。考虑以下关于在函数中使用带有可变参数的默认值的简单示例:

带有默认可变数据参数的函数。

当我们试图追加分数`98`时，期望的结果被打印出来，因为我们省略了`scores`参数，我们期望使用空列表。当我们试图将分数`92`追加到一个`[100, 95]`列表中时，结果`[100, 95, 92]`也在意料之中。然而，当我们试图追加分数`94`时，我们中的一些人可能会期望结果是`[94]`，但事实并非如此。为什么会这样？

这都是因为 Python 中的函数也是一等公民，被认为是常规对象(见我的[上一篇文章](https://medium.com/swlh/everything-is-an-object-in-python-learn-to-use-functions-as-objects-ace7f30e283e)关于函数是 Python 中的对象)。这意味着当定义一个函数时，会创建一个对象，包括函数的默认变量。让我们来看一个关于这些概念的代码片段:

跟踪默认可变数据参数。

我们修改了前面的函数，允许它输出`scores`列表的内存地址。正如你所看到的，在我们调用函数之前，我们能够找出函数参数的默认值和它访问`__default__`属性的内存地址。调用函数两次后，内存地址相同的同一个`list`对象已经被更新。

那么最佳实践是什么？我们应该使用`None`作为可变数据类型的默认值，这样在声明函数时，函数不会实例化可变对象。当函数被调用时，我们可以根据需要创建可变对象。有关更多信息，请参见以下代码。现在，一切都按预期运行:

对默认可变数据参数使用 None。

# 5.滥用*args & **kwargs

Python 允许我们通过支持可变数量的参数来编写灵活的函数。如果您还记得，您一定在某些库的文档中见过`*args`和`**kwargs`。本质上，`*args`指的是不确定数量的位置论元，而`**kwargs`指的是不确定数量的关键字论元。

在 Python 中，位置参数是基于其位置传递的参数，而关键字参数是基于其指定的关键字传递的参数。下面我们来看一个微不足道的例子:

位置和关键字参数。

在函数`add_numbers`中，`num0`和`num1`是位置参数，而`num2`和`num3`是关键字参数。需要注意的一点是，您可以更改关键字参数之间的顺序，但不能更改位置参数和关键字参数之间的顺序。让我们进一步看看`*args`和`**kwargs`是如何工作的。最好用实例来学习它们。需要注意两件事:

1.  可变数量的位置参数作为一个元组来处理，因此我们可以用一个星号来解包它(在本文中了解更多关于元组解包的信息[)。](https://medium.com/swlh/10-things-you-should-know-about-tuples-in-python-b71189bdba1f)
2.  可变数量的关键字参数作为一个字典来处理，因此我们可以用两个星号来解包它(在本文中了解更多关于字典解包的信息[)。](https://medium.com/better-programming/dictionary-merging-and-updating-in-python-3-9-4ac67c667ce)

* Python 函数中的 args 和**kwargs。

虽然`*args`和`**kwargs`的可用性允许我们编写更灵活的 Python 函数，但是滥用它们会导致函数的混乱。前面我提到过我们可以使用 pandas 库进行数据操作，并简单提到了读取 CSV 文件的`read_csv`函数。你知道这个函数可以接受多少个参数吗？来看看它的[官方文档](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html):

```
pandas.read_csv(*filepath_or_buffer: Union[str, pathlib.Path, IO[~AnyStr]], sep=',', delimiter=None, header='infer', names=None, index_col=None, usecols=None, squeeze=False, prefix=None, mangle_dupe_cols=True, dtype=None, engine=None, converters=None, true_values=None, false_values=None, skipinitialspace=False, skiprows=None, skipfooter=0, nrows=None, na_values=None, keep_default_na=True, na_filter=True, verbose=False, skip_blank_lines=True, parse_dates=False, infer_datetime_format=False, keep_date_col=False, date_parser=None, dayfirst=False, cache_dates=True, iterator=False, chunksize=None, compression='infer', thousands=None, decimal: str = '.', lineterminator=None, quotechar='"', quoting=0, doublequote=True, escapechar=None, comment=None, encoding=None, dialect=None, error_bad_lines=True, warn_bad_lines=True, delim_whitespace=False, low_memory=True, memory_map=False, float_precision=None*)
```

如果你计算一下，你会发现参数的总数是 49——1 个位置参数和 48 个关键字参数。理论上，我们可以这样做来缩短列表:

```
pandas.read_csv(*filepath_or_buffer: Union[str, pathlib.Path, IO[~AnyStr]], **kwargs)*
```

然而，在这个函数的实际实现中，我们仍然必须解包`**kwargs`并弄清楚如何正确读取 CSV 文件。为什么这些经验丰富的 Python 开发人员愿意列出所有这些关键字参数？这都是因为他们明白以下原则:

> “显性比隐性好。”—[Python 的禅](https://www.python.org/dev/peps/pep-0020/)

虽然使用`**kwargs`可以为我们节省一些函数声明的第一行，但代价是我们的代码变得不那么清晰。同样的想法也适用于`*args`的使用。如上所述，如果我们在代码共享环境中工作，我们总是希望我们的代码是显式的，从而更容易理解。因此，只要有可能，我们希望避免使用`*args`和`**kwargs`来编写更显式的代码。

# 外卖食品

在本文中，我们回顾了 Python 程序员在代码中可能犯的五个常见错误。尽管通过忽略项目中的这些错误，您可以拥有自己的编码风格，但您的代码可能变得难以理解，并导致长期可维护性低下。因此，如果可能的话，我们都可能希望避免这些错误，并促进代码的可读性，从而提高可共享性。

# 附加阅读

在这篇文章中，我们提到了我以前的文章中已经涉及的几个概念，比如函数是 Python 中的对象以及解包元组和字典。如果你感兴趣，请随意阅读其中一些。为了方便起见，下面提供了一些链接:

[](https://medium.com/better-programming/30-simple-tricks-to-level-up-your-python-coding-5b625c15b79a) [## 提升 Python 编码水平的 30 个简单技巧

### 更好的 Python

medium.com](https://medium.com/better-programming/30-simple-tricks-to-level-up-your-python-coding-5b625c15b79a) [](https://medium.com/better-programming/dictionary-merging-and-updating-in-python-3-9-4ac67c667ce) [## Python 3.9 中的字典合并和更新

### 快速浏览 Python 3.9 的新 merge |和 update |=操作符

medium.com](https://medium.com/better-programming/dictionary-merging-and-updating-in-python-3-9-4ac67c667ce) [](https://medium.com/swlh/10-things-you-should-know-about-tuples-in-python-b71189bdba1f) [## 关于 Python 中的元组，你应该知道的 10 件事

### 在 Python 编程中使用元组非常方便。每个 Python 程序员都应该非常了解它的用法。

medium.com](https://medium.com/swlh/10-things-you-should-know-about-tuples-in-python-b71189bdba1f)
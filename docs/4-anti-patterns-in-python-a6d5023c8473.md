# Python 中的 4 种反模式(以及如何避免它们)

> 原文：<https://betterprogramming.pub/4-anti-patterns-in-python-a6d5023c8473>

## 了解 Python 习惯用法及其替代方法

*变更日志:
2022 年 12 月 31 日—使用 Medium 的新代码块突出显示语法
2022 年 1 月 5 日—修复打字错误并提高清晰度*

![](img/76298ee9158a2a37bccea5b086d180ff.png)

照片由[谢尔盖·库图佐夫](https://unsplash.com/@serge_k?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/awkward?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

Python 在 IEEE 2021 顶级编程语言中排名第一，是最容易掌握的语言之一，在机器学习社区中非常受欢迎。

然而，有一些常见的习惯用法和模式稍微偏离了良好的软件工程实践:可读代码、ETC(更容易更改)原则、DRY(不要重复自己)原则，以及防止程序员自己的意外错误。

在这篇文章中，我将分享 Python 中我认为是反模式的 4 个习惯用法，为什么我们应该避免它们，以及我们如何改进它们的一些建议。

# 1.文档字符串

Docstrings 重复了很多知识，明显违反了 DRY 原则。docstring 重复的内容:

1.  参数及其 Python 类型的描述，以及函数返回的内容-如果包含类型注释，这已经包含在函数签名中
2.  函数作用的总结——这已经包含在函数名和函数体中
3.  函数引发的异常——这已经在函数体中了

功能在很大程度上应该是自文档化和自解释的。如果你发现你必须做很多解释，你可能需要重构你的代码。

```
def preprocess_sentence(text):
    """
    Preprocess sentence to remove line breaks and 
    special characters.

    >>> preprocess_sentence("Hello world!")

    Args:
        text (str): text to preprocess

    Raises:
        ValueError: If text is an empty string

    Returns:
        str: preprocessed text
    """
    pass
```

虽然文档字符串可能有一些好处(比如给出函数的快速摘要，尽管仍然有争议)，但是应该避免使用它们，因为:

*   知识的重复(解释和代码)可能会导致不一致，如果您忘记了将 docstring 和代码一起更改的话。
*   它允许开发人员通过编写文档字符串来弥补糟糕的代码。
*   docstring 中的一些描述看起来像是为了编写它而存在的(只是因为 docstring 需要它)，就像解释它返回的内容——duh 这是一个预处理文本，我们不需要知道这些。

## 我们能做些什么呢？

*   如果您认为 docstring 没有提供任何价值，请将其完全删除。
*   引入渐进类型—类型注释参数并返回类型。使用类型别名。
*   仅在 docstring 中显示输入和输出。这尤其有助于数据转换，这在机器学习中很常见。

```
def remove_special_chars(text: str) -> str:
    """
    ["hello world!\n"] -> ["hello world"]
    """
    pass
```

**⚠️例外:库、包和框架**

然而，文档字符串在库、包或框架中是必要的。为什么没事？

一个库有两种不同类型的受众— (1)代码贡献者，以及(2)使用他们库的其他开发人员。

您的项目的代码库只有前者——您和您的团队成员都是贡献者。

第二组——使用他们的库的开发人员——对代码和某些东西是如何实现的不感兴趣，因为他们只对如何使用它感兴趣。这就是文档字符串有用的地方。

于是，维护文档和代码一致性的额外开销的责任就落在了贡献者身上。库通常在它们的 CI/CD 管道中检查文档是否与代码一致。这些包括精细的单元测试，如果有代码示例就运行它们(scikit-learn)，以及在集成新的更改之前在代码评审中进行手动检查(可能有多个评审员)。

因此，除非你正在编写一个库或框架，否则不要编写 docstrings。

# 2.只读数据字典

```
person = {}

person["name"] = "Fandi"
person["age"] = 25
person["country"] = "Singapore"
```

Python 字典不是存储只读键值数据(例如配置)的好选择。它们不提供安全性，因为它们:

1.  可变——我们可能会意外地在代码中的任何地方覆盖或添加新数据，并且
2.  通过字符串类型的键访问，这可能会被拼错，从而导致 KeyError 异常。

我们这里所说的“安全”指的是数据在被读取时的可靠性和准确性。这意味着这个物体(包含我们的数据)在它被创建后不应该是*变异的*，而且只要它还活着，就永远不会是。不小心这样做会引发一个异常(而不是悄悄地继续程序)。

## 我们能做些什么呢？

使用[冻结数据类](https://docs.python.org/3/library/dataclasses.html#frozen-instances) (Python 3.7)，静态类型检查器(例如 [mypy](https://mypy.readthedocs.io/en/stable/) )和 linters。

```
from dataclasses import dataclass

@dataclass(frozen=True)
class Person:
    name: str
    age: int
    country: str
```

这就创建了一个名为`Person`的类。`frozen=True`防止我们意外覆盖数据(由 mypy 引发)。甚至没有机会访问拼写错误的属性。我们还受益于常见 ide 中类属性的自动完成。

还有其他替代方法，尽管我不推荐它们: [UserDict](https://docs.python.org/3/library/collections.html#collections.UserDict) (大量样板代码)，一个带有自定义设置器的类(大量样板代码)，以及`[namedtuple](https://docs.python.org/3/library/collections.html#namedtuple-factory-function-for-tuples-with-named-fields)`(看起来不像 dataclass 那样直观)。

*   请注意，虽然这里的重点是使用字典来存储键值数据，但是我们也可以将相同的原理应用于顺序数据的列表。

# 3.字符串而不是枚举

对于只接受一个可能选项列表中的一个选项的函数参数来说，字符串类型是一个糟糕的选择。以下是一些原因:

1.  拼写错误的风险。
2.  逃避棉绒的耗尽检查。如果您忘记添加一个选项，mypy 可以为您检测出来(见下文)。
3.  代码重复。例如，如果您想要重用 if-else 分支检查，您可能需要重复 if-else 语句。

```
def fit(solver="lbfgs"):

    if solver == "lbfgs":
        pass
    elif solver == "saga":
        pass
    elif solver == "sag":
        pass
    else:
        raise ValueError
```

然而，这种模式在机器学习库中非常常见，原因很明显——快速原型。如 1 中所述。Docstrings 部分，这些库比我们有更多的检查。

## 我们能做些什么呢？

使用[enum](https://docs.python.org/3/library/enum.html#creating-an-enum)和棉绒。枚举准确地抓住了我们的意图——实例是一个仅来自定义的可能值列表的值。为了安全起见，稍微多写一些代码。

```
from enum import Enum

class SolverAlgorithm(Enum):
    LBFGS = 0
    SAGA = 1
    SAG = 2

def fit(solver: SolverAlgorithm = SolverAlgorithm.LBFGS):

    if solver == SolverAlgorithm.LBFGS:
        pass
    elif solver == SolverAlgorithm.SAGA:
        pass
    elif solver == SolverAlgorithm.SAG:
        pass
    else:
        raise ValueError
```

不幸的是，mypy 的穷举检查只能通过编写更多的代码来实现。见[此处](https://hakibenita.com/python-mypy-exhaustive-checking#exhaustiveness-checking)(上一次我这么试的时候，mypy 还是没有给我任何警告)。

# 4.列出跨多行的理解

虽然理解(如此命名是为了包括列表、字典和生成器理解)相当简洁，但是如果代码有以下一种以上的情况，它们会变得非常难以阅读:

*   if-else 子句
*   多行
*   嵌套理解

可读性大打折扣，尤其是对于那些喜欢顺序阅读代码的人。

这看起来不错:

```
document = [
    preprocess(sentence)
    if len(sentence) > 20 else sentence + "\n"
    for sentence in sentences
]
```

在我看来，这仍然很好，尽管读起来有点挑战性，因为你的眼睛需要从中间的`for`开始，然后去寻找下一个`for`，然后去第一个`x`。

```
nested_list = [[1,2,3],[4,5,6],[7,8,9]]

new_list = [
    x for sub_list in nested_list for x in sub_list
]
```

这是最糟糕的。简洁？不，谢谢你。

```
nested_list = [[1,2,3],[4,5,6],[7,8,9]]

new_list = [
    x if x > 4 else -1 
    for sub_list in nested_list 
    for x in sub_list
    if sum(sub_list) > 6
]
view raw
```

## 我们能做些什么呢？

*   缩进理解，并适当地将从句分成不同的行。
*   使用可读性更强的变量名。
*   请改用 for 循环，尤其是对于嵌套的理解。牺牲一点速度和简洁来获得更高的可读性。

```
nested_list = [[1,2,3],[4,5,6],[7,8,9]]

new_list = []
for sub_list in nested_list:
    if sum(sub_list) <= 6:
        continue
    for x in sub_list:
        item = x if x > 4 else -1
        new_list.append(item)
```

暂时就这样吧！感谢阅读。

# 参考

[务实的程序员](https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/)

*感谢* [*钟大伟*](https://medium.com/u/f392dd76f846?source=post_page-----a6d5023c8473--------------------------------) *和* [*梁齐威*](https://medium.com/u/1d3591a5a4b4?source=post_page-----a6d5023c8473--------------------------------) *审阅本文！*
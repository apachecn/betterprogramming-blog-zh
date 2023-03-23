# 看看 Python 3.9 中令人惊叹的新特性

> 原文：<https://betterprogramming.pub/take-a-look-at-the-awesome-new-features-coming-in-python-3-9-8753c494de39>

## 字典联合操作符、泛型类型注释等等

![](img/7dddf6efad368667e961ba340d8ce69f.png)

[法比安·格罗斯](https://unsplash.com/@grohsfabian?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 3.9 目前正处于测试阶段的第四个迭代阶段(截至 2020 年 7 月)。虽然有些事情可能会改变，但现在已经很清楚新版本会是什么样子了。

# 泛型类型批注

泛型类型注释，又名 [PEP 585](https://www.python.org/dev/peps/pep-0585/) ，是许多 python 爱好者渴望已久的东西。现在，您可以指定一个集合及其项目的类型，而不依赖于`types`库:

```
def do_stuff(data: list[int]): 
    pass
```

以下是一些现在已经通用的集合:

*   `tuple`
*   `list`
*   `dict`
*   `set`
*   `frozenset`
*   `type`
*   `collections.deque`
*   `collections.abc.Coroutine`
*   `re.Pattern`
*   [还有更多](https://www.python.org/dev/peps/pep-0585/)

# 字典联合运算符

![](img/78d45ef960e0165f54104cf71d00e130.png)

JS 开发人员。

在 Python 3.9 之前，合并到字典的标准方法是:

1.  `.update`方法:`a.update(b)`
2.  传播操作员(hello JS): `{**a, **b}`

嗯，我们又有一个了。`|` union 操作符做了它应该做的事情:

```
a = {'cars': 5, 'phones': 2} 
b = {'cows': 10, 'lizards': 3} 
a | b 
// {'cars': 5, 'phones': 2, 'cows': 10, 'lizards': 3}
```

就像`.update`或 spread 操作符一样，如果字典中有重复的关键字，这可能会导致数据丢失。此外，该运算符同样适用于扩充赋值:

```
a = {'cars': 5, 'phones': 2} 
b = {'cows': 10, 'lizards': 3} 
a |= b 
print(a) 
// {'cars': 5, 'phones': 2, 'cows': 10, 'lizards': 3}
```

# 删除后缀和前缀

新的`.removesuffix()`和`.removeprefix()`字符串方法就是这么做的:

```
'SubaruImpreza`.removeprefix('Subaru') // 'Impreza' 'SubaruImpreza'.removesuffix('Impreza') // 'Subaru'
```

# 新解析器

在 Python 3.9 中，使用了新的解析器。它基于 [PEG(解析表达式语法)](https://en.wikipedia.org/wiki/Parsing_expression_grammar)，不像旧的使用 [LL(从左到右解析器)](https://en.wikipedia.org/wiki/LL_parser)。这样做是因为 LL 解析器的能力已经耗尽，实现新的语言特性变得越来越困难。最有可能的是，您会注意到没有区别——性能是可比的，并且完全向后兼容性是经过验证的。

只有当您的代码使用标准库中的`parser`模块时，您才需要关心它。不久前它被弃用了，并且不能与新的解析器一起工作。作为一种变通方法，您可以使用参数`-X oldparser`或环境变量`PYTHONOLDPARSER=1`切换回旧的解析器。

# Zoneinfo 模块

Python 3.9 引入了一个名为`zoneinfo`的新模块。它实现了为 IANA 时区提供支持的`ZoneInfo`类。

```
from zoneinfo import ZoneInfo 
from datetime import datetime, timedelta 
timestamp = datetime(2020, 7, 15, 11, tzinfo=ZoneInfo("America/Los_Angeles"))
```

# 图形库模块

另一个新模块——这次是处理图表。目前，它只实现了有向无环图的拓扑排序算法。如果你不明白我刚刚写的，你很可能不会需要新的模块。对于那些熟悉图论的人来说，模块应该是这样使用的(摘自 Python 文档):

```
>>> graph = {"D": {"B", "C"}, "C": {"A"}, "B": {"A"}} 
>>> ts = TopologicalSorter(graph) 
>>> tuple(ts.static_order()) 
('A', 'C', 'B', 'D')
```

# 结束语

谢谢你的阅读，我希望你喜欢我的文章。请继续订阅更多内容！

## 资源

*   [发行说明](https://docs.python.org/3.9/whatsnew/3.9.html)
*   [变更日志](https://docs.python.org/3.9/whatsnew/changelog.html#changelog)
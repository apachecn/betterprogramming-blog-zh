# Python 3.10 中的结构模式匹配

> 原文：<https://betterprogramming.pub/structural-pattern-matching-in-python-3-10-247b2626c525>

## 它不仅仅是一个开关盒

![](img/800aa4a97462af6c600f755d209163a0.png)

[穆利亚迪](https://unsplash.com/@mullyadii?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Python 是最具趋势的语言之一，由于其在数据科学和人工智能领域的流行，工作需求不断增长。

为了跟上其他新兴语言(如 Go、Julia 和 Rust)的发展，Python 软件基金会投入了大量资源来不断改进 Python。

最近，它发布了一个标志着 Python 3.10 的重大版本升级。不要以为这是 Python 3 的最后一个主要版本，因为 3.11 现在正在考虑中，正如 Python 的[官方网站](https://docs.python.org/3.11/whatsnew/3.11.html)所示。

总之，与 3.9 相比，Python 3.10 提供了几个新特性，代表了一个重大的升级，比如显式类型别名、支持联合操作的更好的注释，当然还有结构化模式匹配——这也是本文的主题。

包括 [634](https://www.python.org/dev/peps/pep-0634/) 、 [635](https://www.python.org/dev/peps/pep-0635/) 和 [636](https://www.python.org/dev/peps/pep-0636/) 在内的许多 pep 都致力于这一新特性的讨论。所以感兴趣的读者可以从官方的角度全面了解这个功能的发展。在这里，我想简单地提供这种技术的概述，并强调一些最常见的用例。

为了便于参考，我大胆地将结构化模式匹配称为 SPM，这是这种技术的缩写。请注意，据我所知，我在官方文档中没有见过这样的名字。对我来说写作很简单。

注:Python 3.10 已经正式发布，你可以在 Python 的官网上找到。这里有一个到下载页面的[链接。](https://www.python.org/downloads/)

# 概述和基本语法

在我们更进一步之前，让我们先通过一个假设的场景来理解什么是结构模式匹配。

假设我们从一个 API 调用收到一个响应，它恢复一个 tuple 对象，第一项是状态代码，第二项是数据，这取决于请求是否成功。

换句话说，当请求失败时，元组只包含一项。下面向您展示了一个可能的解决方案:

不使用 SPM 时的示例

如您所见，该解决方案符合我们的业务需求。然而，当我们的需求发展时，我们将不得不使用多层嵌套的`if…elif…else…`语句创建额外的分支。当这种情况发生时，我们代码库的可读性和可维护性会显著下降。

这就是 SPM 发挥作用的时候了，它旨在优雅地处理这项业务。这个添加到 3.10 中的新特性涉及到两个新关键字的使用:`match`和`case`(严格来说，它们被称为软关键字，参见[这里的](https://docs.python.org/pl/3.10/reference/lexical_analysis.html#soft-keywords)进行讨论)。

让我们看看如何使用 SPM 来实现这一需求。一个可能的解决方案如下所示:

SPM 示例

正如您所看到的，基于 SPM 的实现的整体可读性得到了很大的提高。它可能会让许多人想起许多编程语言中都有的经典 switch-case 特性。但是，SPM 不一样——它更好，接下来我们来探讨一下。

# 匹配多个值

在前面的代码片段中，您已经看到我们使用了许多`case`子句来创建多个模式，可以对照这些模式来检查`resp`。

然而，SPM 是灵活的，因为每个模式可以包含多个值，而不是只有一个值。下面的代码片段向您展示了一个可能的用例。

```
match resp:
    case (200, data) | (100, data):
        ...
    case ((404 | 401), data):
        ...
    case ((404 | 401) as status_code, data):
        ...
```

*   您可以将不同的模式聚合到一个单独的`case`子句中。使用它，您只需测试多个值。这些可选模式或值由并集运算符(竖线)分隔。
*   或者，在每个模式中，您可以使用 union 操作指定可能的值。
*   为了增加额外的灵活性，我们可以将可能的值转换为变量:`(404 | 401) as status_code`，这样我们就可以在后面的步骤中使用`status_code`。

更一般地说，模式中显示的变量可以称为捕获。本质上，匹配模式的值被分配给模式中相应的变量，这样这些捕获的值可以用于进一步的处理。

# 应用条件

除了匹配多个模式和值，您还可以对`case`子句应用附加条件。这样，您可以在不改变模式的情况下即时检查模式。

```
special_codes = [300, 301, 302]
match resp:
    case (code, data) if code in special_codes:
        ...
```

要应用条件，只需在模式后面添加一个`if`语句。请注意，模式中使用的变量在`if`语句中立即可用。

# 带星号的表达式和通配符

到目前为止，我们处理的元组中的条目不超过两个。然而，在元组或其他类型的数据容器中，您可能会得到数量可变的条目。

在这些情况下，您可以考虑使用一个带星号的表达式，它将多个条目捕获到一个列表中，以供以后处理，如下所示。

```
>>> def spm_starred(resp):
...     match resp:
...         case (code, *items):
...             print("items:", items)
... 
...             
>>> 
>>> spm_starred((200, "item1", "item2", "item3"))
items: ['item1', 'item2', 'item3']
```

另一个我们没有提到的有用特性是通配符模式。具体来说，我们使用下划线来表示任何与之前的模式不匹配的内容。这有点像其他语言中 switch-case 语句中的`default`子句。

```
match something
    case pattern0:
        ...
    case pattern1:
        ...
    case pattern2:
        ...
    case _:
        # this is the wildcard pattern
```

# 匹配复杂的对象

我们使用的模式非常简单:字符串、整数和元组。SPM 的强大之处在于它可以匹配复杂的模式，包括看似复杂的对象，比如字典和定制实例。

```
match resp_dict:
    case {"code": status_code, "data": data}:
        print("Status Code:", status_code)
        print("Data:", data)
```

如上所示，我们可以创建一个模式，它是一个`dict`对象，其值需要与字典中使用的键相匹配。

在更高级的场景中，我们甚至可以根据自定义的基于实例的模式来检查值，如下所示。

SPM 匹配类实例

如上所示，我们对照一些涉及定制类的模式来检查`person`。重要的一点是，您必须使用关键字参数来指定模式。

# 结论

在本文中，我们回顾了 Python 3.10 中最有用的新特性——结构化模式匹配。如您所见，这是一个强大的特性，超越了其他编程语言中的经典 switch-case 用法，后者通常需要精确匹配。Python 更进了一步，支持定义一个通用模式，显著提高了这项技术的灵活性。

每当您需要处理多种可能的场景时，这可能是考虑使用结构化模式匹配的最佳时机，它比多个`if…elif…else`语句具有更好的可读性。
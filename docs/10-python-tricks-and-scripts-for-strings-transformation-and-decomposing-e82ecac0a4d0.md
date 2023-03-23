# 字符串转换和分解的 10 个 Python 技巧和脚本

> 原文：<https://betterprogramming.pub/10-python-tricks-and-scripts-for-strings-transformation-and-decomposing-e82ecac0a4d0>

## 像真正的 Pythonista 一样解析字符串

![](img/53d2ccaefe62c5c1084a973e42489bc2.png)

由[彼得·诺伊曼](https://unsplash.com/@peterneumann?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我上一篇关于数据结构和容器可接受的 Python 习惯用法的文章非常成功，所以我决定为字符串解析和标记化准备一个脚本汇编。

没有人能否认文本分析和字符串解析的重要性。它应用于几乎每个软件开发方向，从 URL 解析到自然语言分析。我不会涵盖这种方法的所有可能的应用——那远远超出了 one piece 的范围。但是我将介绍一些在 Python 程序中处理字符串和标记的基本技术(因为它是我最喜欢的语言之一)。

这些小脚本应该被视为更大的文本分析和数据预处理应用程序的构建块。基础知识对进一步发展非常重要。

好了，我们去列表吧！

## 1.翻译和替换

第一种情况是从文本中替换或删除一些符号或一些子串。Python 在`string`模块中有内置函数，这些函数执行期望的任务。

`translate()`使用符号映射删除或更改特定符号:

`replace()`正如它的名字所暗示的那样工作——通过将一个子串更改为想要的子串:

## 2.字符串消毒

现在，我们可以将前面语句中的知识应用于字符串净化。在数据清理期间，这是数据科学项目中要求最高的过程之一——一个很好的例子是带有空白符号和换行符的原始文本。这里有一个简单的脚本来清理这样的字符串:

## 3.以你想要的方式分裂

文本分析需要不同的指标，如字数、符号数、平均句子长度。为了计算这些值，我们需要准备好文本——清理和分割。幸运的是，Python 有几个用于文本划分的内置函数:

*   空白符号的默认拆分:

```
test_string.split()Out[1]: ['The', 'quick', 'brown', 'fox', 'jumps', 'over', 'the', 'lazy', 'dog']
```

*   按确定的令牌数拆分:

```
test_string.split(' ', 2)Out[2]: ['The', 'quick', 'brown fox jumps over the lazy dog']
```

*   字符串的反向拆分:

```
test_string.rsplit(' ', 2)Out[3]: ['The quick brown fox jumps over the', 'lazy', 'dog']
```

*   按自定义符号拆分(当然，所有函数都支持此功能):

```
test_string.split('e')Out[4]: ['Th', ' quick brown fox jumps ov', 'r the lazy dog']
```

*   将字符串拆分成所需的标记，前后都有标记:

```
test_string.partition('fox')Out[5]: ('The quick brown ', 'fox', ' jumps over the lazy dog')
```

## 4.剥离和填充

另一个重要的特性是能够从字符串中删除多余的前导和尾随符号。为此，我们有`strip()`功能系列:

*   默认情况下，剪切空格。
*   从左侧或右侧剪切空格。
*   剪切自定义符号。

此外，还有一个用前导零填充数字的有用特性:

## 5.解构和再造

文本生成需要从单词字典中构造句子和短语。作为一个过程，这是字符串拆分的逆过程。Python 允许我们使用内置的字符串方法`join()`，将单词连接回句子中:

## 6.删除标点符号

这是文本清理工具集的另一个用例。Python 的`string`模块[有很多内置的常数](https://docs.python.org/3/library/string.html)和一组独立的符号。`string.punctuation`是其中之一，所以我们将使用它进行字符串消毒。

## 7.处理案例

文本格式化是每个数据科学家的痛苦。没有连续格的词和不同格式的句子，在数据清理时造成了很大的麻烦。但是，对于这些任务，还有其他 Python 函数:

## 8.正则表达式的世界

有时文本清理不容易由确定的符号或短语来提供。我们需要使用一些模式来代替。它是正则表达式使用的完美字段，也是合适的 Python 模块。

我们不会讨论正则表达式的所有功能，但是我们将关注它的应用——例如，数据分割和替换。是的，上面描述了这些任务，但是这里有一个更强大的替代方法。

按模式分割:

替换为模式:

## 9.对字符串进行标记

是时候收集我们之前学到的所有技巧，并将其应用于真正的标记化了。但是，我们不会重复所有的代码。这里有一个使用`pandas`的很酷的替代例子。在我们的例子中，我们应该清除字符串中的额外符号，转换成单个大小写，并将其拆分成标记。

## 10.(奖励)找到子串

在任何清洁工作之前，我们必须确定我们是否真的需要清洁。在大多数情况下，问题会缩小到在文本中搜索某个符号或短语。Python 为我们的目的提供了很多函数。

尾随子字符串:

```
test_string.endswith('dog')Out[1]: True
```

前导子字符串:

```
test_string.startswith('dog')Out[2]: False
```

常规检查:

```
'fox' **in** test_stringOut[3]: True
```

获取子字符串的索引:

```
test_string.find('fox')Out[4]: 16
```

当然，任何任务都可以用多种方式解决，尤其是当我们谈论 Python 的时候。但是，我认为我对字符串解析的理解会对你有用。

你可以在我的 Github 上找到带有工作代码的 Jupyter 笔记本:

[](https://github.com/Midvel/medium_jupyter_notes/blob/master/string_manip/string-tokenization-parsing.ipynb) [## 中级/中等 _jupyter_notes

### permalink dissolve GitHub 是 4000 多万开发人员的家园，他们一起工作来托管和审查代码，管理…

github.com](https://github.com/Midvel/medium_jupyter_notes/blob/master/string_manip/string-tokenization-parsing.ipynb) 

另外，请确保您没有错过我之前关于 Python 习惯用法的文章:

[](https://medium.com/better-programming/10-tiny-python-idioms-for-collections-and-data-structures-2f0d2923832) [## 集合和数据结构的 10 个小 Python 习惯用法

### 以最 Pythonic 化的方式使用容器

medium.com](https://medium.com/better-programming/10-tiny-python-idioms-for-collections-and-data-structures-2f0d2923832) 

请分享你最喜欢的 Python 技术！
# 词干:如何为搜索标记文本

> 原文：<https://betterprogramming.pub/stemming-how-to-tokenize-text-for-search-2e43bb3dd0ea>

## 一些关键概念

![](img/e61a39ec4ef25ee132ace6dc7da5c0bb.png)

安东尼·坎廷在 Unsplash[上的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

当开发一个文本搜索系统时，首要任务是如何索引数据。最常见的文本搜索类型是基于标记的搜索。用户输入一个查询，该查询被标记化，并且在文档的语料库中搜索最佳匹配。

文本如何标记是用户如何找到文档的一个重要因素。本文涵盖了在搜索系统中标记文本以进行索引时要考虑的一些关键概念。像 [Elasticsearch](https://www.elastic.co/) 这样的企业搜索系统内置了所有讨论过的功能，但是最好理解一下概念。所有的例子都是用 Python 编写的，但是所涉及的概念是与编程语言无关的。

# 分割代币

非结构化文本并不是真正的非结构化，它只是使用一种语言模型来结构化。空格、句点、逗号和连字符都是文本的结构，因此人类可以处理和理解。计算机不像我们一样工作，需要帮助处理文本流。

拆分文本最简单的方法是在空白上，如下图所示:

```
>>> "This is a test for text-based search".split()
['This', 'is', 'a', 'test', 'for', 'text-based', 'search']
```

一种类似但略有不同的拆分文本的方法是使用正则表达式。请考虑以下情况:

```
>>> import re
>>> re.split(r"\W", "This is a test text-based search")
['This', 'is', 'a', 'test', 'for', 'text', 'based', 'search']
```

这给出了一个稍微不同的结果。以上是对非文字字符的拆分(定义为`[A-Za-z0–9_]`)。连字符现在被视为拆分字符。两者都不是错误的分裂方式——它们只是不同而已。在决定拆分文本的最佳方式之前，先了解用户的期望。

# 区分大小写

区分大小写是另一个重要的概念。姓名、地址、城市、州和国家都有大写字母和小写字母。这些是本文的特点。例如，如果以下文本被拆分:

```
>>> "Paris is the capital of France".split()
['Paris', 'is', 'the', 'capital', 'of', 'France']
```

上面的令牌保留他们的情况。区分大小写的令牌可能很有价值。在某些情况下，如果用户搜索“巴黎”，可能不希望匹配“巴黎”[单词嵌入](https://en.wikipedia.org/wiki/Word_embedding)和文本相似度系统对于大小写混合记号有不同的理解。如果优先考虑查找信息，不区分大小写的搜索通常最适合用户。

要构建不区分大小写的令牌，只需使文本小写即可:

```
>>> "Paris is the capital of France".lower().split()
['paris', 'is', 'the', 'capital', 'of', 'france']
```

以上内容允许用户查找给定令牌的所有匹配，而不考虑大小写。

# 堵塞物

词干化是将一个单词缩减为其词根的过程。例如，单词“develop”也可以采用“发达”或“发展中”的形式标记化后，这三个单词会产生不同的标记。用户可能会在查询时使用尾随通配符搜索(即`develop*`)来解决这个问题。词干化是在索引时处理这个问题的一个选项。

下一个例子使用 NLTK，如果还没有安装，可以通过 pip 安装。

```
pip install nltk
```

NLTK 提供了一个词干分析器，可以将单词减少到它们的词根。请考虑以下情况:

```
>>> from nltk.stem.porter import PorterStemmer
>>> stemmer = PorterStemmer()
>>> [stemmer.stem(token) for token in ["developed", "develop", "developing"]]
['develop', 'develop', 'develop']
```

所有三个单词都解析为同一个单词。当用户输入带有“developed”、“developing”或“develop”的查询时，该查询将匹配带有这三个“develop”变体中任何一个的文档。再说一次，词干不一定是对的或错的——它取决于用户在搜索时的期望。

# 停止言语

在索引大量文本时，最常见的标记为用户提供的价值最少。像“a”、“an”、“is”、“this”、“will”这样的词都频繁出现，在查询时不会给用户提供太多的价值。

在索引和查询之前，停用字词过滤会删除特定的标记。它还有一个副作用，就是减少了整个索引的大小。

回到最初的例子，停用词过滤可以这样添加:

```
>>> tokens = "This is a test for text-based search".lower().split()
>>> [t for t in tokens if t not in ["a", "for", "is", "this"]]
['test', 'text-based', 'search']
```

现在只存储了三个令牌，而不是七个。存储最独特和最相关的标记，过滤掉普通和不太相关的标记。

# **过滤**

停用字词是一种令牌过滤。某些索引可能需要额外的过滤来帮助检索最佳匹配。过滤的例子是只接受字母数字标记，要求标记具有一定长度，或者拒绝具有数字的标记。

以下示例说明了要求令牌仅包含字母字符的令牌过滤器:

```
>>> import re
>>> tokens = "1234 test1234 rejected".lower().split()
>>> [t for t in tokens if re.match(r"^[a-z]+$", t)]
['rejected']
```

带数字的令牌被拒绝。如果用户在查询中输入数字标记，它不会匹配任何文档。

# 示例标记器

下面是一个完整的符号化器，把所有这些概念放在一起。这个类接受执行所解释的每个概念的选项。

示例标记器

# 结论

当涉及到对文本进行标记时，在索引时需要记住一些重要的选项。数据的标记化方式最终决定了数据的搜索方式，并应考虑用户对如何查找数据的期望。

感谢阅读！
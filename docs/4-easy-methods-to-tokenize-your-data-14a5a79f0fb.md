# 4 种简单的数据令牌化方法

> 原文：<https://betterprogramming.pub/4-easy-methods-to-tokenize-your-data-14a5a79f0fb>

## 使用以下四种简单的方法标记您的数据

![](img/47d37767b0578881b09cd8db388f5ad2.png)

照片由[格伦·凯莉](https://unsplash.com/@glencarrie?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 介绍

最近一直在探索自然语言处理(NLP)的世界。这个领域是机器学习、语言学和计算机科学的交叉领域，研究计算机如何解释和使用语言。这是数据科学最令人兴奋的部分之一，因为它可以帮助企业，例如，从评论中提取有意义的信息。

在本文中，我想解释任何 NLP 项目中至关重要的一步，标记化，以及如何实现它。

# 这是什么？

标记化是几乎所有 NLP 项目的第一种数据预处理方法。它包括将你的输入文本分解成更小的片段，如单词或句子。

这很有用，因为它允许机器学习算法有效地运行，因为您的数据现在是离散的数据包，而不是非结构化的堆。

# 履行

我们现在将通过一些方法来实现单词标记化。

## 1.Python 的。split()方法

Python 作为名为`.split()` 的字符串类型/对象的内置方法，其工作方式如下:

```
# name a string
string = "Hello, I'm Egor. What's your name?"
# call the split method
string.split()
```

输出:

```
['Hello,', "I'm", 'Egor.', "What's", 'your', 'name?']
```

这个很好用！唯一的问题是它包括标点符号作为单词 token 的一部分。理想情况下，标点符号应该被认为是它自己的记号。

## 自然语言工具包(NLTK)

NLTK 是一个 Python 包，包含许多 NLP 工具和模型，面向学习和研究。NLTK 包提供了一个单词标记器函数，方便地命名为`word_tokenize` ***。*** 这可以这样实现:

```
# import the package
import nltk
#download the language models
nltk.download()# define sentence
string = "Hello, I'm Egor. What's your name?"# use tokenizer
from nltk.tokenize import word_tokenize
word_tokenize(string)
```

输出:

```
['Hello', ',', 'I', "'m", 'Egor', '.', 'What', "'s", 'your', 'name', '?']
```

啊，这样好多了！标点符号被符号化了。

## 宽大的

spaCy 是另一个 NLP 库，但被认为比 NLTK 更“最新”,并提供生产就绪软件。

```
# download spacy model in jupyter notebook
!python -m spacy download en_core_web_sm# import and tokenize
import spacy
model = spacy.load("en_core_web_sm")
doc = model(string)
tokens =[]
for token in doc:
    tokens.append(token.text)

tokens
```

输出:

```
['Hello', ',', 'I', "'m", 'Egor', '.', 'What', "'s", 'your', 'name', '?']
```

我们可以看到 spaCy 的输出和 NLTK 一样。

## 根西姆

Gensim 主要是一个主题建模库，但也包含了`tokenize` 函数，其用法如下:

```
# import package
from gensim.utils import tokenize# define sentence
string = "Hello, I'm Egor. What's your name?"# get tokens
list(tokenize(string))
```

输出:

```
['Hello', 'I', 'm', 'Egor', 'What', 's', 'your', 'name']
```

Gensim 的有用之处在于它基于标点符号进行拆分，所以它们不包含在令牌列表中！

# 结论

在本文中，我们展示了如何使用四个不同的库来标记 NLP 项目的数据。我在下面附上了这些库的链接，这样你可以更多地探索它们的功能！

*   https://www.nltk.org
*   spaCy : [https://spacy.io](https://spacy.io)
*   根西姆:【https://radimrehurek.com/gensim/】T2

你可以在这里查看上面写的完整代码:

[](https://github.com/egorhowell/Medium-Articles/blob/main/Data%20Science%20Basics/methods_to_tokenize.py) [## Medium-Articles/methods _ to _ token ize . py at main egorhowell/Medium-Articles

### 此文件包含双向 Unicode 文本，其解释或编译可能与下面显示的不同…

github.com](https://github.com/egorhowell/Medium-Articles/blob/main/Data%20Science%20Basics/methods_to_tokenize.py) 

# 和我联系！

*   [*要想在媒体上阅读无限的故事，请务必在这里注册！*](/@egorhowell/membership) 💜
*   [*在我发布注册邮件通知时获得更新！*T13*😀*](/subscribe/@egorhowell)
*   [*领英*](https://www.linkedin.com/in/egor-howell-092a721b3/) 👔
*   [*碎碎念*](https://twitter.com/EgorHowell) 🖊
*   [*github*](https://github.com/egorhowell)*🖥*
*   *[](https://www.kaggle.com/egorphysics)**🏅***

> ***(所有表情符号由 [OpenMoji](https://openmoji.org/) 设计——开源表情符号和图标项目。许可证: [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/#)***
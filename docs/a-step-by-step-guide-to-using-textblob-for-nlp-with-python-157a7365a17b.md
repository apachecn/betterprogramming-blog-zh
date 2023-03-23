# 通过 Python 使用 TextBlob for NLP 的分步指南

> 原文：<https://betterprogramming.pub/a-step-by-step-guide-to-using-textblob-for-nlp-with-python-157a7365a17b>

## *一个方便的库来完成你所有的文本处理任务*

![](img/cf5b49c2d5c7e211f8f8b35a7cadd123.png)

Jess Bailey 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

本文将指导您使用 [TextBlob](https://textblob.readthedocs.io/en/dev/) 库完成文本分析过程。这个库运行在 NLTK 包之上，并提供大部分的文本处理分析。

作为一个自然语言处理领域，文本分析正在迅速发展，出现了一系列应用，如聊天机器人、招聘分析、语言提取、社交网站分析等。

TextBlob 库为大多数自然语言处理任务提供了建模和特征提取的分类器。

# 涵盖的主题

```
1\. Installing the textblob library
2\. Creating textblob and POS tagging
3\. Tokenization
4\. Lemmatization
5\. WordNet and WordList
6\. Parsing
7\. N-Gram
8\. Sentiment analysis
9\. Word and Phrase Frequencies 
10\. Correction of spellings
```

# ***安装 TextBlob 库***

TextBlob 的安装非常简单。我们可以打开一个 Anaconda 提示符，如果有任何其他环境，我们可以选择它。否则，我们可以将其安装在基础环境中。要在 Anaconda 中安装，命令如下所示:

```
conda install -c conda-forge textblob
```

# ***创建文本块和词性标注***

创建一个`textblob`非常简单。我们只需要从 TextBlob 库中导入`Textblob`。

```
from textblob import TextBlob
```

这个`TextBlob`是一个用于文本分析的`textblob`类。现在，我们将创建一个该类的对象，用于进一步的分析。

```
string = TextBlob("Hello all, I am Amit Chauhan from India")
```

我们将借助于类中的`tags`方法，看到这个字符串的部分语音方法。这种标记方法将返回带有单词及其词性的元组。

```
string.tags**#output:**
[('Hello', 'NNP'),
 ('all', 'DT'),
 ('I', 'PRP'),
 ('am', 'VBP'),
 ('Amit', 'NNP'),
 ('Chauhan', 'NNP'),
 ('from', 'IN'),
 ('India', 'NNP')]
```

# ***标记化***

标记化是自然语言处理的一个重要部分，它将语句划分为单词。TextBlob 中标记化最神奇的地方在于，它可以将句子分解成单词和句子，如下例所示:

# ***词汇化***

词汇化是用来把一个句子变成它的词根。我们在`textblob`类中使用了`lemmatize`方法来查找带有词根的单词列表。

如果我们没有在`lemmatize`方法中传递任何东西，那么它将返回单词的名词。我们也可以通过词性来获得相应的单词。

这些是单词的例子。现在我们将看到一个带有词汇化的句子:

```
sent = "Today is a beautiful day"
a = sent.split(" ")
print(a)
print([Word(word).lemmatize() for word in a])
print([Word(word).lemmatize("v") for word in a])**#output:**
['Today', 'is', 'a', 'beautiful', 'day']
['Today', 'is', 'a', 'beautiful', 'day']
['Today', '**be**', 'a', 'beautiful', 'day']
```

# ***WordNet 和 WordList***

WordNet 是用于查找同义词和其他信息的单词集合。TextBlob 库使用 NLTK 的 WordNet 的数据库。我们可以使用`synset`方法获取被搜索单词的单词或句子，如下所示:

`WordList`是 TextBlob 库中的一个类，包含了`upper`、`lower`、`lemmatize`、`stem`、`append`、`pluralize`、`count`等方法。

# ***解析***

这就像一个句子的解析树，通过检查动词短语、名词短语等来找到要进行语法构造的句子。

```
string = TextBlob("Today")
print(string.parse())**#output:**
Today/NN/B-NP/O
```

# **N-Gram**

TextBlob 为句子提供了易于使用的 n 元语法。作为回报，当我们在`ngram`方法中给出 n-gram 的值时，这个单词数就是一个列表。ngram 的示例如下所示:

# ***情绪分析***

情感分析用于检查一个单词或句子的情感是积极的还是消极的。

```
from textblob import TextBlob
from textblob.sentiments import NaiveBayesAnalyzer
string = TextBlob("I am happy today", analyzer=NaiveBayesAnalyzer())
string.sentiment**#output:**
Sentiment(classification='pos', p_pos=0.7367024460316827,
                                p_neg=0.2632975539683174)
```

# ***词频***

这用于统计一个单词/短语在文本中的出现频率。有两种方法:用`word_counts`方法和用`count`方法。

```
#the first method
text = TextBlob("hip hip hurray")
text.word_counts['hip']**#output:**
2#the second method
text.words.count('hip', case_sensitive=True)**#output:**
2
```

# ***纠正拼写***

TextBlob 的这一方面用于纠正句子中写错的单词，如下所示:

```
text = TextBlob("Today is a beautif day")
print(text.correct())**#output:**
Today is a beautiful day
```

# ***结论***

TextBlob 对于自然语言处理任务非常有用，比 spacy 和 NLTK 有更多的内置方法。

我希望你喜欢这篇文章。

我希望你喜欢这篇文章。通过我的 [LinkedIn](https://www.linkedin.com/in/data-scientist-95040a1ab/) 和 [Twitter](https://twitter.com/amitprius) 联系我。

# 推荐文章

*   [NLP —用 Python 从零到英雄](https://medium.com/towards-artificial-intelligence/nlp-zero-to-hero-with-python-2df6fcebff6e?sk=2231d868766e96b13d1e9d7db6064df1)
*   [Python 数据结构数据类型和对象](https://medium.com/towards-artificial-intelligence/python-data-structures-data-types-and-objects-244d0a86c3cf?sk=42f4b462499f3fc3a160b21e2c94dba6)
*   [Python 中的异常处理概念](https://pub.towardsai.net/exception-handling-concepts-in-python-4d5116decac3?source=friends_link&sk=a0ed49d9fdeaa67925eac34ecb55ea30)
*   [为什么 LSTM 在深度学习方面比 RNN 更有用？](https://pub.towardsai.net/deep-learning-88e218b74a14?source=friends_link&sk=540bf9088d31859d50dbddab7524ba35)
*   [神经网络:递归神经网络的兴起](https://pub.towardsai.net/neural-networks-the-rise-of-recurrent-neural-networks-df740252da88?source=friends_link&sk=6844935e3de14e478ce00f0b22e419eb)
*   [用 Python 全面解释线性回归](https://medium.com/towards-artificial-intelligence/fully-explained-linear-regression-with-python-fe2b313f32f3?source=friends_link&sk=53c91a2a51347ec2d93f8222c0e06402)
*   [用 Python 全面解释逻辑回归](https://medium.com/towards-artificial-intelligence/fully-explained-logistic-regression-with-python-f4a16413ddcd?source=friends_link&sk=528181f15a44e48ea38fdd9579241a78)
*   [Python 中 concat()、merge()和 join()的区别](https://pub.towardsai.net/differences-between-concat-merge-and-join-with-python-1a6541abc08d?source=friends_link&sk=3b37b694fb90db16275059ea752fc16a)
*   [与 Python 的数据争论—第 1 部分](https://pub.towardsai.net/data-wrangling-with-python-part-1-969e3cc81d69?source=friends_link&sk=9c3649cf20f31a5c9ead51c50c89ba0b)
*   [机器学习中的混淆矩阵](https://medium.com/analytics-vidhya/confusion-matrix-in-machine-learning-91b6e2b3f9af?source=friends_link&sk=11c6531da0bab7b504d518d02746d4cc)
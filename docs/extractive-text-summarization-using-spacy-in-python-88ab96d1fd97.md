# Python 中使用 spaCy 的抽取式文本摘要

> 原文：<https://betterprogramming.pub/extractive-text-summarization-using-spacy-in-python-88ab96d1fd97>

## 根据使用 spaCy 提取的关键字，从文章中找到排名靠前的句子

![](img/aee8381919d69250c57cf37ba80080e3.png)

由[罗曼·维尼斯](https://unsplash.com/@rvignes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/text?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

传统上，TF-IDF(Term Frequency-Inverse Data Frequency)经常被用于信息检索和文本挖掘，以计算用于文本摘要的句子的重要性。

TF-IDF 重量由两项组成:

*   TF:术语频率—测量术语在文档中出现的频率。因为每个文档的长度不同，所以一个术语在长文档中出现的次数可能比短文档多得多。因此，作为标准化的一种方式，术语频率通常除以文档长度，例如文档中的术语总数。

```
TF(t) = (Number of times term t appears in a document) / (Total number of terms in the document)
```

*   IDF:逆文档频率—衡量术语的重要性。在计算术语频率时，所有术语都被视为同等重要。但是，众所周知，某些术语可能会出现很多次，但在文档中并不重要。我们通常称这些词为*停用词*。例如:是，是，他们，等等。

```
IDF(t) = log_e(Total number of documents / Number of documents with term t in it)
```

如果您要处理多个域，TF-IDF 不是一个好的选择。不平衡的数据集往往会有偏差，这将极大地影响结果。

一个领域中的普通术语可能是另一个领域中的重要术语。俗话说:“一个人的肉是另一个人的毒药。”

让我们试着用不同的方法，只使用重要的术语。这篇文章的重点是找出一篇文章中的顶级句子，如下所示:

1.  使用 [spaCy 的](https://spacy.io/)语言模型标记文章。
2.  提取重要关键词，计算归一化权重。
3.  根据关键词出现，计算文章中每个句子的重要性。
4.  根据计算出的重要性对句子进行排序。

让我们进入下一部分，开始安装必要的模块。

# 1.设置

我们将使用`pip install`下载空间模块。强烈建议您在继续之前创建一个虚拟环境。

在管理员模式下运行终端，因为我们稍后下载语言模型时将需要管理员权限来创建符号链接。

```
pip install -U spacy
```

安装完成后，下一步是下载语言模型。在本教程中，我将使用大的英语模型。请随时查看[官方网站，了解可用型号的完整列表](https://spacy.io/usage/models)。

```
python -m spacy download en_core_web_lg
```

下载模型需要一些时间，因为它的大小约为 800MB。如果您在下载模型时遇到问题，您可以尝试使用较小的语言模型。

完成后，运行以下命令检查 spaCy 是否正常工作。它还显示了已安装的型号。

```
python -m spacy validate
```

让我们继续下一部分。我们将用 Python 写一些代码。

# 2.履行

## 导入

在 Python 文件的顶部添加以下导入声明。

```
import spacy
from collections import Counter
from string import punctuation
```

`Counter`用于统计频率，而`punctuation`包含最常用的标点符号。

# 负载空间模型

空间模型可以通过两种方式加载。第一种是通过内置的加载函数。

```
nlp = spacy.load("en_core_web_lg")
```

如果您遇到无法加载模型的问题，即使已经安装了模型，您也可以通过第二种方法加载模型。您需要直接导入模块，并可以使用它来加载模型。

```
import en_core_web_lgnlp = en_core_web_lg.load()
```

# 顶句功能

我们将在函数内部编写代码。尽可能模块化你的代码总是一个好主意。

```
def top_sentence(text, limit):
```

该函数接受两个输入参数:

*   `text` —输入文本。可以是一小段，也可以是一大堆文字。
*   `limit` —返回的句子数量。

第一部分是对输入文本进行分词，找出其中重要的关键词。

*   `#1` —将输入文本转换为小写，并使用 spaCy 的语言模型对其进行标记。
*   `#2` —循环遍历每个令牌。
*   `#3` —如果令牌是停用字词或标点符号，则忽略该令牌。
*   `#4` —如果令牌是我们已经定义的词性标签，则将它附加到列表中。

我之前介绍过一个关于从文本中提取关键词和标签的教程。请随意查看。

下一步是标准化关键词的权重。

```
freq_word = Counter(keyword) #5
max_freq = Counter(keyword).most_common(1)[0][1] #6
for w in freq_word:
    freq_word[w] = (freq_word[w]/max_freq) #7
```

*   `#5` — `Counter`将把列表转换成一个字典，其中包含各自的频率值。
*   `#6` —获取最常用关键字的频率。
*   `#7` —循环字典中的每一项，并归一化频率。最常用的关键字的频率值为 1。

如果您要在计算后打印出`freq_word`变量，您应该会看到一个`Counter`对象，它包含一个字典，其规范化值的范围是从 0 到 1。

```
Counter({'ghosn': 1.0, 'people': 0.625, 'equipment': 0.625, 'japan': 0.625, 'yamaha': 0.5, 'musical': 0.5, 'escape': 0.5, 'cases': 0.375, 'japanese': 0.375})
```

让我们通过识别重要关键字的出现来计算句子的重要性，并总结其价值。

*   `#8` —循环文本中的每个句子。句子由基于句号标点的空间模型分割。
*   `#9` —基于空间标记化循环句子中的每个单词。
*   `#10` —根据我们之前提取的关键词确定该单词是否为关键词。
*   `#11` —将规范化的关键字值添加到句子的键值对中。
*   `#12` —在`sent_strength`字典中创建一个新的键值，使用句子作为键，规范化的关键字值作为值。

您应该能够获得每个句子的累积归一化值。我们将使用该值来确定排名靠前的句子。

*   `#13` —根据归一化值对字典进行排序。将反转参数设置为`True`进行降序排列。
*   `#14` —循环遍历每个已排序的项目。
*   `#15` —将结果附加到列表中。句子的第一个字母是大写的，因为我们在标记化过程中已经将文本转换为小写。请注意，句子中的所有其他单词仍然是小写的。如果你想在句子中保持大写，请实现你自己的映射系统。
*   `#16` —如果计数器超过我们设定的限值，则中断循环。这决定了函数将返回多少个句子。
*   `#17` —通过用空格连接每个元素，以字符串形式返回列表。

你的功能应该如下。

# 结果

让我们测试一下我们刚刚创建的函数。在你的测试中可以随意使用任何文本。我将使用以下文本作为本教程的例子。

```
example_text = '''Yamaha is reminding people that musical equipment cases are for musical equipment — not people — two weeks after fugitive auto titan Carlos Ghosn reportedly was smuggled out of Japan in one. In a tweet over the weekend, the Japanese musical equipment company said it was not naming any names, but noted there had been many recent stories about people getting into musical equipment cases. Yamaha (YAMCY) warned people not to get into, or let others get into, its cases to avoid "unfortunate accidents." Multiple media outlets have reported that Ghosn managed to sneak through a Japanese airport to a private jet that whisked him out of the country by hiding in a large, black music equipment case with breathing holes drilled in the bottom. CNN Business has not independently confirmed those details of his escape. The former Nissan (NSANF) CEO had been out on bail awaiting trial in Japan on charges of financial wrongdoing before making his stunning escape to Lebanon at the end of December. Ghosn has referred to his departure as an effort to "escape injustice." In an interview with CNN\'s Richard Quest last week, Ghosn did not comment on the nature of his escape, saying he didn\'t want to endanger any of the people who aided in the operation. Ghosn did, however, respond to a question about what it felt like to ride through the airport in a packing case by first declining to comment but then adding: "Freedom, no matter the way it happens, is always sweet." In a press conference in Lebanon ahead of the CNN interview last Wednesday, Ghosn\'s first public appearance since fleeing Japan, Ghosn said he decided to leave the country because he believed he would not receive a fair trial, a claim Japanese authorities have disputed. Brands sometimes capitalize on their tangential relationship to big news in order to attract attention on social media. Yamaha is one of Japan\'s best known brands and Ghosn was one of Japan\'s top executives before being ousted from Nissan — a match made in social media heaven. Not surprisingly, Yamaha\'s post went viral on Twitter over the weekend.'''
```

记住为`top_sentence`函数传递要返回的句子数量。

```
print(top_sentence(example_text, 3))
```

我得到了以下结果:

```
"Yamaha is reminding people that musical equipment cases are for musical equipment — not people — two weeks after fugitive auto titan carlos ghosn reportedly was smuggled out of japan in one. In a press conference in lebanon ahead of the cnn interview last wednesday, ghosn's first public appearance since fleeing japan, ghosn said he decided to leave the country because he believed he would not receive a fair trial, a claim japanese authorities have disputed. In a tweet over the weekend, the japanese musical equipment company said it was not naming any names, but noted there had been many recent stories about people getting into musical equipment cases."
```

# 未来改进

虽然我们已经实现了一个简单的摘要文本摘要功能，但是仍然有很多可以根据您的使用情况进行改进的地方。例子包括:

*   保留结果的大小写敏感性。
*   句子之间的连接因为句子的顺序会乱序，最终输出。对于基于故事的文本来说，结果并不好。
*   长句和短句之间的规范化。

# 3.结论

让我们回顾一下今天所学的内容。

我们从简单解释 TF-IDF 和我们方法的不同开始。然后，我们继续安装必要的模块和语言模型。

接下来，我们实现了一个自定义函数来从一段文本中获取顶部的句子。

在每个函数调用期间，我们将提取关键字，归一化频率值，计算句子的重要性，根据其重要性值对句子进行排序，并根据我们传递给函数的限制返回结果。

最后，我们探索了如何基于我们自己的用例进一步改进它。

感谢您的阅读，希望在下一篇文章中再次见到您！

# 参考

1.  [使用 Python 中的 spaCy 提取关键词](https://medium.com/better-programming/extract-keywords-using-spacy-in-python-4a8415478fbf)
2.  [TF-IDF:单页教程](http://www.tfidf.com/)
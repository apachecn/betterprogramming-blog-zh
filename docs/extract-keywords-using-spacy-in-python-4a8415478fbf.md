# 在 Python 中使用 spaCy 提取关键字

> 原文：<https://betterprogramming.pub/extract-keywords-using-spacy-in-python-4a8415478fbf>

## 从一篇文章中找到热门关键词并生成标签

![](img/3070a0a4b34aa661d845ec71daac3a91.png)

照片由 [Unsplash](https://unsplash.com/s/photos/scrabble?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[émile Perron](https://unsplash.com/@emilep?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄

在这篇文章中，你将学习如何从一大块文本中提取最重要的关键词——一篇文章、一篇学术论文，甚至是一条简短的推文。你可以自由地使用它来生成标签，计算句子的重要性等等。

在本教程中，我将使用一个名为 spaCy 的工业级自然语言处理模块。我以前用 spaCy 做过一个关于[相似性匹配的教程——你可以随意看看。本教程分为三个部分:](https://medium.com/better-programming/the-beginners-guide-to-similarity-matching-using-spacy-782fc2922f7c)

*   设置
*   履行
*   结论

# 1.设置

我们将通过 pip 安装来安装空间模块。下载语言模型时，创建符号链接需要管理权限。在管理员模式下打开终端。强烈建议在运行以下命令之前创建一个虚拟环境:

```
pip install -U spacy
```

下一步是下载您选择的语言模型。在本教程中，我将使用大的英语模型。请随时查看[官网，了解可用型号的完整列表](https://spacy.io/usage/models)。

## en_core_web_lg(大)

```
python -m spacy download en_core_web_lg
```

模型的文件大小约为 800MB。如果您想尝试一下，请下载语言模型的较小版本。

## en_core_web_md(中型)

中型型号要小得多，只有 100MB。

```
python -m spacy download en_core_web_md
```

## en_core_web_sm(小型)

最小的英语语言模型应该只需要一会儿就可以下载，因为它大约有 11MB。

```
python -m spacy download en_core_web_sm
```

完成后，运行以下命令检查 spaCy 是否正常工作。它还显示了已安装的型号。

```
python -m spacy validate
```

让我们转到下一节，开始用 Python 写一些代码。

# 2.履行

## 导入

首先，我们需要在文件的顶部添加一个导入声明。

```
import spacy
```

除了空间，我们还需要以下进口。`Counter`将用于根据频率对关键字进行计数和排序，而`punctuation`包含最常用的标点符号。

```
from collections import Counter
from string import punctuation
```

## 负载空间模型

我们可以通过下面的命令轻松地加载刚刚安装的模型。根据您安装的型号的名称修改字符串。

```
nlp = spacy.load("en_core_web_lg")
```

如果您遇到无法加载模型的问题，即使已经安装了模型，您也可以用不同的方式加载模型。让我们直接导入模块，您可以使用它来加载模型。

```
import en_core_web_lgnlp = en_core_web_lg.load()
```

## 热门词汇功能

我们将在函数中编写关键字提取代码。这要方便得多，当我们需要从一大块文本中提取关键词时，我们可以很容易地调用它。它接受一个字符串作为输入参数。

包含我们想要提取的词性标签的列表。在本教程中，我将只使用`PROPN`(专有名词)`ADJ`(形容词)和`NOUN`(名词)。如果您想提取另一个词性标签，如动词，请根据您的要求扩展列表。

`#2`将输入文本转换成小写，并通过我们之前加载的 spacy 模型对其进行标记。将返回一个处理过的`Doc`对象。该对象包含基于标记化过程的`Token`对象。

`#3`循环遍历每个标记，确定标记化的文本是否是停用词或标点符号的一部分。忽略此标记，如果是，则继续下一个标记。

`#4`如果标记化文本的词性标签是我们之前指定的，则存储结果。

`#5`以字符串列表的形式返回结果。

让我们使用您选择的简单文本来测试一下。我使用以下输入文本:

```
output = get_hotwords('''Welcome to Medium! Medium is a publishing platform where people can read important, insightful stories on the topics that matter most to them and share ideas with the world.''')print(output)
```

运行该函数后，我获得了以下结果。

```
['welcome', 'medium', 'medium', 'publishing', 'platform', 'people', 'important', 'insightful', 'stories', 'topics', 'ideas', 'world']
```

## 删除重复项目

请注意，如果我们刚刚编写的函数在输入文本中包含相同的重要关键字，它就会包含重复项。在这种情况下，关键字`medium`重复两次。您可以通过`set`功能轻松移除它:

```
output = set(get_hotwords('''Welcome to Medium! Medium is a publishing platform where people can read important, insightful stories on the topics that matter most to them and share ideas with the world.'''))print(output)
```

您应该能够获得以下输出:

```
{'medium', 'ideas', 'publishing', 'important', 'stories', 'people', 'insightful', 'platform', 'world', 'topics', 'welcome'}
```

## 从关键字生成标签

通过在每个关键字的开头附加散列符号，可以很容易地从关键字生成散列标签。最简单的方法是使用列表理解法。您需要用空格将结果列表连接起来，以生成一个 hashtag 字符串:

```
output = set(get_hotwords('''Welcome to Medium! Medium is a publishing platform where people can read important, insightful stories on the topics that matter most to them and share ideas with the world.'''))hashtags = [('#' + x) for x in output]
print(' '.join(hashtags))
```

当您运行它时，将显示以下结果:

```
#medium #ideas #publishing #important #stories #people #insightful #platform #world #topics #welcome
```

## 按频率排序

可能存在关键字的顺序基于频率的情况。在这种情况下，您需要根据关键词出现的频率对它们进行排序——使用`Counter`模块来排序并获取最频繁出现的关键词。`Counter`模块有一个`most_common`函数，它接受一个整数作为输入参数。记住，必须去掉`set`函数，才能保留每个关键字的出现频率。

```
output = get_hotwords('''Welcome to Medium! Medium is a publishing platform where people can read important, insightful stories on the topics that matter most to them and share ideas with the world.''')hashtags = [('#' + x[0]) for x in Counter(output).most_common(5)]print(' '.join(hashtags))
```

在这些情况下，最常见的五个标签如下:

```
#medium #welcome #publishing #platform #people
```

# 3.结论

让我们回顾一下今天所学的内容。我们通过 pip 安装开始安装`spaCy`模块。然后我们下载了一个预先训练好的语言模型。在这种情况下，我下载了英文模型的大版本。

接下来，我们编写了一些简单的代码来实现我们自己的关键字提取器。我们定义了自己的`hotword`函数，它接受一个输入字符串并输出一列关键字。我们使用 Python 内置的`set`函数从结果中删除重复项。列表理解对于在每个关键字前面附加散列符号来创建 hashtags 字符串非常有帮助。最后，我们探索了`Counter`模块中的`most_common`函数，根据频率对关键词进行排序。

感谢你的阅读，我希望在下一篇文章中见到你！

# 参考

1.  [https://medium . com/better-programming/the-beginners-guide-to-similarity-matching-using-spacy-782 fc 2922 f7c](https://medium.com/better-programming/the-beginners-guide-to-similarity-matching-using-spacy-782fc2922f7c)
2.  [https://spacy.io/](https://spacy.io/)
3.  [https://github.com/explosion/spaCy](https://github.com/explosion/spaCy)
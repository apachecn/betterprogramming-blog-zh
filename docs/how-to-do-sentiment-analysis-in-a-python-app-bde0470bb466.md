# 如何在 Python 应用程序中进行情感分析

> 原文：<https://betterprogramming.pub/how-to-do-sentiment-analysis-in-a-python-app-bde0470bb466>

## 构建嵌入在 GUI 中的意见挖掘模型

![](img/55c29c0397ce7a11f9d2f6722ae7bb28.png)

凯尔·格伦在 [Unsplash](https://unsplash.com/s/photos/feelings?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在互联网和社交媒体时代，人们的真实意见正在成为营销和政治的基本资源。

反馈有助于公司改善服务，但是，对于一个人来说，手动分析数以百万计的反馈是不可能的。此外，一个人会犯很多错误。

为了避免这种风险并节省金钱和时间，公司开发了*情绪分析 AI* ，也称为*意见挖掘*。

观点挖掘是自然语言处理的一个热门分支，其目的是分析文档的极性。情感分析最常见的任务之一是基于作者表达的观点和情感的文档分类。

例如，如果一位政治家想知道他们在人们中的受欢迎程度，开始调查以查看结果，并将意见分为积极或消极，他们将使用意见挖掘模型。

在这个简短的介绍给我们一个主题的概述之后，让我们看看我们在这个教程中要做什么:

*   首先，我们将创建一个简单的情感分析模型，对电影评论进行分类(正面或负面)。
*   然后，我们将把模型嵌入到图形应用程序中。
*   作为本教程的总结，我将提出一些想法来使分类器适应其他用途。

在我们开始之前，我建议您注意代码上的注释。

# 下载数据集

在本教程中，我们将使用 IMDB 的电影评论数据集。

可以从 [Dropbox](https://www.dropbox.com/s/sxdfxweepcfu1jz/movie_s_a_dataset.csv.gz?dl=0) 下载。

# 创建模型

这些是您需要的 Python 包:

*   [泡菜](https://docs.python.org/3/library/pickle.html)
*   [回复](https://docs.python.org/3/library/re.html)
*   熊猫
*   [NLTK](https://pypi.org/project/nltk/)
*   [sklearn](https://pypi.org/project/sklearn/)
*   [os](https://docs.python.org/3/library/os.html)

为了构建我们的模型，我们将使用核外学习技术，这有助于我们处理大型数据集，避免高计算成本。

作为分类器，我们会使用 [scikit-learn 的 SGDClassifier](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.SGDClassifier.html) (随机梯度下降分类器)，我们会将其序列化(保存)在一个. pkl 文件中。

现在，让我们编码吧！

## Pkl_builder.py

该文件创建并序列化 SGDClassifier。

首先，我们导入所需的模块并下载`stopwords`:

但是什么是停用词呢？

停用词是那些在英语中非常常见的词，并且不包含关于文本的重要信息，所以最好识别并删除它们。

在我们分析文档之前，我们需要清理它们:我们删除所有不想要的字符，并通过 regex(正则表达式)库识别表情符号:

然后我们创建矢量器，它将文本转换成可分析的数据，以及分类器(我之前说过的随机梯度下降分类器):

好了，我们即将完成情感分析模型的创建，但在此之前，我们必须训练它并将其保存在一个`.pkl`文件中。请注意，我们还将对之前下载的停用词进行序列化:

这段代码将创建`classifier.pkl`和`stopwords.pkl`文件。

您的目录应该是这样的:

## 目录树

```
/movieclassifier    
    /pkl_objects        
        classifier.pkl        
        stopwords.pkl    
    pkl_builder.py
```

我们已经创建了我们的模型。来测试一下吧！

# 测试我们的模型

在上一步中，我们构建并序列化了分类器；现在我们要测试它。但在此之前，我们必须创建矢量器。等等，我们不是已经创造出来了吗？

是的，我们做了，但是我们创建了矢量器来训练模型，而不是测试它。幸运的是，代码相当相似，我们唯一要做的就是用下面的代码创建一个`vectorizer.py` 文件。

## 矢量器. py

如您所见，这段代码与我们之前开发的代码相同，只是它使用了我们之前创建的停用词文件。

为了测试代码，您需要创建另一个名为`model_test.py` 的 Python 文件，并键入以下代码。

## 模型 _ 测试. py

在这里，我们加载了分类器文件并定义了我们的分类函数，其中 X 变量是矢量化的文档(转换成可分析数据的文档)。*y 变量*是我们的模型对收到的评论做的预测。

然后它将短语“我喜欢这部电影。完美。”并打印我们模型的预测和预测正确的概率。

这就是你的目录现在应该的样子。

## 目录树

```
/movieclassifier    
    /pkl_objects       
        classifier.pkl       
        stopwords.pkl    
    pkl_builder.py
    model_test.py    
    vectorizer.py
```

测试一下！

打开您的终端(UNIX 用户)或命令提示符(Windows 用户),然后:

1.  进入`/movieclassifier`目录。
2.  通过键入:`python model_test.py`执行我们之前创建的脚本。

有用！厉害！

现在我们准备将模型嵌入到图形应用程序中。

# 将模型嵌入图形应用程序

为了将模型嵌入到应用程序中，我们将使用 [Kivy](https://kivy.org/) Python 包，所以我们需要先安装它:`pip install kivy`。

好了，我们可以开始编码了！

## **App.py(位于/movieclassifier 目录中)**

首先，我们导入所需的模块。之后，我们加载我们的分类器并粘贴我们在`model_test.py`中定义的分类函数。

现在我们将创建 Kivy 应用程序。请记住，要彻底理解代码，还得看注释。

这里，我们刚刚将模型嵌入到 kivy 应用程序中。

现在执行`app.py`，你将看到魔法！

# 结论

你做到了，你创建了自己的观点挖掘模型，你可能想知道之后该做什么。我可能有一些建议给你。

*   增强应用程序的图形，还有很多需要改进的地方。
*   将模型应用于其他目的。为此，您必须使用不同的数据集执行`pkl_builder.py`文件。您可以使用`dataset.csv`来代替`movie_s_a_dataset.csv`，这将使用另一个数据集来训练模型。例如，新款`dataset.csv`可能是这样的:

```
review,sentiment
"I really liked this amazon product, the quality is good and the delivery was ok.", 1
"Bad, really bad, when I got the product it was broken.", 0
```

显然，要创建一个像样的工作模型，数据集必须有至少 2000 个或更多的评论。事实上，我们之前使用的数据集大约有 25，000 条评论。
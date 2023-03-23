# 构建基于社交媒体行为的音乐推荐系统

> 原文：<https://betterprogramming.pub/build-a-music-recommendation-system-based-on-social-media-behavior-df7e1e26c67>

## 一个歌曲推荐系统，从社交媒体内容中找出用户的情绪

![](img/a7f3da8492c83bf8989fd84b184e9980.png)

布鲁斯·马斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

嗨，伙计们，我想和你们分享我最后一年的研究。选择这个领域的原因是我在选择歌曲时的个人经历。尽管我的手机上有一些我最喜欢的音乐，但我一直在寻找与我当前心情相关的东西。所以我想到开发一个系统来检测我们当前的情绪，并据此推荐一首歌曲。

# 考虑的领域

如上所述，我发现的主要研究空白是从社交媒体内容中识别复杂的情感类别。为此，我在 Twitter 平台上使用了自然语言处理技术来提取数据。

该实现由三个模块组成:英语内容情感检测、僧伽罗语内容情感检测和抒情情感检测。

这里我们只讨论英语内容情感的分析模型。你可以在我的 [GitHub](https://github.com/RaviduShehan/Music-Recommendation-System-based-on-Emotions-in-Users-social-media-behaviour) 上找到资源。

让我们开始为英语情感检测模型编码。

# 开发识别社交媒体内容中情绪的模型

在这里，我将讨论我用来从英语和非正式英语内容中检测情感的情感检测模型。在创建这个模型的时候，我在 [TensorFlow](https://www.tensorflow.org/) 之上使用了 [Keras](https://keras.io/) 。

第一步，你需要有标签数据集。为此，您可以从 [Kaggle](https://www.kaggle.com/praveengovi/emotions-dataset-for-nlp) 获取数据集。

获得数据集后，让我们导入必要的包。您可以随时使用 [pip](https://pip.pypa.io/en/stable/) 安装软件包。

现在，您已经导入了训练模型所需的所有包。在下一步中，必须提取句子及其相关情绪，并分别插入到训练、测试和验证数据帧中。

让我们提取数据集，并将它们放入培训、测试和验证数据框架中。

**注意**:您需要给出您目录中相关文件的正确路径。

现在我们需要清理我们插入到数据框中的句子。我们去掉所有的介词、冠词、标点符号和停用词，只留下每个句子中最重要的词。

这里被删除的单词表现为噪声，因此它们必须被删除以获得期望的结果，这是高水平的测试准确性。

清理完数据后，我们需要对单词进行分词。单词标记化是自然语言处理中的一个重要过程。它标记每个句子，分离每个独特的单词，并为每个独特的术语建立一个索引字典。

这个数据集由六个独特的情感类别组成。喜、怒、悲、惧、惊、爱，可以认定为上述情感范畴。我们将类的数量定义为 6，我们使用 300 个嵌入维度，序列的最大数量被指定为 500。

填充训练和测试序列时，关键是将`maxlen`参数设置为相同的值；否则，将会出现错误。

在接下来的几个步骤中，每个情感值都被分配给一个[分类](https://www.tensorflow.org/api_docs/python/tf/keras/utils/to_categorical)值(0–5 ),以对字典进行编码并使用`to_categorical`函数。当我们得到结果时，我们将分类值映射回字符串。

为了以更有效和更彻底的方式训练模型，从而产生更高的训练精度，我们使用了词向量。为此，我们使用了在维基百科上训练的一百万个词向量。为此，您可以使用许多预先训练好的单词向量。在[快速文本](https://fasttext.cc/docs/en/english-vectors.html)中查看它们。

现在是时候构建用于训练模型的架构了。我们首先创建一个`[Embedding](https://keras.io/api/layers/core_layers/embedding/)` [层](https://keras.io/api/layers/core_layers/embedding/)，其权重取自单词向量文件。

我们还添加了一个`[Bidirectional](https://keras.io/api/layers/recurrent_layers/bidirectional/)` [层](https://keras.io/api/layers/recurrent_layers/bidirectional/)，它由以下特征组成:

*   `gru_output_size` =128
*   `dropout` = 0.2
*   `recurrent_dropout` = 0.2

我们添加了一个`[Dense](https://keras.io/api/layers/core_layers/dense/)` [层](https://keras.io/api/layers/core_layers/dense/)，它有`‘softmax’`激活。

优化器是亚当的优化器，损失由`categorical_crossentropy`决定。

功能`model.summary()`可用于查看模型的特征、图层类型、输出形状和参数数量。

最后，我们可以[使用训练集训练](https://www.tensorflow.org/api_docs/python/tf/keras/Model)模型，并测试准确性，因为模型的度量被设置为`accuracy`。

我们已经将`batch_size`添加为 128，历元数设置为 30。

[批量](https://machinelearningmastery.com/how-to-control-the-speed-and-stability-of-training-neural-networks-with-gradient-descent-batch-size/)和[时期](https://medium.com/@upendravijay2/what-is-epoch-and-how-to-choose-the-correct-number-of-epoch-d170656adaaf)计数都可以修改。为了避免过度拟合，历元的数量不应过多。在许多情况下，较大的批处理大小会产生更好的结果，但是它们也会占用大量内存，因此在许多系统上是不可能执行的。

```
#Train model for 30 epcohs
batch_size=128
epochs=30
history=model.fit(X_train_pad,y_train,batch_size=batch_size,epochs=epochs,callbacks=[checkpoint],validation_data=(X_test_pad,y_test))
```

培训完成后，我们可以保存模型以在我们的`app.py`中使用。此外，我们可以使用一些演示功能来显示模型的实际结果。

```
model.save('trainedModel')
```

需要定义目录将模型保存为`trainedModel`。让我们看看如何获得这个模型的演示输出。

请记住，我们将情感转换为分类值或数值，因此我们需要将分类值映射回原始的英语情感，以获得正确的感觉。

以上陈述的结果是“悲伤”，这很可能是正确的。
通过组合前面的代码片段可以生成整个程序。除非你的电脑已经有了 GPU，在这种情况下，你是一个幸运的人，否则我会推荐使用 [Google Colab](https://research.google.com/colaboratory/faq.html) 来运行这个应用程序，因为它包括一个固有的 GPU，这对训练机器学习模型很有用。

这就是我为英语内容创建模型的方式。我在上面给出了我的 GitHub 链接，所以你可以查看整个系统。我后面会讨论歌词分析过程。这只是从文本内容中获取情感类别的一种基本方式。您可以尝试不同的方法来实现这个目标。

编码快乐！
# 面向 2021 年围棋的数据科学现状

> 原文：<https://betterprogramming.pub/the-state-of-data-science-for-go-in-2021-7171d191e73>

## 包括数据框架、情感分析和十大框架的工作示例

![](img/e4bbbf1b2926ab1b09e920f2a9f6c56e.png)

来自 [Unsplash](https://images.unsplash.com/photo-1523961131990-5ea7c61b2107?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=2048&q=80)

数据科学是一个快速变化的领域，它处于现代技术、数学和统计学的前沿。然而，你不需要太多的数学背景来利用框架来分类图像、预测产品的未来需求或进行情感分析。虽然 Python 已经将自己定位为数据科学的顶级编程语言之一，但仍有努力将该领域扩展到更新的语言，如 GoLang。

在本文中，我们将依次讨论以下主题:

```
1\. [Work with dataframes using Gota (including our own custom methods)](#656d)
2\. [Performing sentiment analysis with GoML](#e2d8)
3\. [Applying K-Nearest-Neighbors with GoLearn](#a326)
4\. [Listing the current top frameworks for data science in Go](#ed8b)
5\. [Examining pros and cons of data science in Go 2021](#7f6b)
```

我们开始吧！

# 在 Gota 中使用数据帧

数据科学始于将数据格式化为易于理解的格式。这通常通过 dataframes 来完成，data frames 是一种给出数据结构的表格格式。Gota 框架专门研究数据帧，使 Gophers 能够方便地组织、操作和获得对数据集的基本了解。以下是从头开始创建数据帧的示例:

这个虚拟数据充当分类垃圾邮件的模拟示例，其中我们有描述、电子邮件的附件数量以及电子邮件中包含不规则符号的百分比。注意我们是如何得到强类型化数据的好处的。

如果我们运行代码并打印出数据帧，我们会得到以下结果:

```
$ go run main.go 
[3x3] DataFrametext             num_attachments symbols 
 0: This is spam 3               0.450000
 1: This is ham  1               0.120000
 2: This is sam  0               0.000000
    <string>     <int>           <float>
```

不算太寒酸。我们得到数据帧的维度、列以及每列的类型。对于数据结构，如自定义结构或哈希映射，有多种数据加载方法。**加载 CSV 文件**也很简单，您可以选择想要的列。这里有一个例子:

Gota 框架还有很多内容，您可以在这里了解:

[](https://github.com/go-gota/gota) [## GitHub-Go-Gota/Gota:Gota:Go 中的数据帧和数据争论(Golang)

### 这是一个数据帧、系列和数据的实现…

github.com](https://github.com/go-gota/gota) 

## 为数据操作添加自定义函数

有时候框架让我们想要更多。虽然 Gota 有坚实的基础，但它没有任何方法来准备我们的数据来训练和测试模型。Go 鼓励一种“能行”的态度，我们自己也能足够容易地实现这些方法。

在下面的函数中，我们将 Gota `dataframe`和`float64`作为我们的输入，将我们的数据分成训练集和测试集。`proportion`参数表示我们希望保存多少数据用于训练。

首先，我们需要重组我们的数据。我们获得了`dataframe`的记录，因为它们很容易被操纵。然后，我们跳过第 0 行，因为它代表数据框的列名，然后将每一行与另一个随机行交换，以便打乱数据。代码如下:

接下来，我们创建 2D 数组 train 和 test，它以`records[0]`作为初始列名。然后我们得到相应比例的行数，并将该数设置为`cutoff`变量。然后我们遍历这些行，根据我们是否通过了`cutoff`，追加到`train`和`test`。最后，我们将`train`和`test` `records`转换回`dataframes`使用。这不是什么新奇的东西，可以被强化，但这是一个开始，实现了我们的目标。

# 用流行的框架构建模型

有趣的事情来了。让我们看两个使用 Go 框架为我们解决数据科学问题的例子。在第一个例子中，我们将使用`goml`框架执行情感分析。在第二部分中，我们将应用 K-最近邻模型来使用 GoLearn 对众所周知的虹膜数据集进行分类。

## 用 GoML 进行情感分析

基于文本的数据打开了 AI/ML 策略的全新大门。情感分析通常包括获取文本并将其分类为积极、消极或中性的类别。一个简单但有效的情感分析模型是朴素贝叶斯。为了简洁起见，我们不会涉及该模型的细节，但谷歌只是几个点击。:)

在我们的例子中，我们将检查 IMDB 电影评论，这些评论可以在 Kaggle 上找到:

[](https://www.kaggle.com/columbine/imdb-dataset-sentiment-analysis-in-csv-format) [## CSV 格式的 IMDB 数据集(情感分析)

### IMDB 电影评论数据集转换为 CSV 文件

www.kaggle.com](https://www.kaggle.com/columbine/imdb-dataset-sentiment-analysis-in-csv-format) 

**注**:朴素贝叶斯模型需要一组平衡的数据，以避免模型出现偏差。如果你用新数据做实验，要考虑到这一点。

好了，第一步是将数据加载到 Gota csv 格式的第 4-13 行。接下来，我们构建朴素贝叶斯模型。GoML 利用通道和 Go 例程来执行在线学习，这意味着与批量训练相比，您可以即时更新您的模型。如果你问我，我会觉得很时髦！

我们通过创建一个接受 GoML `TextDataPoint` `struct`的通道来创建要使用的`stream`。我们还将有一个`errors`通道，以防出现任何问题。然后我们调用`NewNaiveBayes()`，它接受`stream`，数据集(2)中的类别数，并告诉模型我们想要提取什么。例如，您可以应用`.OnlyWordsAndNumbers`。以下是展示这一过程的代码:

我们不会利用在线学习，相反，我们将有自己的函数`TrainModel()`，它基本上将整个数据帧一次一行地移植到模型中。在我们关闭流并检查错误通道是否有问题之后，我们调用我们自己的定制函数`GetModelAccuracy()`来查看模型的执行情况。

如果我们运行这个程序，我们会得到 85%的准确率。对于如此简单的第一种方法来说，这已经不错了！

```
$ go run main.go 
Training:
        Model: Multinomial Naïve Bayes
        Classes: 2
Training Completed.
h(θ) = argmax_c{log(P(y = c)) + Σlog(P(x|y = c))}
        Classes: 2
        Documents evaluated in model: 40000
        Words evaluated in model: 155259Naive Bayes Accuracy: 0.8582
```

如果你好奇，这里有两个自定义函数:

您可以在此了解更多关于 GoML 的信息:

[](https://github.com/cdipaolo/goml) [## GitHub - cdipaolo/goml:围棋中的在线机器学习(等等)

### goml 是一个完全用 Golang 编写的机器学习库，它让普通开发者可以学习机器…

github.com](https://github.com/cdipaolo/goml) 

## 用 GoLearn 对虹膜数据集应用 K 近邻

这将是一个展示 GoLearn 的简单示例。请随意尝试他们的 KNN 模型，或者深入了解他们的 GitHub repo。我会推荐它，因为 GoLearn 是目前数据科学的顶级框架(当然是观点)。

运行该程序将产生以下结果:

```
Iris-virginica	28	2	  56	0.9333	0.9333  0.9333
Iris-setosa	    29	0	  59	1.0000  1.0000	1.0000
Iris-versicolor	27	2	  57	0.9310	0.9310  0.9310
Overall accuracy: 0.9545
```

# 最后的想法

为围棋创造一个数据科学生态系统还有很多工作要做。虽然有越来越多的解决方案和框架，但仍有许多工作要做。如果你自己是一个地鼠并且有技能，我鼓励你看看你是否能帮助这些项目成长！

## 顶级框架

1.  通用机器学习— [GoLearn](https://github.com/sjwhitworth/golearn) (53 个贡献者，7.9K 颗星)
2.  在线学习— [GoML](https://github.com/cdipaolo/goml) (7 个贡献者，1.2K 颗星)
3.  Dataframes — [Gota](https://github.com/go-gota/gota) (22 个贡献者，1.7K 颗星)
4.  神经网络— [GoBrain](https://github.com/goml/gobrain) (6 个贡献者，482 颗星)
5.  数据管道— [厚皮动物](https://github.com/pachyderm/pachyderm) (140 个贡献者，52k 颗星)
6.  进化算法— [eaopt](https://github.com/MaxHalford/eaopt) (15 个贡献者，740 颗星)
7.  表演 ML — [柳珊瑚](https://github.com/gorgonia/gorgonia) (46 个贡献者，4.1K 颗星)
8.  推荐系统— [金雀花](https://github.com/zhenghaoz/gorse) (15 位投稿人，4K 明星)
9.  计算机视觉— [GoCV](https://github.com/hybridgroup/gocv) (104 个贡献者，42k 颗星)
10.  通用工具— [GoNum](https://github.com/gonum/gonum) (101 个贡献者，5.1K 颗星)

## Go AI/ML Pros

*   强类型语言
*   快速、高性能和编译的应用程序
*   成长中的社区
*   广泛的框架生态系统

## Go AI/ML Cons

*   框架生态系统高度分散
*   未建立的社区支持
*   经常要自己动手做事
*   缺乏高层次的方法来使用
*   没有推动创新的强大力量
*   最低绘图能力

感谢阅读！
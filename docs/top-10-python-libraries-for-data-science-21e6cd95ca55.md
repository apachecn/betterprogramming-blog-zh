# 数据科学的 10 大 Python 库

> 原文：<https://betterprogramming.pub/top-10-python-libraries-for-data-science-21e6cd95ca55>

## 数据科学入门？从这里开始

![](img/868345ff58aba38174cabe5d7e230928.png)

克里斯·利维拉尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

[Python](https://www.python.org/) 已经成为当今使用最广泛的编程语言——尤其是在数据科学领域，因为它是一种高性能语言，易于学习和调试，并且具有广泛的库支持。这些图书馆中的每一个都有特定的焦点。一些人管理图像和文本数据，而其他人则专注于数据挖掘、神经网络和数据可视化。Python 可用于统计分析和构建预测模型。当谈到解决数据科学任务和挑战时，数据爱好者、分析师、工程师和科学家正在利用 Python 的力量。

在本文中，我将讨论对数据科学和机器学习最有用的十大 Python 库。

如果您使用的是 [Anaconda](https://www.anaconda.com/) 发行版，那么已经安装了一些库。你所要做的就是导入这个库，或者如果你的机器上没有的话，用 pip 安装它。

1.  [Pandas](https://pandas.pydata.org/) : Pandas 的名字来源于“panel data”，这是一个计量经济学术语，指多维结构化数据集和“Python 数据分析”。众所周知，清理和转换数据在数据分析中非常重要。Pandas 提供了丰富的数据结构和函数，旨在使结构化数据的处理变得快速、简单和富于表现力。Pandas 允许您从不同的文件格式导入数据，如 CSV、JSON、SQL 和 Microsoft Excel。它基于两种主要的数据结构:“序列”(一维)和“数据帧”(二维)。数据框架非常类似于 Excel 和 SPSS 等统计软件中的表格。Pandas 允许各种数据操作，例如处理和输入缺失数据、索引、从数据帧中添加和删除列、合并、整形、选择以及数据争论、管理特性和可视化。
2.  [NumPy](https://numpy.org/):NumPy(Numerical Python)，Python 中最基础、最通用的数组处理包之一，是一个科学计算和执行元素级、高级数组运算的工具。它是许多库的基础，例如用于机器学习的 SciPy 和 scikit-learn。NumPy 维称为*轴*，轴的数量称为*秩*，其数组类称为`ndarray` *。* NumPy 有助于数组的数学运算及其矢量化。NumPy 数组上数学运算的向量化提高了性能和执行时间。基本的数组操作是加法、乘法、整形、索引和切片数组。它在数据分析方面的一个主要目的是，它是在算法之间传递数据的主要容器。
3.  [SciPy](https://scipy.org/) : SciPy(科学 Python)是另一个用于高级科学和技术计算的核心库。它建立在 NumPy 之上，因此扩展了它的功能，并为科学和数字计算提供了许多用户友好和高效的例程。SciPy 包含处理数据集成、数据优化、数据插值、数据修改、线性代数、概率论、随机数生成、积分学、傅立叶变换等模块。
4.  [Matplotlib](https://matplotlib.org/) : Matplotlib 是 Python 中使用最广泛的可视化库。这是一个用于创建二维图表的低级库。Matplotlib 容纳图例、标签和网格。用 Matplotlib 可以讲故事。可以用 Matplotlib 创建的一些图有饼图、条形图、散点图、直方图、线图等等。许多流行的绘图库，如 seaborn，都是为使用 matplotlib 而设计的。
5.  [Seaborn](https://seaborn.pydata.org/):**Seaborn 是一个基于 Matplotlib 构建的高级数据可视化库，集成了 NumPy 和 Pandas 数据结构。它是绘制吸引人的统计图表、热图和其他类型的汇总数据的可视化工具。有丰富的可视化图库，包括一些复杂类型，如时间序列、联合图和小提琴图。Seaborn 的数据图形可以包括条形图和直方图，饼图，散点图等。Seaborn 可用于确定变量之间的关系(相关性)，绘制因变量的线性回归模型，查看和观察分类变量，等等。**
6.  **[sci kit-Learn](https://scikit-learn.org/stable/):sci kit-Learn 是一个免费的机器学习 Python 库，用于数据挖掘任务和预测建模，如回归、分类和聚类。它具有监督和非监督的机器学习算法，如决策树、支持向量机、朴素贝叶斯、随机森林、交叉验证、k 均值聚类等。Scikit-Learn 由 NumPy，SciPy 支持。Matplotlib，熊猫，等等。**
7.  **[TensorFlow](https://www.tensorflow.org/) : TensorFlow 是 Google 为机器学习和深度学习开发的流行框架。它用于定义和运行张量计算。TensorFlow 高效地进行分类以及数据创建和预测。它是执行自然语言处理、对象识别、语音识别、运动检测等任务的最佳工具。它与运行大型数据集的人工神经网络一起工作，并允许轻松部署机器学习和深度学习应用程序。它提供了多个抽象层，因此您可以选择您的模型需要什么。有了 TensorFlow，你可以很容易地建立和训练机器学习模型，比如 Keras。它还允许您在任何地方部署机器学习模型，无论是在云上、您的浏览器上还是您的本地机器上。谷歌、Twitter、可口可乐、Airbnb 和网飞等大公司都使用 TensorFlow。**
8.  **[Keras](https://keras.io/) : Keras 是一个免费开源的 Python 神经网络库。这是一个用于训练和构建神经网络和建模的库。与 TensorFlow 同时提供高级和低级 API 不同，Keras 只提供高级 API。它有优化器、层、激活函数等等。Keras 支持在深度神经网络中更容易处理不同类型的图像和文本数据的工具。它可以与 TensorFlow、Theano 等库一起使用。您可以使用 Keras 完成许多任务，比如用多层深度的重复代码块编写函数。**
9.  **[Statsmodels](https://www.statsmodels.org/stable/index.html) : Statsmodels 是一个 Python 库，可以实现机器学习，为描述性统计和统计数据分析提供计算，比如统计模型估计、假设检验、贝叶斯模型、线性回归、相关性等等。**
10.  **[beautiful soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/):beautiful soup 是一个流行的 Python 库，使用网站的 API 在不同的网站上进行数据抓取和网络爬行。它从 HTML 和 XML 文件中提取数据。Beautiful Soup 支持 Python 标准库中包含的 HTML 解析器，但它也支持许多第三方 Python 解析器。**

**你知道数据科学和机器学习中用到的其他有用的有帮助的 Python 库吗？把它们放在下面的评论区，分享有用的信息。**

**感谢您的阅读。**
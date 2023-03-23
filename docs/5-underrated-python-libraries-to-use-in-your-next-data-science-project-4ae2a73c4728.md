# 在您的下一个数据科学项目中使用的 5 个被低估的 Python 库

> 原文：<https://betterprogramming.pub/5-underrated-python-libraries-to-use-in-your-next-data-science-project-4ae2a73c4728>

## 外加额外的新图书馆

![](img/1a324ad657fc1d8d77a19b6e0431f328.png)

[女同胞](https://unsplash.com/@cowomen?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

在软件开发和数据科学的世界里，一切都变化得如此之快。每天都有新的包部署并上传到 GitHub 上。这种速度可能会让我们中的一些人感到不知所措，尤其是如果我们有一个每天都在使用的最喜欢的库。

由于新库的数量，一些好的库没有被发现。一些有用的库，可以帮助加快和减轻你的工作。

在本文中，我将向您展示五个 Python 库，它们可以让您的项目更上一层楼，并加快开发速度。我还会给你一个额外的新库，可以帮助你自动化你的项目的某些方面。

本文中的库可以用于数据科学的不同领域，从 web 抓取到扩展您的项目，甚至是自然语言生成(NLG)。

# 摩丁

## 装置

要安装 Modin，只需使用`pip`:

```
pip install modin
pip install ray
```

Modin 使用 Ray 或 Dask 计算引擎来访问您的硬件。您可以将自己喜欢的添加到 Modin 的安装命令中，如下所示:

*   `pip install modin[ray]`
*   `pip install modin[dask]`
*   `pip install modin[all]`

## 概观

我们需要分析、处理和清理的数据量每天都在增加。高效完成所有这些任务的能力是决定项目成功的因素之一。

数据集越大，用熊猫来分析就越麻烦。Pandas 是迄今为止处理所有不同类型数据的最棒的 Python 库之一。它是每个数据科学项目的核心。

然而，在处理大型数据集时，Pandas 可能会消耗大量内存。熊猫不能有效地利用你的硬件，这就是[摩丁](https://modin.readthedocs.io/en/latest/)来帮忙的地方。

Modin 是一个 Python 库，它利用了你的硬件构造。它是 Pandas 的替代产品，提供了大部分 pandas APIs 的完全并行化。即使在简单的笔记本电脑上，摩丁也能提供显著的加速。有了 Modin，如果你的笔记本电脑有四个物理内核，你处理数据的速度可以快四倍。

要开始使用 Modin，你需要做的就是修改你的代码中的`import`行，然后像处理不支持 Modin 的熊猫一样继续工作。

# 变形金刚(电影名)

## 装置

假设您安装了 TensorFlow 2.0 和/或 PyTorch 1.1.0，您可以使用`pip`轻松安装变压器:

```
pip install transformers
```

## 概观

[变形金刚](https://huggingface.co/transformers/index.html)是 Pytorch 和 TensorFlow 2.0 最先进的自然语言处理。它为自然语言理解(NLU)和自然语言生成(NLG)提供通用架构，拥有 100 多种语言中超过 32 种预训练模型。

变形金刚在 NLU 和 NLG 任务上提供了高性能，这些任务对于初学者、教育者和实践者来说是足够简单的。此外，它还包含更复杂的深度学习研究人员和动手开发者模型。

包含在变形金刚中的模型属于五个类别之一:[自回归模型](https://huggingface.co/transformers/model_summary.html#autoregressive-models)、[自动编码模型](https://huggingface.co/transformers/model_summary.html#autoencoding-models)、[序列到序列模型](https://huggingface.co/transformers/model_summary.html#seq-to-seq-models)、[多模态模型](https://huggingface.co/transformers/model_summary.html#multimodal-models)和[基于检索的模型](https://huggingface.co/transformers/model_summary.html#retrieval-based-models)。

变压器提供了许多基本的 NLP 任务，已经内置并随时可以使用。变压器提供的一些功能有:

1.  情感分析。
2.  文本生成。
3.  命名实体识别(NER)。
4.  问题解答。
5.  总结。
6.  翻译。

# 细流

## 装置

确保你有 Python 3.6 或更高版本；然后您可以使用`pip`安装 Streamlit:

```
pip install streamlit
```

## 概观

[Streamlit](https://streamlit.io/) 是一个开源的 Python 库，可以轻松地为机器学习和数据科学项目构建定制的 web 应用。Streamlit 会跟踪每次保存的更改，并在您编码时更新您的应用。这是一个简单而强大的应用程序模型，允许您以令人难以置信的速度构建令人惊叹的用户界面。

数据科学和机器学习项目的一个重要方面是结果呈现和可视化。使用 Streamlit 这样的工具，你可以用一种吸引人的互动方式展示你的发现，真正传达你的工作价值。

开始使用 Streamlit 简单明了；您可以用很少的努力和几行代码构建一个强大、漂亮的 web 应用程序。

# LightGBM

## 装置

您可以使用`pip`从命令行安装 LightGBM:

```
pip install lightgbm
```

## 概观

[LightGBM](https://lightgbm.readthedocs.io/en/latest/index.html) 是一个使用基于树的学习算法的梯度推进 Python 框架。它被设计成高效且易于使用。除了处理大规模数据集的能力之外，LightGBM 还具有许多特性，包括快速训练速度、低内存使用、高精度、支持并行和 GPU 学习。

LightGBM 可以应用于很多问题，比如二分类、回归、多分类、交叉熵和λrank。它还支持许多指标，如 L1/L2 和日志损失。

LightGBM 的一个有前途的特性是它处理缺失值的能力，这是几乎所有数据科学项目中经常出现的问题。

# Theano

## 装置

要安装 Theano，只需使用`pip`命令:

```
pip install theano
```

## 概观

[Theano](http://deeplearning.net/software/theano/) 是一个为神经网络和深度学习应用而构建的 Python 库。它能够高效地评估数学运算，包括多维数组。

虽然 Python 比其他编程语言(如 C)慢得多，但使用 Theano 利用了最新的 GPU，使其在某些情况下在大多数 CPU 上的性能比 C 好得多。

Theano 是 Numpy 的力量和 Sympy 的力量的结合。它有许多优点和功能，例如它可以找到不稳定的表达式和稳定的方法来计算它们，它通过在 GPU 上执行部分代码来利用 GPU，使其比常规 Python 代码快得多，并且它可以自动创建用于计算梯度的符号图。

# 额外奖励:自动刮板机

## 装置

要安装 Autoscraper，您需要直接从源代码开始安装。

```
pip install git+https://github.com/alirezamika/autoscraper.git
```

## 概观

网络搜集是数据科学项目的第一步。当我们手动从网上收集数据时，这可能是一项单调乏味的任务。这就是 Autoscraper 的用武之地。

Autoscraper 是一个非常新的 Python 库，用于自动抓取网页，使其简单快捷。它获取一个 URL，并生成一个网页的 HTML 内容和一个我们想要从该页面获取的样本数据列表。数据可以是文本或页面中包含的任何 HTML 标记值。

它可以学习刮擦规则和模式，并返回与最初刮擦的元素相似的元素。然后，您可以使用它来抓取具有相似结构的 URL，而无需从头开始重写代码。

# 外卖食品

在数据科学、机器学习或一般的软件开发中，每天都有新的库和包被部署。这使得与所有新库保持同步变得非常困难。尤其是如果我们已经有了日常任务的首选库。

在本文中，我介绍了六个被低估的相对较新的库，它们可以帮助您更快、更有效地完成下一个数据科学或机器学习项目。这些图书馆包括:

1.  **摩丁**扩大和加快你的熊猫数据帧。
2.  **内置基本 NLP 模型的变压器**。
3.  **简化**将你的发现以一种漂亮、吸引人的方式作为网络应用程序呈现出来。
4.  **LightGBM** 用于高效的基于树的机器学习算法。
5.  **Theano** 用于快速实用的神经网络和深度学习应用。
6.  **Autoscraper** 自动化枯燥乏味的网页抓取任务。

所以，当你开始下一个项目时，考虑尝试其中一个，谁知道呢，也许其中一个会成为你的新宠。

# 参考

[1] [摩丁文档。](https://modin.readthedocs.io/en/latest/)

[2] [变压器文件。](https://huggingface.co/transformers/)

[3] [简化文档。](https://docs.streamlit.io/en/stable/)

[4] [LightGBM 文档](https://lightgbm.readthedocs.io/en/latest/Python-Intro.html)。

[5] [Theano 文档](http://deeplearning.net/software/theano/)。

[6] [自动刮刀储存库](https://github.com/alirezamika/autoscraper)。
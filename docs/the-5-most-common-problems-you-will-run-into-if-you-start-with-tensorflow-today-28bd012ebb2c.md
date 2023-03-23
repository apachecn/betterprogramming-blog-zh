# 如果您现在开始使用 TensorFlow，将会遇到的 5 个最常见的问题

> 原文：<https://betterprogramming.pub/the-5-most-common-problems-you-will-run-into-if-you-start-with-tensorflow-today-28bd012ebb2c>

## **以及如何解决这些问题，这样你就可以开始了！**

![](img/3600f5a3722a1f24e0c150146f22bcb7.png)

照片由[马修·汉密尔顿](https://unsplash.com/@thatsmrbio?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄。

这件事在我们的待办清单上已经有一段时间了。由于假期后事情往往会变得有点安静，我和我的女朋友最终决定开始学习机器。

我们的背景:我们都是软件工程师。我们大约六年前在同一家公司开始工作，但我们两年前才认识。我目前正在开发我的 tweet 调度器 [blauspecht.io](https://www.blauspecht.io) ，我最终希望将机器学习功能集成到其中。

我们对 [TensorFlow](https://www.tensorflow.org/) 的最初体验相当不错。我们设法在几个小时内完成了第一个教程，对结果相当满意。但是环境的初始设置比我们预期的要长。为了节省您的时间，我希望在开始使用 TensorFlow 之前知道以下五件事。

# 1.正确的 Python 版本

```
Could not find a version that satisfies the requirement tensorflow (from versions: )
No matching distribution found for tensorflow
```

在撰写本文时，Python 的最新版本是 3.9.1。但是 TensorFlow 只支持 Python v3.5 到 3.8，我用的是 3.8 分支的最新版本，目前是 [3.8.7](https://www.python.org/downloads/) 。

# 2.正确的数字版本

```
RuntimeError: The current Numpy installation ('...\\site-packages\\numpy\\__init__.py') fails to pass a sanity check due to a bug in the windows runtime. See this issue for more information: https://tinyurl.com/ y3dm3h86
```

如果您打算在英特尔机器上运行 TensorFlow 和 Windows，您必须将 NumPy 降级到 [1.19.3](https://github.com/numpy/numpy/wiki/FMod-Bug-on-Windows) 。你可以通过运行`pip install numpy==1.19.3`来实现。

# 3.TensorFlow.js 的另一个正确的 Python 版本

```
gyp ERR! find Python **********************************************************
gyp ERR! find Python You need to install the latest version of Python 2.7.
gyp ERR! find Python Node-gyp should be able to find and use Python. If not,
gyp ERR! find Python you can try one of the following options:
gyp ERR! find Python - Use the switch --python="C:\Path\To\python.exe"
gyp ERR! find Python   (accepted by both node-gyp and npm)
gyp ERR! find Python - Set the environment variable PYTHON
gyp ERR! find Python - Set the npm configuration variable python:
gyp ERR! find Python   npm config set python "C:\Path\To\python.exe"
gyp ERR! find Python For more information consult the documentation at:
gyp ERR! find Python https://github.com/nodejs/node-gyp#installation
gyp ERR! find Python **********************************************************
gyp ERR! find Python
gyp ERR! configure error
gyp ERR! stack Error: Could not find any Python installation to use
```

如果你想试用 [TensorFlow.js](https://www.tensorflow.org/js) 并想在 Node.js 内部运行，你需要 npm 包 [tfjs-node](https://www.npmjs.com/package/@tensorflow/tfjs-node) 。这个包需要 [node-gyp](https://www.npmjs.com/package/node-gyp) ，进而需要 Python 2 **。**为了配置 npm 使用 Python 2，我使用了命令`npm config set python /c/path/to/python2.7`。

# 4.Jupyter 笔记本的依赖性

```
ipykernel is not installed
nbconvert is not installed
```

[TensorFlow 网站上的教程](https://www.tensorflow.org/)大量使用了 [Jupyter 笔记本](https://jupyter.org/)。为了在本地运行笔记本，我用`pip install ipykernel nbconvert jupyter`安装了额外的依赖项。

# 5.Jupyter 笔记本到 Python 代码的转换

用 VS 代码导出 Jupyter 笔记本好像也坏了。也就是说，您也可以使用`jupyter nbconvert — to python notebook.ipynb`从命令行导出笔记本。

# 下一步是什么？

我们从 IMDb 电影评论分类教程开始。我建议你也这样做。它包含一步一步的说明，并在结尾包括一个很好的练习:

[](https://www.tensorflow.org/tutorials/keras/text_classification) [## 基本文本分类| TensorFlow 核心

### 本教程演示了从存储在磁盘上的纯文本文件开始的文本分类。你将训练一个二进制…

www.tensorflow.org](https://www.tensorflow.org/tutorials/keras/text_classification) 

在我对 TensorFlow 感到更加舒适之后，我遵循了关于过度拟合和欠拟合的教程，该教程描述了常见的训练问题和训练最佳实践。这也非常非常有趣:

[](https://www.tensorflow.org/tutorials/keras/overfit_and_underfit) [## 过拟合和欠拟合|张量流核心

### 你可能熟悉奥卡姆剃刀原理:给定对某事的两种解释，最有可能…

www.tensorflow.org](https://www.tensorflow.org/tutorials/keras/overfit_and_underfit) 

如果你实现了基于`SparseCategoricalCrossentropy`和的提前停止，将你的模型保存到磁盘以备后用，还有一件事你可能会偶然发现。要加载用定制指标训练的模型，您还需要将指标传递给`load_model`函数:

```
imported_model = tf.keras.models.load_model('./model', custom_objects={“SparseCategoricalCrossentropy”: losses.SparseCategoricalCrossentropy(from_logits=True)})
```

我将在未来几天和几周内分享更多关于我使用 TensorFlow 的经验和我的第一个机器学习项目。敬请期待！

感谢阅读。
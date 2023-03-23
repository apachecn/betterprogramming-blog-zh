# 酸洗机器学习模型

> 原文：<https://betterprogramming.pub/pickling-machine-learning-models-aeb474bc2d78>

## 训练你的 ML 模型一次，不用训练就可以使用

![](img/b378607d4cfa7ac45f82d742add09191.png)

[TmGomes](https://www.deviantart.com/tmgomes) 在 [DeviantArt](https://www.deviantart.com/) 上拍摄的照片。

在你创建了一个机器学习模型之后，你训练它。培训过程通常需要一些时间。模型训练好之后，得到的就是模型对象。要使用该模型，您要么以某种方式保存该模型，要么可以在每次使用时重新训练该模型，这是低效的)。

如果您有数万亿字节的模型训练数据，每次您都要进行预测，那么每次都训练模型将是低效的。而是训练完模型，就可以保存了。理想情况下，一旦有了新数据，就应该对模型进行重新训练，但不必在每次想要使用它时都进行重新训练。

要保存机器学习模型，请使用 [pickle](https://docs.python.org/3/library/pickle.html) 。

# 什么是腌制？

Pickle 是 Python 中的一个模块，用于序列化和反序列化 Python 对象。这将转换列表、字典等 Python 对象。转换成字节流(零和一)。您可以通过称为 unpickling 的过程将字节流转换回 Python 对象。酸洗也称为序列化、扁平化或编组。

# 如何腌制

pickle 模块有两个方法。

## 泡菜。**转储()**

`pickle.dump()`方法将 Python 对象转储到 pickle 文件中。这将在您当前的工作目录(`pickle.dump(what_are_we_dumping, where_are_we_dumping_it)`)中创建一个`.pickle`文件:

在上面的代码片段中，我们从一个`example_dict`字典创建了一个`example_dict.pickle`文件。语句 1 和 2 执行同样的任务，将字典转换成 pickle 文件。使用`with`语句确保在程序执行离开`with`语句的上下文后，打开的文件描述符自动关闭。`open`语句中的`'wb'`表示我们正在*将字节*写入文件。

## 泡菜。 **load()**

`pickle.load()`方法允许您通过将`.pickle`文件(`pickle.load(what_do_we_want_to_load)`)加载到内存中来使用它:

在上面的代码片段中，我们从一个`example_dict.pickle`文件创建了一个`example_dict`字典。语句 1 和 2 执行读取 pickle 文件的相同任务，pickle 文件是一个字典。open 语句中的`'rb'`表示我们正在从文件中读取*字节数据*。

# 酸洗机器学习分类器

在导入所有必要的库之后，我们读取数据，然后将它分成测试集和训练集。然后，我们根据`X_train`和`y_train`数据训练(拟合)分类器。在我们训练了分类器之后，我们接着将这个分类器保存在一个 pickle 文件中。我们从线性回归分类器(`clf`)中创建一个 pickle 文件(`linearregression.pickle`)。

当我们想要使用一个训练好的分类器时，我们将 pickle 文件(`linearregression.pickle`)转换回一个分类器(`pickle_clf`)。任何时候我们想在程序中使用分类器，我们只需使用`pickle_clf`。

原始分类器(`clf`)和酸洗分类器(`pickle_clf`)相同。这可以通过在相同的测试集上比较两者所达到的准确度来证明。对比后返回`True`，显示准确率相同，证明分类器相同。

# 什么时候腌制

*   保存程序的状态。
*   通过 TCP 连接发送 Python 数据。
*   保存 Python 对象。

# 什么时候不腌制

*   当使用多个 Python 版本时:在不同的 Python 版本中分离对象可能会很麻烦。
*   当跨多种语言工作时:**`pickle` 使用的数据格式是 Python 特有的，这意味着非 Python 程序可能无法重构腌制的 Python 对象。**
*   **当使用递归数据结构时:试图处理一个高度递归的数据结构可能会超过最大递归深度。在这种情况下会引发一个`[RuntimeError](https://docs.python.org/2/library/exceptions.html#exceptions.RuntimeError)`。您可以使用`[sys.setrecursionlimit()](https://docs.python.org/2/library/sys.html#sys.setrecursionlimit)`提高此限制。**
*   **使用外部 pickle 文件时:**`pickle`模块[不安全](https://docs.python.org/3/library/pickle.html#admonition%20warning)。只对您信任的数据进行解压缩。有可能构建恶意 pickle 数据，该数据将在拆包期间执行任意代码。永远不要解开可能来自不可信来源的数据。****

# ****cPickle****

****就像`pickle`模块一样，`cPickle`支持 Python 对象的序列化和反序列化。因为是用 C 语言写的，所以比基于 Python 的`pickle`快一千倍的[。由于`pickle`和`cPickle`产生的输出相同，我们可以互换使用这两个模块。](https://docs.python.org/2/library/pickle.html#module-cPickle)****

# ****资源****

1.  ****Python 官方文档:
    [https://docs.python.org/2/library/pickle.html](https://docs.python.org/2/library/pickle.html)
    [https://docs.python.org/3/library/pickle.html](https://docs.python.org/3/library/pickle.html)****
2.  ****丹·巴德的博客:
    [https://dbader . org/blog/python-context-managers-and-with-statement](https://dbader.org/blog/python-context-managers-and-with-statement)****
3.  ****Manu Kalia 关于 pickle 的文章:
    [https://medium . com/better-programming/don-fear-the-pickle-using-pickle-dump-and-pickle-load-5212 f 23 dbce](https://medium.com/better-programming/dont-fear-the-pickle-using-pickle-dump-and-pickle-load-5212f23dbbce)****
4.  ****Lokesh Sharma 关于 pickle 的博客:
    [https://medium . com/@ lokesh Sharma 596/what-is-pickle-in-python-3d9f 261498 B4](https://medium.com/@lokeshsharma596/what-is-pickle-in-python-3d9f261498b4)****
5.  ****Sentdex 机器学习播放列表:
    [https://www.youtube.com/watchv=za5s7RB_VLw&list = plqvvvaa 0 qudfktos 3 keq _ kag 2 p 55 yrn 5v&index = 7&t = 0s](https://www.youtube.com/watch?v=za5s7RB_VLw&list=PLQVvvaa0QuDfKTOs3Keq_kaG2P55YRn5v&index=7&t=0s)****
6.  ****Sentdex 泡菜教程:
    [https://www.youtube.com/watch?v=2Tw39kZIbhs&t = 343s](https://www.youtube.com/watch?v=2Tw39kZIbhs&t=343s)****
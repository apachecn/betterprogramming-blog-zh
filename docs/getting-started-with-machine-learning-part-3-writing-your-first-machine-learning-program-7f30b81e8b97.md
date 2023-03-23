# 机器学习入门，第 3 部分:编写第一个机器学习程序

> 原文：<https://betterprogramming.pub/getting-started-with-machine-learning-part-3-writing-your-first-machine-learning-program-7f30b81e8b97>

## 使用 sci-kit learn 的简单示例

![](img/47fd3f7aa3c62ddcc4d993d0bd9fe877.png)

图片来自 [Freepik](https://www.freepik.com/free-vector/particle-artificial-inteligence-face-background_5513843.htm#page=1&query=machine%20learning&position=0)

欢迎来到机器学习入门系列的第三部分。我希望你已经阅读了我以前写的关于机器学习基础知识以及如何设置代码编辑器或 ide 以开始用机器学习/数据科学编码的故事。第一部分讲述了机器学习的[概念](https://medium.com/@adorekasun/getting-started-with-machine-learning-part-1-5cca93679d74)，第二部分讲述了开始一个 ML 项目的[基本步骤](https://medium.com/p/e9d2c614c6cc/edit)。

所以让我们来做一个快速的动手练习。

这个程序是一个超级简单的程序，从两个给定的特征来分类/预测水果的类型。这个例子用苹果和橘子。我输入重量和质地。在被赋予一些特征后，程序会学习，每当我们赋予它完全独立的特征时，它会预测水果的类型。

由于这是一个基础程序，它只需要一个库，那就是 sci-kit learn。您需要在命令提示符或 Anaconda 虚拟环境中使用`Pip install scikitlearn` 在当前计算机上安装 sci-kit learn。(我在上一篇文章中已经讨论了这一部分)。完整的代码将如下所示:

```
from sklearn import tree# smooth = 1 and bumpy =0 machines use real data
features = [[140, 1], [130, 1], [150, 0], [170, 0]]labels = [0, 0, 1, 1] # apple = 0 and oranges = 1adoreclassifier = tree.DecisionTreeClassifier()adoreclassifier = adoreclassifier.fit(features, labels)print(adoreclassifier.predict([[120, 1]]))
```

让我们一行一行地讨论代码。

```
from sklearn import tree
```

这将从 sci-kit 学习库中导入树函数。这样做只是为了使程序轻量级，否则你可以将整个库作为`import scikitlearn`导入。程序中使用的主要算法， *i* ，是决策树算法。这就是为什么我只导入了树的部分。

```
features = [[140, 1], [130, 1], [150, 0], [170, 0]]
```

现在，让我们定义要插入到树中的事物的特征。

![](img/3b1df2c47d8fc6deb13ddd9e71fa9449.png)

图片由作者提供

让我们取一些这两种水果的重量和质地的样本，并把它们输入到树中。我已经给出了二进制格式的纹理，其中 1 表示平滑，0 表示凹凸不平。在这里，我制作了一个 4*2 的特征矩阵，其中训练数据在列中，权重和纹理包含在升序的行中。您可以根据需要向该树中插入任意多的数据，每增加一条数据都会增加行数。

假设我们增加了水果表面的湿度或干度的另一个特征。假设橘子是湿的(1)，苹果是干的(0)。所以我将添加另一个包含干或湿特性的列。

```
features = [[140, 1, 1], [130, 1, 1], [150, 0, 0], [170, 0, 0]]
```

现在变成了 4*3 的矩阵。

```
labels = [0, 0, 1, 1]
```

现在我给出给定特征的标签。由于这是监督学习，我们必须给出给定特征的标签。标签以二进制显示，其中 0 代表橙色，1 代表苹果(参考上表)。现在我们已经定义了一个特征矩阵和它们各自的标签。让我们制作一个分类器来存储所有这些。

```
adoreclassifier = tree.DecisionTreeClassifier()
```

我做了一个分类器叫做`adoreclassifier`。您可以使用您想要的名称(仅变量)。现在我们必须从 sci-kit 学习树函数中初始化分类器。由于算法是决策树，`tree.DecisionTreeClassifier()`初始化分类器。您的分类器名称就是您用来生成变量的名称——在我的例子中，就是`adoreclassifier` *。*现在我们必须将特征和标签传递给我们制作的分类器。

```
adoreclassifier = adoreclassifier.fit(features, labels)
```

拟合函数用于拟合或匹配决策树。我们必须首先传递特征，然后传递标签，以便树可以清楚地识别这是特征，这是它的标签。我们已经制作了分类器，并为其配备了特征和方法，因此现在我们的模型已经制作完成。我们所要做的就是用它来做预测。

```
print(adoreclassifier.predict([[120, 1]]))
```

这里我打印一个随机水果的预测值，这个水果重 120 克，光滑(1)。在分类器的学习部分，我们在一定程度上给出了所有要学习的东西。现在我们给出分类器中没有的数据，所以分类器从`classifier_name.Predict() function`开始预测那部分。

我觉得对于第一个机器学习程序来说已经足够了。如果有任何问题，请在评论区告诉我，我会尽快联系你的！手指交叉！编码快乐！
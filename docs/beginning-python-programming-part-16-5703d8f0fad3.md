# Python 编程入门—第 16 部分

> 原文：<https://betterprogramming.pub/beginning-python-programming-part-16-5703d8f0fad3>

## 让我们来谈谈测试驱动开发

![](img/a123b2188298822fe2e854e193a7c04e.png)

照片由[米哈尔·皮查多](https://unsplash.com/@michalp24?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在上一篇文章中，我们介绍了多处理，完成异步。

[](https://medium.com/better-programming/beginning-python-programming-part-15-ae96dd8b9c95) [## Python 编程入门—第 15 部分

### 多重处理简介

medium.com](https://medium.com/better-programming/beginning-python-programming-part-15-ae96dd8b9c95) 

在本文中，我们将涵盖任何编程语言最关键的方面。

# 测试

测试代码最流行的两个模块是`unittest`和`PyTest`。我选择在本文中讨论`unittest`,因为它是 Python 附带的。

`unittest`是 Python 提供的一个框架，允许您创建关于代码的断言。如果断言为真，测试通过。

在我们开始测试之前，我们应该了解一些关于测试代码的事情，其中一些是社区中分歧很大的。

有几种不同形式的测试:

*   **单元测试**——覆盖你的代码的各个部分的测试(这就是本文的内容)
*   **可靠性测试(冒烟测试)** —确保您的整个程序能够经受住时间的考验或者不会崩溃的测试。(冒烟测试这个名字来源于一个古老的诀窍，如果你打开一个东西，它没有开始冒烟，它应该会工作。)
*   **质量保证测试** —由 QA 团队执行的测试(如果有)。QA 工程师被训练成你代码的混沌猴子。(致质量保证工程师:我充满爱心地说这句话。)

*   **用户验收测试** —你的代码交给一个测试你的代码的试验小组。试点小组越大，你得到的结果就越多。(例如，早期接入游戏)

这些测试可以按任何顺序进行，但这是典型的使用顺序。

*   **代码覆盖率** —测试覆盖的代码的百分比。
*   **覆盖率**——定义测试数量与代码行数量的比率。

我知道这些是敏感的话题，所以我要理性地讨论它。

拥有 100%的代码覆盖率总是很好的，但至少你应该关注你的应用程序的业务逻辑，这是你的程序被创建来做的代码的主要部分。在大多数情况下，一个好的最低限度应该是 30%的覆盖率，尽管你可以通过一些设计良好的测试轻松达到 80%的覆盖率。一个设计良好的测试应该覆盖代码的大范围工作流，比如检查 web 服务器应用程序的响应以确保它是正确的。您可以将路由、业务逻辑甚至数据库逻辑都包含在一个测试中。

覆盖率通常是一些开发人员的吹嘘点。有的会说“我有 1:1 的比例”(一行代码一个测试)。这可能意味着大量浪费的生产周期。在中大型项目中，更好的目标是 1:10 甚至 1:20。这在浪费生产力和测试准确性之间提供了一个中间地带。

*   测试驱动开发(TDD)——TDD 是一种先对比*代码，然后编写测试*的方法。TDD 首先编写一个测试，然后编写最少的代码来通过测试。

TDD 和覆盖率通常是齐头并进的；如果你曾经听到一个开发人员谈论他们的测试比率，他们可能正在使用 TDD。TDD 不是一件坏事，但是如果你不小心的话，它会很快引起维护上的麻烦。

举个例子吧。

首先，我们需要`import unittest`将模块拉进来进行测试。接下来，我们有一个基本的`add`函数，它将两个数相加。

现在我们创建一个名为`TestAddFunction`的类，它继承自`unittest.TestCase`，将包含我们为上面的 add 函数创建的每个测试。在真实的场景中，您的类将包含与工作流功能相关的所有测试。如果工作流是从一个文件中检索名称，您将测试打开一个文件，读取文件，解析名称，并确保从文件中检索的名称的值和数量是正确的。

我们创建的第一个测试是`test_add_numbers`。它唯一的职责是测试我们的 add 函数是否返回了正确的结果。首先，我们调用函数并将结果存储在一个变量中。最后，我们使用
`self.assertEqual(result, 4, "Whoops")`来检查我们函数的结果确实是 4。由于我们继承了`unittest.TestCase`，我们收到了相当多的断言来帮助我们测试代码。

`assertEqual`需要三个参数。前两个是我们想要比较的值，第三个是我们想要在测试失败时打印到控制台的消息。在这个基本的例子中，测试失败的原因是显而易见的，但是有时您可能有不止一个断言，这有助于确定哪个断言失败了。

为了运行测试，您可以使用以下命令:

```
cd /path/to/project
python -m unittest
```

如果您在项目目录中创建了一个虚拟环境，那么您需要在最后指定一个文件名:

```
python -m unittest tests.py
```

在 PyCharm 中，您需要[为测试](https://www.jetbrains.com/help/pycharm/2019.1/creating-run-debug-configuration-for-tests.html)添加一个运行配置。

## 安装和拆卸

有时你需要在测试前后执行工作。`setUp`和`tearDown`允许您配置您的测试环境，因此您不需要在生产系统中测试。

这只是一个简单的测试，确保文件不是空的。这里我们使用`self.assertTrue(len(contents), "Empty file")`进行测试。`assertTrue`需要一个布尔表达式。空弦、`None`和`0`也等同于`False`结果。

我们主要关注的是`setUp`和`tearDown`。`setUp`在我们的类创建时被调用。把这个当做测试类的`def __init__(self):`。在这里，我们用它来创建一个包含单词“Hello”的新文件。随着测试的继续，它将调用`test_file_read`，后者将调用函数`read_file`并将响应存储到`contents`中。然后我们检查以确保`contents`不仅仅是一个空字符串；如果是，我们打印“空文件”

我们最后的检查是断言文件的内容等于“Hello”这看起来像是重复的测试，但是通过首先检查内容的长度，我们可以确信文件至少是存在的。最后的测试是确保文件的内容和我们预期的一样。

当我们为这个类运行所有的测试时，`tearDown`被调用。这是我们执行任何必要清理的地方。在这个场景中，我们在项目目录中创建了一个名为`testfile`的文件，我们需要删除它。

# 新闻报道

关于测试还有很多内容要介绍，但是我会把它留给你在下面的推荐阅读中。我想谈谈覆盖率，因为这是你写完测试后知道的方法。如果没有覆盖率，你需要遵循这样的格言“编写测试，直到对失败的恐惧变成对编写测试的厌倦。”

您可以通过使用`pip install coverage`或者使用 Pycharm 中的捆绑包来安装覆盖。(设置>构建、执行、部署>覆盖率>使用捆绑的覆盖率. py)

您可以使用以下命令从命令行运行并检查测试覆盖率:

```
cd /path/to/project
python -m coverage run
python -m coverage report
```

这将打印类似如下的输出:

```
Name      Stmts   Miss  Cover
-----------------------------
tests.py     13      0   100%
```

`Name`是包含测试的文件，`Stmts`是在文件中发现的语句数量，`Miss`是覆盖率遗漏的测试数量，`Cover`是被测试覆盖的代码百分比。

要查看选项列表，请使用`python -m coverage help`。

在 PyCharm 中，选择 Run > Run tests with coverage。测试覆盖了绿色阴影线。您还将在右侧栏中看到每个文件的统计数据(默认布局)。

# 测试驱动开发(TDD)

TDD 是一种测试代码的方法。这个想法是你写一个失败的测试，然后写足够的代码让测试通过。描述这一点的最直接方式是遵循以下步骤:

1.  添加测试
2.  运行测试。如果通过，请执行第一步。
3.  做出改变。
4.  运行测试。如果通过，请执行第一步。如果失败，请执行第三步。

在我们之前的例子中，我们编写了一个函数，然后我们编写了将一次性调用该函数的测试。在 TDD 中，我们编写测试并运行它，完全知道测试会失败，因为逻辑不存在。然后我们会编写足够的代码来通过测试。如果它仍然失败，我们回过头来检查我们的逻辑，找出失败的原因并做出修正。如果它过去了，我们继续前进。

因为这只是一个方法论，所以我不会在这里涉及，但是如果你想了解更多，我会在建议阅读中提供一个链接。

# 摘要

我们学习了如何在 Python 中进行一些基本的测试，并使用代码覆盖率来计算我们的代码有多少被测试覆盖，并且对 TDD 进行了简单的介绍。

我过去很难写测试，因为我写的大多数程序都很简单。我不得不改变我的想法。我开始认为我写的每个程序每天都能赚 100 万美元，直到它失败。为了防止它失败，我需要编写测试来确保它可以在没有维护的情况下运行几十年。

# 推荐阅读

## 测试断言的完整列表

 [## 单元测试—单元测试框架— Python 3.7.4 文档

### 编辑描述

docs.python.org](https://docs.python.org/3/library/unittest.html#unittest.TestCase.debug) 

## 新闻报道

 [## Coverage.py — Coverage.py 4.5.3 文档

### Coverage.py 是一个测量 Python 程序代码覆盖率的工具。它监控你的程序，记录下…

coverage.readthedocs.io](https://coverage.readthedocs.io/en/v4.5.x/index.html) 

## 测试驱动开发

[](http://www.agiledata.org/essays/tdd.html) [## 测试驱动开发(TDD)简介

### 编辑描述

www.agiledata.org](http://www.agiledata.org/essays/tdd.html) 

## PyTest

 [## pytest:帮助你编写更好的程序——pytest 文档

### 该框架使编写小型测试变得容易，同时还可以扩展以支持应用程序和软件的复杂功能测试。

docs.pytest.org](https://docs.pytest.org/en/latest/#) 

# 下一步是什么？

在本系列的最后一篇文章中，我将提供一些我在开发工作流中使用的技巧。虽然它对我有用，但对初学者来说是一个很好的起点，但不一定对每个人都有用。

熟能生巧；在 GitHub 上阅读其他开发者的代码也是如此。我强烈建议您出去看看其他一些 Python 项目，并向其他人学习。

[](https://medium.com/better-programming/beginning-python-programming-part-17-2d7f4f85efd9) [## Python 编程入门—第 17 部分

### 总结工作场所优化

medium.com](https://medium.com/better-programming/beginning-python-programming-part-17-2d7f4f85efd9)
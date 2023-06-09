# 从熟练开发人员的角度学习数据科学

> 原文：<https://betterprogramming.pub/learning-data-science-from-the-perspective-of-a-proficient-developer-59c258e4d7ae>

## 当您已经是一名优秀的开发人员时，开始使用数据科学

![](img/57ab310c91707f0e7874b6524e2a322c.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

如你所知，数据科学，更确切地说是机器学习，现在非常流行*，所以你猜怎么着？我决定报名参加 MOOC 课程，以便精通数据科学。但是，当你开始有一个强大的开发背景时，这与它是你编程的主要经验之一时是完全不同的。原因如下。*

# *德莱弗斯技能习得模型*

*Dreyfus 模型指出，学习者会经历不同的阶段来定义他们如何看待一个情况，他们关注哪些因素，他们在结果中的参与程度，以及他们如何解决问题。为了简单起见，我将关注开发人员根据模型可以表现出的三种面貌:初学者(又名新手)、熟练者(又名主管)和专家(又名大师)。*

*   *新手套用自己知道的菜谱，不考虑上下文(没有情境意识)。他们寻找直截了当的规则，以便在安全的环境中进行实验。初学者通常专注于最简单的任务，放弃较难的。*
*   *熟练者可以根据上下文选择食谱。他们专注于在不同的背景下试验这些规则，需要一个能提供即时反馈的环境。如果不值得，他们可能更愿意避免使用第三方工具。*
*   *专家为需求创造新的配方，知道一切都取决于环境。他们凭直觉把握形势，知道什么是可能的，什么是困难的。为了降低风险，他们通常从最困难的任务或结果最差的任务开始。*

*一言以蔽之:不同的阶段意味着不同的需求。虽然编程可以被看作是数据科学的工具，而不是数据科学本身，但它可能是你日常实践中占用最多的东西(除非你是理论家)。因此，当你已经掌握了编程的实用艺术时，作为一个所谓的初学者学习数据科学不会那么有效。你最好把注意力放在那些不会到来的事情上。*

# *Python 为王*

*由于世界各地的数据科学家似乎都在使用 Python，您可能认为您首先需要学习 Python 编程。没有比这更真实的了。您实际上不需要成为 Python 专家。只要简单地了解基础知识，通过与其他语言的类比，你就已经差不多掌握了:导入一个模块，打印一些格式化的数据，如果你愿意遵循 [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) 原则，创建函数，处理循环或分支。您很少需要处理面向对象的编程或设计模式。*

*如果你是一个精通开发的人，想掌握数据科学，先深入学习 Python 很可能是浪费时间。*

*如果 Python 是王者，皇家宫廷实际上就是我所推荐的专注于**、**即**、**的数据科学生态系统，更具体地说:*

*   *跑步 [Jupyter](https://jupyter.org/) 因为大多数教程、实践课或练习都是以笔记本的形式提供的。*
*   *使用 [NumPy](https://numpy.org/) 进行基本统计，如计算平均值、中值、最小值/最大值、标准偏差、方差和分位数值。*
*   *使用 [pandas](https://pandas.pydata.org/) 从内存、网络或基于文件的值(如 CSV)创建数据集，并显示直方图/箱线图。*
*   *使用 [Matplotlib](https://matplotlib.org/) 显示分类数据的基本散点图或曲线图和饼图，使用 [seaborn](https://seaborn.pydata.org/) 显示[高级显示](https://towardsdatascience.com/how-to-do-visualization-using-python-from-scratch-651304b5ee7a)如配对图或热图(如相关矩阵、混淆矩阵)。*
*   *使用 [SciPy](https://www.scipy.org/) 进行高级统计，如玩概率密度函数、z 分数和显示 Q-Q 图。*
*   *使用 [statsmodels](https://www.statsmodels.org/) 和 [scikit-learn](https://scikit-learn.org/) 进行建模([回归](https://levelup.gitconnected.com/an-introduction-to-regression-in-python-with-statsmodels-and-scikit-learn-9f75c748f56e)、[分类](https://towardsdatascience.com/machine-learning-with-python-classification-complete-tutorial-d2c99dc524ec)等)。).*

*如果您想要精通生态系统，最重要的事情之一是如何在所有数据科学工具包之间传递数据。简而言之，我想说 pandas 为您提供了一种叫做[数据帧](https://pandas.pydata.org/pandas-docs/stable/reference/frame.html)的数据格式。基本上，数据框相当于电子表格(即，可能具有不同数据类型的命名列)，但具有超级能力(即，精心设计的选择和关键机制)。NumPy、Matplotlib 和 SciPy 是低级工具包，它们将本机数组作为输入和/或透明地封装它。大多数时候你只需要从[数据帧](https://pandas.pydata.org/pandas-docs/stable/reference/frame.html)中提取[系列](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.html)就可以使用这些工具包。建模工具包更高级，通常直接管理作为输入的数据帧。你也可以看看一些直接管理数据帧作为输入的工具包，比如 [Altair](https://altair-viz.github.io/) 或 [Joypy](https://github.com/sbebo/joypy) 用于可视化。*

*如果您是一名熟练的开发人员，并且希望掌握数据科学，那么学习如何高效地使用 pandas 数据框可能是值得的。*

*您可以使用基于 GUI 的工具包先浏览和分析数据框内容，从而节省时间。你也可以通过使用[熊猫的 IO 工具](https://pandas.pydata.org/pandas-docs/stable/user_guide/io.html)存储原始或处理过的数据帧来节省大量处理能力。事实上，大多数笔记本都是通过读取 CSV 数据文件来工作的，这对于保存数据来说效率非常低，而绝对有[更多的选择](https://towardsdatascience.com/stop-persisting-pandas-data-frames-in-csvs-f369a6440af5)。*

*Anaconda 通常用于开始处理数据，因为它附带了一系列数据科学包，允许您管理不同的环境，以减少处理各种版本时的问题。然而，Anaconda 是一个相当大的软件，有超过 200 个包，所以当更新时，你有时不得不等待几个小时。比起初学者，您更精通包管理([依赖地狱](https://en.wikipedia.org/wiki/Dependency_hell))，您可能更喜欢 [Miniconda](https://docs.conda.io/en/latest/miniconda.html) ，它是 Anaconda 的一个小型引导版本，只包含最有用的组件。*

# *正态分布是女王*

*因为似乎所有的数据科学家都在使用正态分布，你可能认为你首先需要了解什么是高斯分布。没有比这更真实的了。事实上，[中心极限定理](https://statisticsbyjim.com/basics/central-limit-theorem/)指出，对于足够大的样本量(统计学家通常推荐 30)，变量均值的抽样分布遵循正态分布，而不管它在总体中的分布如何。因此，许多统计方法，包括相关、回归、 [t 检验](https://statisticsbyjim.com/hypothesis-testing/t-tests-t-values-t-distributions-probabilities/)、[异常值](https://towardsdatascience.com/ways-to-detect-and-remove-the-outliers-404d16608dba) [检测](https://towardsdatascience.com/5-ways-to-detect-outliers-that-every-data-scientist-should-know-python-code-70a54335a623)和[方差分析](https://medium.com/@StepUpAnalytics/anova-one-way-vs-two-way-6b3ff87d3a94)，都假设感兴趣的值是正态分布的。注意，根据不同的方法，[并不总是意味着数据是正态分布的](https://towardsdatascience.com/the-worst-misconception-in-statistics-488cf73d734e)，而是数据的平均值或误差残差。*

*然而，不确认输入数据符合用于分析它们的统计方法的假设是研究中最常见的错误之一。*

*考虑到，作为初学者，在对数据应用方法(上下文感知)之前，您并不总是被告知使用视觉检查(例如直方图和 Q-Q 图)和/或显著性测试(例如夏皮罗-维尔克测试)来检查正态性。你至少可以在解释结果之前看看数据。*

*机器学习实际上是对新情况的一种概括。只存在于特定数据集中的模式是没有意义的，你要寻找的是同样存在于其他数据集中的模式。分布就是这些类型的模式，如果你正在研究平均值，这是很常见的，正态分布是女王。*

# *生产差距是真实的*

*与任何研究活动一样，数据科学的很大一部分致力于实验:模型工程、超参数优化、模型测试等。在学习时，这种情况会进一步加剧，因为老师更喜欢完全受控的环境(通常是你的笔记本电脑)，以避免技术问题并专注于概念。*

*然而，作为一名熟练的开发人员，您自然会意识到生产约束以及探索性代码和生产就绪代码之间的差距。一方面，采用云作为应用的主要基础设施对笔记本电脑提出了挑战，当在笔记本电脑上本地运行时，这模糊了客户端-服务器架构的推理。另一方面，现实世界的数据可能非常庞大，而且模型对笔记本电脑来说计算量太大。类似生产的环境可以提供更多探索数据和提高技能的机会。最后但同样重要的是，由于 exploration 非常容易出错，您应该能够轻松地恢复您的工作的旧版本，并可能在团队中同时工作。*

*因此，您应该尽早采用以下方法，以显著减少勘探到生产的差距。*

## *1.设置开发工作流*

*[GitHub](https://github.com/) 已经成为开发版本控制系统的事实标准，但是数据科学展示了它不能直接解决的特定问题。首先，软件项目结构并不真正相关；类似于 [Cookiecutter 数据科学](https://github.com/drivendata/cookiecutter-data-science)的东西可能是更好的选择或者至少是灵感的来源。第二，您必须管理大型数据文件和笔记本，这不是真正的 Git 友好(区分/合并操作将不是人类可读的)。希望[数据版本控制(DVC)](https://github.com/iterative/dvc) 可以在 AWS S3 或 Google Drive 等基于云的服务上为你管理数据集版本和存储。此外，[NBD ime](https://github.com/jupyter/nbdime)/[nbstripout](https://github.com/kynan/nbstripout)可以帮助你在笔记本上保持 Git 操作流畅。*

## *2.让您的工作可配置*

*为了找到数据的最佳模型，您经常需要调整一些参数，但是更改笔记本中的硬编码值并不是最有效的方法。 [papermill](https://github.com/nteract/papermill) 根据一些输入参数将任何笔记本包装成新的笔记本，就像您执行可配置的命令行脚本一样。有了 [Streamlit](https://www.streamlit.io/) ，你可以让每个参数成为一个[全交互网络应用](https://towardsdatascience.com/coding-ml-tools-like-you-code-ml-models-ddba3357eace)中的一个小部件。但是，您首先需要使用 nbconvert 将笔记本导出为 Python 脚本。*

## *3.让你的作品可调试*

*为了调试，在代码中添加一个`print()`语句可能是开发人员最常见的做法之一。然而，正如我之前提到的，还有许多更复杂的解决方案。*

*日志记录是一个非常强大的工具，但这一切都是为了使用正确的工具来完成工作。首先，您应该能够根据不同的严重性级别(例如，开发信息、生产错误消息等)过滤/激活您的日志。).其次，您应该能够在需要时深入检查对象和堆栈跟踪，以避免遗漏上下文信息。第三，您还应该能够将日志写到一个文件中，而不是屏幕上，或者除了屏幕之外。最后但同样重要的是，您应该能够根据不同的领域按需过滤日志(例如，数据预处理、模型拟合等。)以避免提供太多关于大型项目的信息。例如， [loguru](https://github.com/Delgan/loguru) 可能是 Python 的一个很好的选择，尽管内置的[日志模块](https://docs.python.org/3/howto/logging.html)已经提供了丰富的特性。*

*当然，如果可能的话(例如，非生产环境)，最好的选择仍然是使用调试器。与任何编程语言一样，Python 带有内置的[调试器](https://medium.com/analytics-vidhya/are-you-writing-print-statements-to-debug-your-python-code-690e6ba098e9)；你只需要学会如何有效地使用这种武器。如果你想设置断点或者更舒适地使用单步执行，你将需要一个支持交互式调试的 IDE(集成开发环境)，像 [Visual Studio Code](https://code.visualstudio.com/) 或者 [PyCharm](http://www.jetbrains.com/pycharm/) 。*

## *4.让你的工作成为一个数据管道*

*笔记本基本上是包含代码、可视化和文本的交互式网络界面。它们非常适合于实验或数据分析，但是，它们并不是为通过 CI 管道自动运行或处理而设计的。在某种程度上，笔记本在生产中的使用甚至可以被视为一种[反模式](https://www.kdnuggets.com/2019/11/notebook-anti-pattern.html)。*

*将您的工作转换为可执行管道的最简单方法是使用 [nbconvert](https://github.com/jupyter/nbconvert) 将您的笔记本导出为 Python 脚本，或者通过 [papermill](https://github.com/nteract/papermill) 运行它。然而，更好的方法可能是预先开始使用脚本，因为没有什么可以阻止您将脚本[导入到您的笔记本](https://github.com/dssg/hitchhikers-guide/tree/master/sources/curriculum/0_before_you_start/pipelines-and-project-workflow#workflow)(这只是 Python 代码，不是吗？).这样，您也可以从非交互流程中调用它。例如，DVC(我们之前已经遇到过)为您提供了一种捕获[数据管道](https://dvc.org/doc/start/data-pipelines)的机制——产生最终结果的一系列数据过程。*

## *5.利用托管服务提供的机会*

*随着数据变得越来越大，模型变得越来越复杂，机器学习任务对你的笔记本电脑来说开始变得过于计算密集型。那么将(部分)工作负载委托给云服务通常是值得的。 [Kaggle](https://www.kaggle.com/) 、 [Google Colaboratory](https://colab.research.google.com/) 、 [AI 平台笔记本](https://cloud.google.com/ai-platform-notebooks/)、[亚马逊 SageMaker](https://aws.amazon.com/sagemaker/) 、 [FloydHub](https://www.floydhub.com/) 、 [SaturnCloud](https://www.saturncloud.io/) 和 [CoCalc](https://github.com/sagemathinc/cocalc) 可能是其中最知名的服务。一个新的趋势是为机器学习提供更多的集成平台，而不仅仅是 SaaS 笔记本，比如 OVH 和 TADA 提供的那些平台。*

# *简而言之，机器学习*

*你可能会奇怪，为什么我到目前为止还没有谈到任何机器学习算法。在我看来，数据科学最困难和最耗时的部分实际上是数据工程(即，使数据可用)，而不是模型工程(即，使数据有用)。在机器学习工具包的帮助下，构建模型实际上非常简单，并且[过程](https://medium.com/@alexstrebeck/training-and-testing-machine-learning-models-e1f27dc9b3cb)几乎是标准化的，如以下伪代码所示:*

1.  *选择您想要使用的模型，并使用一组选定的参数(也称为超参数)创建一个实例。*
2.  *将输入数据集拆分为训练数据集(用于构建模型)和测试数据集(用于验证模型)。*
3.  *使您的模型符合训练数据集。*
4.  *在测试数据集上评估模型的准确性和性能。*
5.  *使用您的模型从新的输入数据预测输出数据。*

*工具包还为您提供了帮助或自动化模型工程、超参数优化和模型测试的功能。所以当你是一个熟练的程序员时，使用机器学习工具包提供的“黑盒”建模实际上是非常简单的。以我的拙见，作为一个精通开发的人，需要注意的最重要的事情之一就是机器学习的[局限性](https://towardsdatascience.com/the-limitations-of-machine-learning-a00e0c3040c6)。事实上，由于你的经验，你可能很快就能完成像样的数据和模型工程。然而，你必须记住几件事。*

*首先，没有数据，机器学习什么都不是，但你可能永远不会知道你的数据是否足够好，足以为你的用例捕捉所有真实世界的效果。显然，机器学习不能应用于输入数据稀缺的问题。但是，即使在大量数据可用的情况下，数据也可能有噪声，包括[误差](https://medium.com/towards-artificial-intelligence/sources-of-error-in-machine-learning-33271143b1ab)或[偏差](https://news.mit.edu/2019/using-ai-predict-breast-cancer-and-personalize-care-0507)。*

*第二，机器学习通常基于随机的，而不是确定性的过程(更具体地说，是深度学习)。通常，模型不理解物理定律或包括物理约束，并且可能预测不切实际的值。简而言之，比起我们自己的判断，更信任算法会导致问题和糟糕的解释能力。*

*最后但同样重要的是，机器学习旨在揭示输入数据和模型结果(即预测)之间的相关性。当人类的大脑试图寻找模式时，即使它们并不存在。然而，相关性并不意味着因果关系。因果关系是关于证明影响预测的真正因素，而模型通常采取“足够好”的捷径。例如，在现实生活中，从 A 到 D 的路径在到达 D 之前会经过 B 和 C，而模型可能会在 A 和 D 之间建立直接关系。*

*随着数据科学和机器学习生态系统如今快速发展，我想以此作为结束，建议您也探索 Python 之外的新实验方式，使用 [hydrogen](https://atom.io/packages/hydrogen) 、[interact](https://nteract.io/)或 [Polynote](https://polynote.org/) 。*
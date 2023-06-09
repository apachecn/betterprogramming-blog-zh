# 该不该用机器学习？

> 原文：<https://betterprogramming.pub/should-you-use-machine-learning-73a7746f7280>

## 如何知道机器学习能否解决你的工程或商业问题

![](img/e73953edd15c5eb19a04f1bce6a62965.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Franck V.](https://unsplash.com/@franckinjapan?utm_source=medium&utm_medium=referral) 拍摄的照片

# 介绍

使用机器学习来解决技术问题已经变得非常流行。公司花费大量的时间和精力来寻找能够利用这些技术来处理复杂任务的员工，这些任务以前可能无法用传统方法解决。

然而，如何知道自己是否真的需要机器学习呢？

尽管这些技术非常有用，但它们并不适合所有情况。如果你试图将机器学习应用于一个不合适的任务，你可能会浪费时间和金钱，最终可能会得到一个性能很差的无用模型。

在这篇文章中，我将讨论一些你在决定机器学习是否适合你的情况时应该问的问题。这些应该作为指导你决策过程的框架。

# 任务

首先要考虑的是你的任务是否很适合机器学习。

**你有一个定义明确、输入和输出清晰的问题吗？**你必须对你的模型的输入和输出有一个清晰的概念。否则，你可能会在生产机器学习模型的特征工程和评估阶段遇到困难。

**您是否有可以用来评估模型性能和比较不同模型的指标？没有一个简单的方法来评估模型，你将很难决定你的模型是否成功，以及选择使用哪个模型。随着您的用例随着时间的推移而发展，您也将很难迭代和改进您的模型。**

**问题是否需要近似解？**大多数机器学习算法都是在没有确切的方法找到解决方案，或者确切的解决方案实现起来成本太高的情况下使用的。如果您的问题确实有一种方法可以准确地解决它，例如通过使用正则表达式、线性编程等经典优化技术，或者约束满足问题等更古老的人工智能技术，那么您可能会更好地使用这些方法。

问题符合机器学习范式吗？大多数机器学习算法都依赖于当前数据将有助于预测或分类未来数据的想法。如果你的情况倾向于外部事件使以前的数据无效，那么机器学习很可能不会有效。同样，如果以前的数据与将来的数据没有相关性，您的模型将无法了解任何有助于您理解真实世界设置中的传入数据的有用趋势。为了有效地使用机器学习，您的模型必须看到相关的过去数据。

# 数据

接下来，你必须确定你的数据是否适合机器学习。

**你有可靠的数据标签吗？**大多数机器学习方法(受监督的那种)依赖于你拥有的每个数据点的标签的存在。这些标签应该尽可能没有噪音，并且应该可以以合理的价格获得。如果您的标注噪音太大，要么是由于数据收集中固有的情境困难，要么是由于标注质量差，那么您的模型很可能无法正确学习数据中的关系。此外，如果获取标签的成本太高，您可能无法在模型的整个生命周期内获取足够的训练数据来使其能够正确学习。

**数据适合机器学习吗？**您用来训练模型的数据必须准确地代表将使用它的真实世界的数据。这并不意味着它必须完美地反映它，但它越接近，你的模型就越有用和准确。尽管有一些技术可以改善围绕类不平衡和缺乏数据可用性的问题，但是如果您能够为您的模型充分提供反映其真实世界输入的训练数据，这总是最好的。如果使用有偏差的训练数据训练模型，并且可用的特征工程和预处理方法不充分，则模型在面对真实数据时可能会表现得出乎意料地差。例如，如果您在分类设置中使用严重不平衡的数据集来训练模型，则可能会出现这种情况。如果您的模型基于其训练数据期望看到 1%的 A 类和 99%的 B 类，那么如果现实世界中有 50%的 A 类和 50%的 B 类(假设您的目标是最大化准确性)，它将表现不佳。

# 模型

最后，你必须调查在你的机器学习模型的生命周期中可能发生的事件。

模型的效果和风险是否被充分理解？你充分意识到你的机器学习模型的社会影响了吗？例如，你有没有研究过这种模式是否会加剧社会经济不平等，或者它是否会在不同社会经济群体之间制造隔阂？除了具体的工程或业务问题之外，充分理解模型的效果是很重要的。如果你的模型容易出现算法偏差，那么你必须提前解决这个问题，并尝试创建一个训练管道，尽可能地从数据中消除偏差。此外，了解对手如何滥用这种模式也很重要。有没有什么方法可以通过逆向工程模型的输出来获取个人信息？行业机密可以泄露吗？关于这些问题，重要的是您要理解通过您的模型的输出哪些数据是可见的，以及谁能够直接访问这些输出。模糊输出可能是有用的，这样您就可以控制透露什么信息，并严格提供必要的信息。

**你能长期维持这种模式吗？**在大多数情况下，机器学习模型在整个时间内都在使用，并不局限于单一的使用实例。因此，您的组织拥有能够长期维护模型的人员是非常重要的。随着现实情况的变化和漂移，您可能需要根据当前数据重新训练您的模型。例如，输入自然语言的模型将需要定期重新训练，以包含语言使用(如俚语)的变化。即使真实世界的数据不会随着时间的推移而改变，您也可能希望研究模型的错误并不断迭代模型以提高性能。因此，您很可能需要一组能够监控和改进模型的专门员工。否则，它可能会很快变得过时甚至无用，这取决于它的领域变化的趋势。
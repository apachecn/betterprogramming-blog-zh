# 7 个 PyTest 特性和插件将为您节省大量时间

> 原文：<https://betterprogramming.pub/7-pytest-features-and-plugins-that-will-save-you-tons-of-time-74808b9d4756>

## 学习最好的 PyTest 命令来加速您的开发过程

![](img/00f194d30ba1af7b09454b6fac49b7e1.png)

*图片由* [*米格尔·布里托*](https://miguendes.me) *—无版权*

在本教程中，我们将学习最好的`pytest`特性和插件来加速你的开发过程。它们非常简单，你可以马上开始使用。

# 1.第一次失败时停止测试会话

运行大型项目的完整测试套件可能需要很长时间。无论您是在本地还是在 CI 服务器上执行它们，在耐心等待之后看到测试失败总是令人沮丧的。

在某些情况下，您可能希望在第一次失败后中止整个会话，以便可以立即修复中断的测试。

幸运的是，`pytest`提供了一个非常方便的 CLI 选项，即`-x / --exitfirst`。

# 2.仅重新运行上次失败的测试

当在本地开发时，您可能更喜欢在将它们推送到您的 repo 之前运行所有的测试。

如果你正在做一个只有几个测试的小项目，重新运行所有的测试是可以的。

然而，如果整个测试套件需要几分钟才能运行，那么您可能只想执行那些失败的测试套件。`pytest`允许通过`--lf`或`--last-failed`选项。

这样你可以节省宝贵的时间，迭代更快！

# 3.从最后失败的测试开始，重新运行整个测试会话

与前面的命令类似，重新运行一切可能会有所帮助。

唯一的区别是，您想先从失败的测试开始。为此，使用`--ff`或`--failed-first`标志。

# 4.显示失败测试的局部变量

我们已经了解了加快迭代速度的重要性，以及它如何为你节省宝贵的时间。

同样，我们能够获得关键的提示来帮助我们调试失败的测试也是非常重要的。

通过使用`--showlocals`标志，或者简称为`-l`，我们可以在回溯中看到所有局部变量的值。

# 5.仅运行测试的子集

有时你只需要运行测试的一个子集。一种方法是运行一个单独文件的所有测试用例。

例如，你可以做`pytest test_functions.py`。虽然这比什么都跑要好，但是我们还是可以改进的。

通过使用`-k`选项，用户可以指定`pytest`将用来选择要执行的测试的关键字表达式。

假设您需要运行前两个测试，您可以传递一个由`or`分隔的关键字列表:

输出:

# 6.并行运行测试

一个项目的测试越多，运行所有测试的时间就越长。这听起来是一个不争的事实，但是它经常被忽视。

一个接一个地运行大量的测试套件是对时间的极大浪费。

加快执行速度的最佳方式是并行执行并利用多个 CPU。

可悲的是，`pytest`没有类似的功能，所以我们必须依靠插件。最好的`pytest`插件是 [pytest-xdist](https://github.com/pytest-dev/pytest-xdist) 。

要将您的测试发送到多个 CPU，使用`n`或`--numprocesses`选项。

如果您不知道您有多少可用的 CPU，您可以告诉`pytest-xdist`用`auto`值在所有可用的 CPU 上运行测试。

# 7.重新运行片状测试并消除间歇性故障

最令人沮丧的情况之一是，看到所有测试在本地通过，却在 CI 服务器上失败。有几个原因会导致这样的失败，但大多数都是“不可靠”测试的结果。

“易变的”测试是以不确定的方式间歇性失败的测试。通常，重新运行它们就足以使它们通过。

问题是，如果您有一个长时间运行的测试套件，您需要重新触发 CI 步骤并等待几分钟。这是一个很大的时间陷阱，幸运的是可以避免。

因此，为了改善这一点，我们可以重新运行“薄片”测试是理想的。通过这样做，我们可以增加通过的机会，避免 CI 步骤的彻底失败。

最好的`pytest`插件是 [pytest-rerunfailures](https://gist.github.com/miguendes/pytest-rerunfailures) 。这个插件尽可能多地重新运行测试，从而消除了间歇性故障。

使用它的最简单的方法是传递您希望测试运行的最大次数的`--reruns`选项。

如果你提前知道一个单独的测试是不可靠的，你可以标记他们重新运行。

# 结论

大型测试套件可以给项目带来很多保证，但也伴随着成本。长时间运行的测试会话会消耗大量开发时间，并使迭代变慢。

通过利用`pytest`特性和它的插件生态系统，有可能大大加快开发过程。

在本教程中，我们看了 7 个可以用来改善我们的生活和减少执行测试时间的技巧。

## 想联系作者吗？

*本帖原载于*[*miguendes . me*](https://miguendes.me/7-pytest-features-and-plugins-that-will-save-you-tons-of-time)*。*
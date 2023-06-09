# 第 30 到 11 行代码:Python 中的石头剪刀布

> 原文：<https://betterprogramming.pub/from-30-to-11-lines-of-code-rock-paper-scissors-in-python-5bfa4313a8a7>

## 重温流行的初学者游戏

![](img/75ee12b8abcda47398554af2bb7bb958.png)

图片由 Mariet Jieopp 为 Getty Images 提供(与 Canva Pro 许可一起使用)

当你第一次学习编程时，你会寻找——或者，可能会被分配——强化基本概念的项目。但是，一旦你获得了更多的知识和经验，你多久会从一个高级程序员的角度重新审视那些初学者项目呢？

在这篇文章中，我想这样做。我想重温一个常见的初学者项目——用 Python 实现游戏“石头剪子布”——用我在将近八年的 Python 编程经验中获得的知识。

# “石头剪刀布”的规则

在深入研究代码之前，让我们先概述一下“石头剪子布”是如何玩的。两名玩家每人选择三个项目中的一个:石头、布或剪刀。玩家同时向对方展示他们的选择，获胜者由以下规则决定:

1.  石头打败剪刀
2.  剪刀打败了布
3.  纸打败了石头

从小到大，我和我的朋友们用“石头剪刀布”解决各种各样的问题。在单人电子游戏中谁先开始玩？谁得到最后一罐汽水？谁要去收拾我们刚刚制造的烂摊子？重要的东西。

# 要求

让我们列出实现的一些要求。与其构建一个完整的游戏，不如让我们专注于编写一个名为`play()`的函数，它接受两个字符串参数——每个玩家选择的`"rock"`、`"paper"`或`"scissors"`——并返回一个字符串，指示获胜者(例如`"paper wins"`)或游戏结果是否相同(例如`"tie"`)。

下面是一些关于如何调用`play()`以及它返回什么的例子:

如果两个参数中的一个或两个无效，意味着它们不是`"rock"`、`"paper"`或`"scissors"`中的一个，那么`play()`应该抛出某种异常。

`play()`也应该是**可换的**。也就是说，`play("rock", "paper")`应该返回和`play("paper", "rock")`一样的东西。

# “初学者”解决方案

为了设置比较基准，考虑初学者如何实现`play()`函数。如果这个初学者和我第一次学习编程时一样，他们可能会开始写下一大串`if`语句:

严格来说，这段代码没有什么错。它运行无误，符合所有要求。这也类似于谷歌搜索“石头剪子布 python”的一些高级实现

但是，有经验的程序员会很快识别出许多代码气味。特别是，代码是重复的，并且有许多可能的执行路径。

# 高级解决方案#1

从更高级的角度来看，实现“石头剪刀布”的一种方法是利用 Python 的字典类型。字典可以根据游戏规则将项目映射到它们击败的项目。

让我们称这本字典为:

`loses_to`提供了一个简单的 API 来确定哪个项目输给了另一个项目:

字典有几个好处。您可以使用它来:

1.  通过检查成员资格或发起`KeyError`来验证所选项目
2.  通过检查值是否输给相应的键来确定获胜者

考虑到这一点，`play()`函数可以写成如下形式:

在这个版本中，`play()`在试图访问一个无效键时利用了由`loses_to`字典引发的内置`KeyError`。这有效地验证了玩家的选择。因此，如果任何一个玩家选择了一个无效的物品——类似于`"lizard"`或`1234`——`play()`会引发一个`KeyError`:

尽管`KeyError`不像`ValueError`那样有描述性消息，但它仍然完成了工作。

新的`play()`功能比原来的简单多了。不需要处理一堆显式案例，只需要检查三个案例:

1.  `player2_choice`输给`player1_choice`
2.  `player1_choice`输给`player2_choice`
3.  `player1_choice`和`player2_choice`相同

然而，还有第四种隐藏的情况，你几乎要眯着眼睛才能看到。这种情况发生在其他三种情况都不为真时，在这种情况下`play()`返回一个`None`值。

但是……这种情况真的会发生吗？实际上，不。它不能。根据游戏规则，如果玩家 1 不输给玩家 2 *玩家 2 不输给玩家 1*，那么两个玩家一定选择了相同的物品。

换句话说，如果另外两个`if`块都没有执行，我们可以从`play()`中删除最后一个`if`块，只删除`return "tie"`:

我们已经做了妥协。我们牺牲了清晰性——我认为与“初学者”版本相比，理解上述`play()`功能如何工作需要更大的认知负荷——以便缩短功能并避免不可及的状态。结果，我们将 30 行初学者解决方案减少到只有 11 行代码。

# 高级解决方案#2

前面的解决方案效果很好。它的可读性*和*比“初学者”的解决方案要短得多。但是不太灵活。也就是说，如果不重写一些逻辑，它就不能处理“石头剪刀布”的变体。

例如，有一个叫做“石头剪刀布蜥蜴史波克”的变种，有一套更复杂的规则:

1.  石头打败剪刀和蜥蜴
2.  纸打败了石头和斯波克
3.  剪刀打败了纸和蜥蜴
4.  蜥蜴打败了斯波克和纸
5.  斯波克打败剪刀和石头

如何修改代码来处理这种变化？

首先，用 Python 集合替换`loses_to`字典中的字符串值。每个集合包含输给相应键的所有项目。下面是这个版本的`loses_to`使用原来的“石头剪刀布”规则的样子:

为什么设置？因为我们只关心*什么*项输给了一个给定的键。我们不关心那些项目的*顺序*。

为了让`play()`处理新的`loses_to`字典，您所要做的就是用`in`替换`==`,以使用成员检查来代替相等检查:

花点时间运行这段代码，验证一切仍然正常。

现在用一个实现“石头剪刀布蜥蜴史波克”规则的字典替换`loses_to`看起来是这样的:

新的`play()`函数完美地与这些新规则一起工作:

在我看来，这是选择正确的数据结构的力量的一个很好的例子。通过使用集合来表示在`loses_to`字典中输给一个键的所有条目，并用`in`替换`==`，您不必添加一行代码就可以得到一个更通用的解决方案。

# 高级解决方案#3

让我们后退一步，采取一种稍微不同的方法。我们将构建一个包含所有可能输入及其结果的表格，而不是在字典中查找条目来确定获胜者。

您仍然需要一些东西来表示游戏规则，所以让我们从上一个解决方案的`loses_to`格言开始:

接下来，编写一个函数`build_results_table()`，它接受一个规则字典，比如`loses_to`，并返回一个新的字典，将状态映射到结果。例如，下面是用`loses_to`作为参数调用时`build_results_table()`应该返回的内容:

如果你觉得那里有什么不对劲，那你就对了。这本字典有两个错误:

1.  **集像** `**{"rock", "rock"}**` **一样不能存在。**集合不能有重复的元素。在真实场景中，这个场景看起来像`{"rock"}`。你实际上不需要太担心这个。我用两个元素写了这些集合，以明确这些状态代表什么。
2.  **不能使用集合作为字典键**。但是我们*希望*使用集合，因为它们自动为我们处理交换性。也就是说，`{"rock", "paper"}`和`{"paper", "rock"}`的值相等，因此在查找时应该返回相同的结果。

解决这个问题的方法是使用 Python 内置的`[frozenset](https://docs.python.org/3/library/stdtypes.html?highlight=frozenset#frozenset)` [类型](https://docs.python.org/3/library/stdtypes.html?highlight=frozenset#frozenset)。像集合一样，`frozensets`支持成员检查，当且仅当两个集合有相同的成员时，它们与另一个`set`或`frozenset`比较相等。然而，与标准集不同的是，`frozenset`实例是不可变的。因此，它们可以用作字典键。

要实现`build_results_table()`,您可以循环遍历`loses_to`字典中的每个键，并为对应于该键的集合中的每个字符串值构建一个`frozenset`实例:

这让你走了一半的路:

不过，导致平局的州没有包括在内。要添加这些，您需要为映射到字符串`"tie"`的`rules`字典中的每个键创建`frozenset`实例:

现在由`build_results_table()`返回的值看起来是正确的:

为什么要经历这么多麻烦？毕竟，`build_results_table()`看起来比前一个解决方案中的`play()`函数更复杂。

你没有错，但是我想指出这种模式可能非常有用。如果一个程序中可以存在有限数量的状态，通过预先计算所有这些状态的结果，有时可以看到速度的显著提高。对于像“石头剪子布”这样简单的事情来说，这可能有些矫枉过正，但是在有几十万甚至几百万个州的情况下，这可能会产生巨大的影响。

这种方法有意义的一个真实场景是在强化学习应用中使用的 [Q-learning 算法](https://en.m.wikipedia.org/wiki/Q-learning)。在该算法中，维护一个状态表(Q 表),该表将每个状态映射到一些预定动作的一组概率。一旦一个代理被训练，它就可以根据观察到的状态的概率选择一个动作，然后相应地采取行动。

通常，一个类似于由`build_results_table()`生成的表被计算出来，然后存储在一个文件中。当程序运行时，预先计算的表被加载到内存中，然后由应用程序使用。

所以，现在您有了一个可以构建结果表的函数，将`loses_to`的表分配给一个`outcomes`变量:

现在您可以编写一个`play()`函数，根据传递给 play 的参数在`outcomes`表中查找状态，然后返回结果:

这个版本的`play()`简单得令人难以置信。就两行代码！如果你愿意，你甚至可以把它写成一行:

我个人更喜欢双线版，而不是单线版。

您的新`play()`函数遵循游戏规则，并且是可交换的:

`play()`甚至在被无效选择调用时引发一个`KeyError`,但是这个错误已经没有那么多帮助了，因为`outcomes`字典的键已经设置好了:

然而，这个模糊的错误可能不是问题。在本文中，您只实现了`play()`函数。在“石头剪子布”的真实实现中，您最有可能在将用户的选择传递给`play()`之前捕获用户输入并验证它。

那么，与以前的实现相比，这个实现快了多少呢？下面是一些计时结果，用来比较使用 IPython 的`%timeit`神奇函数的各种实现的性能。`play1()`是[高级解决方案#2](#advanced-solution-2) 部分的`play()`版本，`play2()`是当前版本:

在这种情况下，使用结果表的解决方案实际上比之前的实现慢*。这里的罪魁祸首是将函数参数转换成一个`frozenset`的那一行。因此，尽管字典查找速度很快，并且构建一个将状态映射到结果的表可以潜在地提高性能，但是您需要小心避免昂贵的操作，这些操作可能最终会抵消您期望获得的任何收益。*

# *结论*

*我写这篇文章是作为练习。我很好奇，既然我已经有了很多经验，我该如何对待一个像 Python 中的“石头剪刀布”这样的初学者项目。我希望你觉得有趣。如果你现在有任何灵感的暗示来重温你自己的一些初学者项目，那么我想我已经完成了我的工作！*

*如果你确实重温了一些你自己的初学者项目，或者如果你过去曾经这样做过，请在评论中告诉我进展如何。你学到新东西了吗？你的新解决方案和你作为初学者写的有多大不同？*

# *是什么激发了这篇文章？*

*朱莉娅圈子里的一个熟人[米格尔·拉兹·古斯曼·马塞多](https://github.com/miguelraz)向我推荐了由[摩西·佐丹奴](https://giordano.github.io/aboutme/)撰写的[博客文章。Mosè利用 Julia 的](https://giordano.github.io/blog/2017-11-03-rock-paper-scissors/)[多重分派](https://en.m.wikipedia.org/wiki/Multiple_dispatch)范例，用不到十行代码编写了“石头剪刀布”:*

*[](https://giordano.github.io/blog/2017-11-03-rock-paper-scissors/) [## 不到 10 行代码的石头剪刀布游戏

### 石头剪子布是一种流行的手游。然而，一些书呆子可能更喜欢在他们的电脑上玩这个游戏，而不是…

giordano.github.io](https://giordano.github.io/blog/2017-11-03-rock-paper-scissors/) 

我不会深入讨论 Mosè代码如何工作的细节。Python 甚至不支持开箱即用的多分派。(虽然你可以在`[plum](https://github.com/wesselb/plum)` [包](https://github.com/wesselb/plum)的帮助下使用它。)

Mosè的文章让我的大脑运转起来，并鼓励我重温 Python 中的“石头剪刀布”,思考如何以不同的方式处理这个项目。

然而，当我研究这个解决方案时，我想起了很久以前我为 Real Python 写的一篇评论文章:

[](https://realpython.com/python-rock-paper-scissors/) [## 制作你的第一个 Python 游戏:石头、剪子、布！-真正的蟒蛇

### 免费奖励:关于 Python 精通的 5 个想法，这是一个面向 Python 开发者的免费课程，向你展示了路线图和…

realpython.com](https://realpython.com/python-rock-paper-scissors/) 

事实证明，我在这里“发明”的前两个解决方案与 Real Python 文章的作者 Chris Wilkerson 提出的解决方案相似。

Chris 的解决方案功能更全。它包括一个交互式游戏机制，甚至使用 Python 的`Enum`类型来表示游戏物品。那一定也是我第一次听说“石头剪刀布蜥蜴史波克”的地方

```
Stay up-to-date with all of my content, get early access to my courses, and get hand-picked content from around the Python and Julia communities straight to your inbox every Friday by signing up for my weekly [Curious About Code newsletter](https://davidamos.dev/curious-about-code-newsletter/).
```
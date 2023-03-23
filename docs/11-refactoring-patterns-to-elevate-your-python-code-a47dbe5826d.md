# 提升 Python 代码的 11 种重构模式

> 原文：<https://betterprogramming.pub/11-refactoring-patterns-to-elevate-your-python-code-a47dbe5826d>

## 让我们扩展一下*重构*的定义，以包括新的功能

![](img/ad74dd9c51ac2e436bf871f141c702c6.png)

适当的重构降低了技术债务(图片由 [Ryland Dean](https://unsplash.com/@ryland_dean?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄)

# 不同类型的重构

我计划在 [Photonai](https://www.photon-ai.com/) 代码库中增加稳定性和性能，并降低维护成本。我在原始代码基础上添加了集群功能，并改变了架构。

一个小的代码重构任务通常是修复 bug。一些人认为如果错误修复发生在发布测试之前，那就不是重构。

一个重要的代码重构项目的例子是使一个程序符合 2000 年，但不改变功能。Y2K 就绪使代码能够在 2000 年 1 月 1 日或以后正确运行。(是的，这是一个东西！)

> “重构是一种重组现有代码体的训练有素的技术，改变其内部结构而不改变其外部行为。”— [马丁·福勒](https://martinfowler.com/books/refactoring.html)

嗯，也许这是正确的，但我在内部扩展了定义:

> “重构是一种训练有素的技术，用于重组现有的代码体，改变其内部结构，**这也可能导致其外部行为发生变化**。”—匿名

[](https://medium.com/continuousdelivery/refactoring-workflows-da982d08d2c7) [## 重构工作流

### 重构是一种改进现有代码库设计的受控技术。其实质是运用一种…

medium.com](https://medium.com/continuousdelivery/refactoring-workflows-da982d08d2c7) 

我重新定义了重构的定义，因为说“重构并添加代码项目”或者仅仅是“添加代码项目”是非常不酷的(更重要的是，上层管理层开始认为重构=好。)

我称之为“重构项目”我知道许多人可能认为我这样做是错误的。

重构的定义(被我)扩大到对现有代码基础的任何改变都是重构。如果您可以向应用程序、框架、包或代码集添加少量或大量的功能，那么您就是在重构代码集。

在本文中，当添加的功能本质上是重构方法的一部分时，将对其进行讨论。

# 1.删除不需要的注释

当您通读并理解代码时，您会遇到不再相关的注释和注释代码。删除它。您提高了可读性并减少了代码量。

你可能认为`# 'fab...` 评论应该保留。我决定删除它，因为它不是一般性的，而是对最初程序员(研究者)兴趣的一个注解。

# 2.将常数赋给变量

将常量放入变量中可以使它们位于一个位置。如果它们位于一个地方，那么更改它们的成本要低得多，维护代码集的成本也要低得多。还有其他原因，比如可读性更好。

在我看来，较低的维护成本是使用将常量赋给变量模式的主要驱动力。你可以和我争论，称之为一种模式。请将它放入你想要的任何分类方案中。这仍然是一个很好的重构实践。

在下面的示例中，代码片段显示了将常量赋值给变量重构。现在想象你将字符串`‘Classifcation'`、`'Regression'`和`'Clustering'`分配给`ELEMENT_TYPES`。然后你把`'Regression'`分散在整个代码中。

将常量赋值给变量的重构本身几乎没有降低维护成本。如果您在整个代码中将所有的`'Regression'`引用都改为`ELEMENT_TYPES[REGRID]`，那将是最好的。

**注意**:找出上例中的错误。请注意纠正这个错误是多么容易，而不是在分散在包中的多个位置进行纠正。

**注意**:错误确实影响行为。它通过了测试，但没有通过代码审查。

**注意** : CLSFID 和其他全局变量遵循 PEP-8 风格指南。你可以用 PEP-8 检查你所有的代码。你可以用黑色的[把你的代码重新格式化成 PEP-8 风格。不要误解我。还有其他工具用于 PEP-8 样式检查和重新格式化。我更喜欢黑色**。**](https://pypi.org/project/black/)

**注意**:我们缩短了许多全局名称，因为我们觉得它们太长了，会影响 80 或更少字符宽的屏幕的可读性。

# 3.修复 Bug 或添加新代码

我对 bug 修复的定义可以称为“由他人添加功能”。*如果是架构变更，我认为是 bug 修复。添加集群和所有相关的代码添加和更改会导致实质性的架构重构。*

*注意:我认为“架构重构”是这个活动更好的术语。*

# *4.修改字典中的关键字*

*我没有做一个更技术性的副标题，比如给字典名称-功能映射增加一个新的层次，主要是因为我认为这是一个足够窄的重构模式。*

*在重构之前修改 Photonai 的字典中用于聚类的键的一个例子是:*

*修改字典中的键并将常量分配给变量重构后:*

# *5.将重复代码转换成装饰器*

*挑战在于，通常没有多少重复的代码可以通过消除来重构。*

*我应该改变规则来重构重复的代码片段。*

*Python 对于在顶部或底部或两者都有重复模式的代码有一个很好的解决方案。解决方案是一个可以绕过差异中间代码的包装器，在 Python **中称为*装饰器*。***

*函数或方法是装饰器的内联代码参数。装饰器包装函数/方法(f)。通过包装 f，装饰器在 f 之前调用代码，然后调用 f，然后在 f 之后调用代码，然后返回结果 f。*

*Decorator(ed) 代码可读性更强，维护成本更低。*

*广泛使用的装饰者之一是`register_DataFrame_method()`。It decorator 函数和方法，使它们能够从 DataFrame 实例中被功能性地调用。你可以在创作者[扎克·赛勒](https://zsailer.github.io/software/pandas-flavor/) **写的博文《[写出你自己的熊猫味道](https://zsailer.github.io/software/pandas-flavor/)》中读到。***

*下面的代码片段详细描述了装饰者`register_DataFrame_method`:*

*[](https://www.pythonforthelab.com/blog/how-to-use-decorators-part-2/) [## 如何使用装饰者第 2 部分

### Decorators 是一种有益的编程模式，它允许以很少的代价改变函数的行为

www.pythonforthelab.com](https://www.pythonforthelab.com/blog/how-to-use-decorators-part-2/) 

你可能会问自己“装饰者`register_DataFrame_method()`与重构重复性代码片段重构活动有什么关系？”

我来回答一个问题。您会重复添加哪些代码来包装函数，以便它们从 DataFrame 实例中调用？

下面的例子展示了我们如何转换函数`statistic()` ，以便从 DataFrame 实例中调用它。* 

# *6.将 Python>3.7 类转换为 Python≤3.7 @dataclass*

*`@dataclass`在 Python 3.7 中新增。它的引入是为了消除使用 Python > 3.7 类导致的样板文件。`@dataclass`实现了一个用于参数初始化的`__init__()`方法、一个用于字符串表示的`__repr__()`方法和一个用于对象比较的`__eq__()`方法。所有与实例化、打印和比较数据类实例相关的样板文件都在`@dataclass`内部。*

*下面是一个使用新的`@dataclass`装饰器的例子:*

***注意**:任何复杂类型都必须包含`field(default_factory=dict)` ，如`list`或`dict`。*

*有关`@dataclass`的更多详情，请访问:*

*[](https://dr-bruce-cottman.medium.com/twelve-dataclass-examples-for-better-python-code-d1318f362d93) [## 更好的 Python 代码的 12 个@dataclass 示例

### 我们用 12 个前后代码示例展示了@dataclass 如何将 Python 代码转换成更好的 Python 代码。

dr-bruce-cottman.medium.com](https://dr-bruce-cottman.medium.com/twelve-dataclass-examples-for-better-python-code-d1318f362d93)* 

# *7.消除不必要的代码语句*

*我把消除不必要的语句放在这里，因为它在<insert language="">程序员中出现得太频繁了。当一个算法被编码和增量更新以通过测试时，它自然地出现在代码中。</insert>*

*如果你想产生干净、可读的代码，你需要删除不必要的代码和注释。在向他人展示或发布代码之前，仔细检查代码，删除不必要的语句。*

*在下面的代码中，删除第 1 行、第 2 行、第 3–6 行和第 8 行的注释。*

*由代码审查来捕捉这种不必要的代码反模式。*

# *8.代码提升*

*代码提升是消除不必要的代码语句模式的子模式。这是一种重构模式，因为它在代码集中经常出现。*

*当一个或多个语句出现在两个或多个条件分支上时，就会发生代码提升。那些多余的语句被从条件式中“吊”了出来。*

*在某些情况下，条件消除了一个或多个分支。通常，这将导致异常变得更加明确。*

*移除是微妙的，有潜在的危险。有些人认为不应该删除，因为这通常不会提高性能或可读性。(我们的编码标准规定了这一点，所以我们这样做是为了不在代码审查中失去印象分)。*

***注意**:代码提升导致代码行(LOC)复杂度指标降低。*

*下面是后一种代码提升的示例:*

***注**:我个人觉得这段代码经过重构后更容易理解。*

# *9.记录代码*

*总是可以有更多的 docstrings。最起码的是你需要理解的文档区域和你添加的任何代码。*

*一个服务不足的领域是隐藏方法，那些标有*邓德斯*(双下划线符号)的方法。`__init__`或`_xxxx`是缺乏关注的方法的典型代表。*

*下面是函数`toContinuousCategory`的文档示例以及最佳实践用法的研究笔记。(还有，选长例的例子。)*

*有许多文档字符串样式。我用的是[谷歌](http://google.github.io/styleguide/pyguide.html)风格。原始样式、Sphinx 以及 docstrings 示例将在下一篇文章中详细介绍。*

*[](https://medium.com/better-programming/the-guide-to-python-docstrings-3d40340e824b) [## 开始编写 Python 文档字符串

### 简化你的生活和任何试图阅读你的代码的人的生活

medium.com](https://medium.com/better-programming/the-guide-to-python-docstrings-3d40340e824b)* 

# *10.放入类型提示*

*我考虑加入类型提示重构，并且我写了一篇专门讨论它的文章:*

*[](/twenty-type-hinting-techniques-and-tools-for-better-python-code-e877e0b0c679) [## 更好的 Python 代码的 20 种类型提示技术和工具

### 让我们讨论一下为什么类型提示技术和工具可以改进您的 Python 代码

better 编程. pub](/twenty-type-hinting-techniques-and-tools-for-better-python-code-e877e0b0c679) 

我修改了 Photonai 代码，使其包含一致的类型提示*。** 

# *11.更新测试套件*

*我发现每个组织在发布测试之前都有自己的指导方针。我的指导方针是:*

*   *代码必须遵循 PEP-8 格式风格指南。*
*   *测试套件必须覆盖代码集的 85%以上。*
*   *对于任何更改的函数或方法，必须有一个或多个测试。*
*   *任何新的函数、方法类和数据类都必须有一个或多个测试。*
*   *测试套件必须是自动化的。*

# *摘要*

*重构的定义已经扩大到“对现有代码基础的任何改变都是重构。你可以向一个应用程序、框架、包或一些代码集添加少量或大量的功能，你正在重构那个代码集。*

*我们讨论了在 Photonai 包中使用的 11 种重构方法。重构 Photonai 包的目标是添加无监督聚类算法。*

*我希望这些关于重构的文章能对你有所帮助。*

*编码快乐！*

# *资源*

*[如何用 PEP 8 写出漂亮的 Python 代码](https://realpython.com/python-pep8/)*

*[](https://github.com/python-rope/rope/blob/master/docs/overview.rst#refactorings) [## 蟒蛇绳/绳子

### 这个文件的目的是给出 rope 的一些特性的概述。它是不完整的。还有一些功能…

github.com](https://github.com/python-rope/rope/blob/master/docs/overview.rst#refactorings)*
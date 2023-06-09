# 用任何编程语言更快编写代码的 5 个技巧

> 原文：<https://betterprogramming.pub/5-tips-to-write-code-faster-in-any-programming-language-cb3d09ba311b>

## 在保持工作质量的同时，将编码速度提高一倍

![](img/26f84e33c87fb647b399ce9c08303d1a.png)

如何快速编码，作者用 Canva 设计的封面。

每个与编程相关的任务都由几个一般阶段组成:理解、计划、编写代码、测试/调试和最终确定。程序员将大部分任务时间花在编码和调试上，因为他们在写代码时通常会做计划。因此，每个程序员都试图通过各种各样的生产率技巧来提高他们的编码速度。例如，一些程序员使用代码编辑器扩展来自动生成一些常用的代码片段。

这些提高效率的小技巧会教你如何加快重复做的事情。在大多数情况下，编写代码是一项动态工作。例如，你今天不会再写昨天写的代码——如果你写了，你可能需要检查设计模式原则。然而，如果我们需要提高编码速度，我们需要了解一些直接影响我们编码速度的事实。程序员可以在一个小时内完成一个特定的编码活动。与此同时，另一个程序员可能在五分钟内完成同样的工作，并保持同样的质量。

在这个故事中，我将解释一些通过保持工作质量来提高编码速度的技巧。你确实可以用任何编程语言、软件框架或库来实践这些技巧，从而比以前更快地完成编码工作。

# 如果可能，应用元编程

我们通常使用复杂的数据类型来存储数据集合。例如，如果我们需要存储和处理一个人的详细信息，我们不会传输和处理单独的变量，如姓名、年龄、国家等。相反，我们根据我们使用的编程语言创建一个类、键值对象或结构。假设您需要用一个名为 Person 的对象生成一个 SQL 查询。一些程序员可能倾向于通过键名访问每个底层变量，如下所示。

```
person.name, person.age, person.country
```

如果需要添加另一个 SQL 字段，就必须修改 SQL 查询生成器函数。当字段数量增加时，您可能需要花费大量时间来显式键入字段名称。如果您将 person 对象视为数据，并提取程序运行时生成 SQL 查询所需的所有信息(字段和值)，那么您可以快速编写高度动态的代码。这种技术被称为元编程，大多数流行的语言都通过反射 API 提供这种技术。

在显式使用特定结构的字段名之前，尝试用元编程的方式思考。当您处理数据序列化和转换时，元编程是一个非常节省时间的方法。然而，过度使用元编程会降低代码的可读性，也会降低程序的速度。因此，请谨慎使用。

# 第一次尝试编写准备提交的代码

大多数程序员倾向于先实现一个快速的解决方案。之后，他们经常做重构，让代码变得干净。此外，他们可能会进行快速重构，以提高代码的可重用性。同时，一些程序员总是在第一次尝试时就努力实现干净的、可重用的代码。如果你先实现一个快速的解决方案，你可以快速检查你的整个代码是否工作。

然而，如果你耐心地开始编写可重用的和准备提交的代码，你将会节省大量的重构时间。这个动作有点难练习，因为我们总是希望在工作的时候看到一些东西在工作。此外，我们可以先实现快速实现来验证技术可行性。

然而，如果您在第一次尝试时就练习实现可重用的、干净的、准备提交的代码，那么通过保持工作质量，您可以更快地完成每项编码任务。

# 学习最新的语言特性并小心使用它们

每种流行的编程语言都引入了新的工具链特性、语言改进和新版本的标准 API 更新。此外，编程语言可能不赞成现有的功能。编程语言的变化通常包括程序员可以用来加速编码过程的特性。例如，在 JavaScript 中使用普通字符串语法(单引号或双引号)处理大型多行字符串操作非常耗时，因为您需要添加许多字符串连接运算符。但是您可以使用新的 ES6 [模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)处理任何复杂的字符串操作。

同样，您最喜欢的编程语言的新特性可能包括一些您可以用来加快编码速度的特性。您可以通过阅读编程语言的版本历史、博客文章和开源代码库来了解这些新特性。此外，您最喜欢的编程语言的标准库可能已经有了一段您试图从头实现的代码。

但是，您需要仔细选择新的语言特性。首先，您需要检查您的生产环境是否支持您希望使用的特性。之后，您需要确保新特性不会影响代码的可读性。

# 尽量不要过度工程化

每个高级工程师都会激励团队中的所有初级工程师用更好的设计模式编写干净的代码。但是如果你试图把所有的设计模式都加入到你写的每一段代码中，你的源代码就变成了一个每个程序员都不敢碰的过度工程化的复杂代码。写代码有两个目的:只为编译器/解释器写代码而不使用任何设计模式，以及写过度工程化的代码以表明你知道所有的设计模式。毫无疑问，我们需要在这两端平衡我们的代码质量，以产生高质量和可读的代码。

我们可以用更少的设计模式轻松地重构代码。但是重构一个庞大的过度工程化的代码库是如此的困难。因此，试着用相关的设计模式实现干净的代码。这会节省你和你队友的时间。

此外，不要过度集成不需要的库。例如，无需为您的简单 React 应用程序集成第三方状态管理解决方案。

# 用你自己的方式练习快速操作键盘

以上提示都是要用心练习的东西。现在，我们要研究一个影响你编码速度的物理事实。除了逻辑值，你的源代码是一个用键盘输入的文本文档。所以，你的打字速度无疑对编码速度影响很大。但是，你不需要成为一个超快的百分之 [WPM](https://en.wikipedia.org/wiki/Words_per_minute) 打字员来更快地编码。程序员以不同的方式使用键盘。一些程序员将源代码输入为连续的字符序列。换句话说，他们按照正确的顺序逐个输入单词和特殊字符。与此同时，一些程序员练习他们自己高效的方式来输入代码。

此外，一些程序员在他们的编辑器中使用自动括号关闭扩展和人工智能支持的自动完成扩展。另一方面，一些程序员(比如我)喜欢用没有任何自动完成扩展的老式方法工作。当你写代码时，你必须找到一种舒适的操作键盘的方式。

使用箭头键而不是鼠标指针在代码行中导航是快速编码者的一个好标志。同样，当你键入代码时，发现你自己快速操作键盘的方法。
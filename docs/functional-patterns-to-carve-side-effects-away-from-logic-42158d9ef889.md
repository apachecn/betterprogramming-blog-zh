# 从逻辑中去除副作用的功能模式

> 原文：<https://betterprogramming.pub/functional-patterns-to-carve-side-effects-away-from-logic-42158d9ef889>

## 用函数式编程提高代码的可测试性

![](img/bbeab94c7faad8882046e239f4bb50e8.png)

照片由[德鲁·比默](https://unsplash.com/@drew_beamer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/split?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

你可能听说过单子，效果系统，或最终无标签，并反弹到上一页，认为这只是学术废话，几乎没有实用性。

公平地说，它们当然属于学习曲线更陡的概念，但它们仍然广泛适用。

所有这些背后都有一个共同的、非常简单的想法。这是贯穿函数式编程的一个反复出现的主题，尤其是在纯函数式编程语言中，如 [Haskell](https://www.haskell.org/) 。

关键的概念是把你的程序分开，一边是逻辑，另一边是带有副作用的执行。

由于 JavaScript 已经成为编程的通用语言，我想保持这种语言不可知，所以我们将通过一些例子来了解它。

# 使用依赖注入

在面向对象的世界中，可以用来消除某些代码副作用的一种广泛使用的技术是[依赖注入](https://www.freecodecamp.org/news/a-quick-intro-to-dependency-injection-what-it-is-and-when-to-use-it-7578c84fa88f/)。

所有有副作用的代码都可以替换为对对象的函数或方法的调用，这些调用作为参数接收或在初始化时传递。

它相当简单易懂，应该比后来的技术更受欢迎。虽然没有它们强大，但是依赖注入消耗了更少的花哨点，并且有助于控制复杂性。

dependencyInjection.js

# 将程序编码为一个值

一旦程序被编码成一个值，事情就变得有趣了。的确，价值观是可以分析和转化的。

它打开了优化的大门，自动生成文档，或者在不直接修改程序的情况下为程序添加功能。例如，您可以向程序添加访问控制，同时让程序完全不知道它的存在。

应用程序代码因此是纯净的，因为它只返回一个没有任何副作用的值。解释器的作用是执行程序。我们本质上将代码分成两个(或更多)部分:

*   一些将有效程序的描述作为一个值返回的纯代码。
*   可选地，一些函数转换程序。
*   运行编码为值的程序的解释程序。

# 顺序程序

下面是一个简单的例子，用户试图猜测一个数字。该程序由更简单的程序(`print`、`read`和`pure`)组成，这些程序被编码为数值。

它们中的每一个都是带有一个`action`的对象，一些特定于该动作的值以及在这个动作之后要采取的`next`动作。注意`next`是一个函数，它接受当前动作的结果并返回一个新程序。

我们也可以定义这些程序的操作。例如，`bind`获取一个程序和一个函数，该函数获取其结果并返回一个新的程序，然后将这两个程序组合成第三个程序。

`bind`遍历动作链，并通过调用给定的函数替换最后一个动作(a `pure`)。注意递归发生在`next`内部，这意味着它直到被调用才会发生，也就是说`bind`是懒惰的。

sequentialActions.js

由于其顺序性，下一步只有在执行完当前步骤后才能知道。

例如，根据用户输入，程序可以分支成不同的子程序。这限制了我们用这些价值观做什么。

事实上，在解释当前动作并将结果反馈给它的`next`函数之前，我们无法分析下一个动作。这并不意味着我们不能用它做有趣的事情，我们将会看到更多。

# 非顺序程序

如果我们不需要一个接一个地执行动作，我们可以用一种仍然允许对程序进行静态分析的方式对程序进行编码，这种分析是在不运行代码的情况下进行的。

下面是一个 JSON 解析器的简单例子。和以前一样，有一个名为`pure`的基本程序，它只是返回一个常数。不同的是功能`apply`而不是`bind`。

`apply`接受两个程序——前者返回一个函数，后者返回一个值——并返回一个运行这两个程序并将值应用于函数的新程序。

通过以这种方式组合程序，我们无需运行任何东西就可以访问子程序。

nonSequentialActions.js

和前面的例子一样，有很多样板文件，用起来不太实用。当然还有改进的空间，但目标只是将程序的基本思想作为价值观来传达。

# 测试

这种结构的一个用例是使测试更容易。唯一需要的是另一个没有副作用的解释器。

testing.js

# 生成文档

在非顺序程序的情况下，我们不必执行代码来分析它。这意味着我们可以做一些有趣的事情，比如自动生成文档。

以我们之前的解析示例为例，我们可以为解析器生成 JSON 对象形式的文档。对于我们的例子，它将返回`{ "database": { "port": "number", "username": "string" } }`。

生成文档. js

# 与概念的关系

在函数世界中，在顺序程序的情况下，将使用*单子*——更具体地说是*自由单子*。

这非常接近我们的小游戏所做的，尽管 Haskell 中的 [free](https://hackage.haskell.org/package/free) 等库提供的抽象会减轻许多样板文件。

对于像解析器这样的非顺序程序，一个非常类似的抽象是*应用函子*(或者仅仅是*应用*)和它的表亲*自由应用*。

最后，依赖注入更接近于*最终无标签*方法。

# 结论

考虑到样板文件的数量和它们引入的复杂性，这里的示例在大多数实际情况下几乎没有实际用途，但是希望它们在不太混乱的情况下展示了这些构造背后的关键思想。

将程序的定义从它们的解释中分离出来，允许我们在中间添加步骤，在解释之前转换程序。我们也可以有不同的解释器用于测试或静态分析(比如生成文档)。

像 Haskell 这样的语言有语法糖和操作符，例如，利用单子和自由应用程序，非常方便(事实上，无处不在)。)

无论你使用哪种语言，总有一种比这里介绍的更方便的方式来编码程序。

如果你喜欢这样的故事，可以考虑成为媒体会员或者订阅。

# 进一步阅读

*   [依赖注入的快速介绍](https://www.freecodecamp.org/news/a-quick-intro-to-dependency-injection-what-it-is-and-when-to-use-it-7578c84fa88f/)，Bhavya Karia 在 freeCodeCamp 上。
*   [Javascript 中的单子](https://curiosity-driven.org/monads-in-javascript)，好奇心驱动。
*   Haskell 库[免费](https://hackage.haskell.org/package/free)，Hackage。
*   [GitHub gist 上这篇文章的代码](https://gist.github.com/thoferon/a4e1ee37fc85c6237d3c4cf3826b7390)。
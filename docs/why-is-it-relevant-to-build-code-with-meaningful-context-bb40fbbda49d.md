# 如何重构你的代码得到一个更干净的解决方案？

> 原文：<https://betterprogramming.pub/why-is-it-relevant-to-build-code-with-meaningful-context-bb40fbbda49d>

## 一个关于有意义代码的小 Scala 教程

![](img/803c3db5e3c9013ed069de969a453ad0.png)

信用:[https://unsplash.com/@nate_dumlao](https://unsplash.com/@nate_dumlao)|图像长度减少

在一次采访中，美国作家大卫·g·麦卡洛说:

> “写作就是思考。写好就是想清楚。所以才这么难。”

虽然 McCullough 谈论的是书面文本而不是代码，但他的说法非常符合编程的上下文。毕竟，软件的质量是由它阅读、理解、维护和扩展的难易程度来评估的。事实上，降低复杂性并将代码精炼到最纯粹的基础既是一种挑战，也是一门艺术。

# **简介**

在本文中，我将给出一个例子，说明如何重构一个小的示例 Scala 代码来包含上下文信息。从本质上讲，添加上下文信息意味着以一种人类可以毫不费力地阅读程序的方式来利用类、函数和名称空间。

在文章的最后，你可以在 GitHub 上找到完整的代码。

# **添加上下文含义**

看看下面的方法。事实上，方法名给了你一些关于程序做什么的想法。然而，信息是不完整的。因此，你必须在头脑中一步一步地执行算法，才能理解它实际上是做什么的。

此外，定义的变量`amount, animalName`和`pluralAdjuster`是返回消息的一部分。我们再次明白，只有通过我们头脑中的程序。

# **注意事项**

现在您已经看到了示例，请与它互动，并问自己以下问题:

*   如果我把输入`"Cat", 1`或`"Cow", 5`或`"Dragon", 0`给`printAnimalStatistics`，会发生什么？
*   在不运行程序和显式检查输出的情况下，我能有多容易说出答案？
*   我能改进什么？建议:在进入解决方案之前，尝试改进代码。您可以使用在线 [sbt 工具 Scastie](https://scastie.scala-lang.org/) 进行快速试用。

# **解决方案**

现在让我们重构代码。这个函数应该被分解成更小的函数。此外，函数和变量应该放在`Animal`类中。代码如下:

现在，`amount,` `animalName`和`pluralAdjuster`显然是`Animal`类的一部分，因此它们的上下文很明显。在`Animal`类中有五个小函数。每项职能都有明确的范围和目的。
现在，再问自己之前的问题。你是否更容易理解新的解决方案？

# **结论**

在本文中，您看到了一个 Scala 代码方法转化为一个类和多个更小的函数。包含上下文信息提高了代码的可读性。此外，本文揭示了关注代码清晰性的重要性。完整的代码可以在[这里](https://github.com/DataBach-maker/ScalaExamples)找到。

如果您对如何改进上面的示例有任何进一步的建议，请随时发表评论。在通往精通的道路上总有东西要学:)。

https://scastie.scala-lang.org/
[https://github.com/DataBach-maker/ScalaExamples](https://github.com/DataBach-maker/ScalaExamples)采访 NEH 董事长 Bruce Cole
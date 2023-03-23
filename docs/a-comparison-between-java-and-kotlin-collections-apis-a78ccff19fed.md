# Java 和 Kotlin 集合 API 的比较

> 原文：<https://betterprogramming.pub/a-comparison-between-java-and-kotlin-collections-apis-a78ccff19fed>

## 通过一些常见的用例示例解释相似性和差异

![](img/14a069e8df199875ccbdec5e5d2d44e5.png)

照片由[莎伦·麦卡琴](https://unsplash.com/@sharonmccutcheon?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 前提

我们将使用两个实体来比较使用 Java 和 Kotlin 中的集合编写代码。我们的目标是看到以下内容:

*   如何在各种情况下有效地使用集合
*   Kotlin 和 Java 在代码行数和易理解性方面的比较

我们将使用两个名为`Actor`和`Episode`的实体。实体只是为了演示。你可以用任何东西，像`Library`、`Book`、`User`、`Employee`、`Bank`、`Money`等等。集合 API 使得使用`List`、`HashMap`和`Set`变得相当简单，而使用 Java 8 中引入的 Streams API，迭代集合就简单多了。有几个操作(称为中间操作和终端操作)可以应用于集合，而不需要实际改变它们。

这提供了一个很好的抽象和对并发的更好的控制。Kotlin 更进了一步，在使用流时抽象了大部分样板文件。语法要简单得多，它以同样的速度做同样的事情。通过几个例子，您将了解如何在 Java 中使用流以及在 Kotlin 中使用类似的实现。

让我们先初始化数据。

# 收集所有电子邮件

从演员列表中，我们正在收集单个演员的电子邮件。注意对`getEmail`的调用。它使用`**::**`操作符作为 lambdas 调用特定方法的简写。

## Java 语言(一种计算机语言，尤用于创建网站)

我们首先将 actor 转换为 stream，并将其映射为只是发出电子邮件，然后最后，我们将它收集回一个列表。

## 科特林

如前所述，Kotlin 已经抽象出了许多样板代码。它负责将列表转换成流，并将结果自动收集回列表。

# 用逗号连接电子邮件

## Java 语言(一种计算机语言，尤用于创建网站)

如果我们想要合并所有由逗号分隔的演员的电子邮件，我们可以使用`Collectors`接口通过提供分隔符来连接单独的电子邮件。一个公共接口`Collector`是

> 一个[可变归约操作](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Reduction)，它将输入元素累积到一个可变结果容器中，在处理完所有输入元素后，可选地将累积的结果转换为最终表示。缩减操作可以顺序执行，也可以并行执行。—甲骨文[文档](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collector.html)

## 科特林

对于 Kotlin 来说，这个也很简单，只需要一个对`joinToString`的函数调用。剩下的就交给科特林了。

# 合计工资

在计算总和、平均值、最大值、最小值等时，经常需要聚合函数。在这种情况下，我们试图得到所有演员的工资总和。

## Java 语言(一种计算机语言，尤用于创建网站)

Java 中的`Collector`接口有一个内置的函数，我们可以用它来获得我们案例中的工资总额。

## 科特林

同样，通过调用`sumBy`，这个实现非常简洁，它为我们完成了工作。

# 按导演获取剧集

在这种情况下，我们希望按导演对所有剧集进行分组。这会给我们一个`Map<String, List<Episodes>>`。传统上，如果我们要做这样的事情，我们会创建一个循环来迭代所有的剧集，并通过拥有与每个导演相关联的剧集列表来将条目放入地图中。

## 带循环的 Java

## Java 流

对于流，这变成只有两行代码，做完全相同的事情。这里重要的一点是，我们不必自己改变`map`和`list`。代码变得更加健壮。

## 科特林

Kotlin 像往常一样更进一步，允许我们在一行中做同样的事情。

# 按性别取名字

现在让我们试着根据性别划分我们的演员，并得到演员的名字。我们实际上想要的是得到两个名字列表，一个包含男人的名字，另一个包含女人的名字。

## Java 语言(一种计算机语言，尤用于创建网站)

这在 Java 中相当棘手。我们首先按性别分组以获得性别图和演员列表，然后我们通过应用映射函数只获取演员的名字。这里我们使用的是`Collector`的`groupingBy` 和`mapping` 函数。

如果我们想获得按性别分组的演员列表，而不是演员的名字，那么我们可以使用`identity`函数来获得完整的对象，就像这样。

## 科特林

不用说，Kotlin 用更少的代码行做了同样的事情。这里的主要区别是，我们可以直接将映射应用于我们创建的映射的值。

# 第一季剧集总数

这个相当简单。如果我们想得到第一季的集数，我们可以简单地按第一季过滤集，然后调用内置的计数函数。令人惊讶的是，对于 Kotlin 和 Java 来说，这一点非常相似。

## Java 语言(一种计算机语言，尤用于创建网站)

## 科特林

下面你会找到 Java 和 Kotlin 的完整代码。

# Java 的完整代码

# 科特林的完整代码

# 离别的思绪

我希望这让您对使用 Kotlin 和 Java 的集合框架有了更好的理解。我们看到了几个日常用例，也看到了 Kotlin 和 Java 之间的框架比较。可能还有几个其他的用例，如果您知道的话，请随意评论这篇文章。

如果你有兴趣更深入地理解 Java 中的 Streams API，我推荐你访问这个[站点](https://chermehdi.com/posts/java-streams-part-1/)。此外，如果你想了解 Kotlin 集合的其他内置操作，你可以在这里找到它们。

感谢阅读。
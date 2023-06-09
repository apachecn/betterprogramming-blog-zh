# 使用适配器模式保护应用程序免受外部依赖的影响

> 原文：<https://betterprogramming.pub/using-the-adapter-pattern-to-secure-apps-from-external-dependency-changes-72d0f4c9d961>

## 如何在不影响应用程序核心的情况下动态地改变外部依赖关系？

![](img/dc14b7ce418b803a181eb45e44254255.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

让我向您介绍一下适配器模式，或者如果您已经熟悉的话，让您重温一下。

工程师可以使用的设计模式有一长串，但是很难知道何时何地使用每一种。如果使用得当，它们是解决已经解决的问题的有力工具。在本文中，我们将看一个真实世界的例子，说明如何使用适配器模式来改进代码，并保护代码免受外部依赖关系变化的影响。

# 从一个例子开始

我已经简化了这个例子，但是它来自一个真实的应用程序。在 Ruby 应用程序中，我们有以下类:

这个类用于检索我们保存的关于邮政编码的附加信息。我们收集客户的邮政编码，并丰富应用程序中使用的数据。

这个类有一个硬编码的依赖项`Zipcodes`。这个类表示 Mongo 集合中保存的数据，如下所示:

当首次部署应用程序时，数据从保存在应用程序存储库中的 CSV 文件中加载。在部署过程中会检测到对 CSV 文件的任何更改，我们存储在 Mongo 中的数据会根据最新的邮政编码信息进行更新。数据保存在 Mongo 中有两个主要原因:

1.  得益于索引，响应时间得到了极大的改善，相比之下，每次加载和查找文件中的记录会很慢。
2.  **内存使用**大大减少，因为我们可以在内存中缓存文件，这将加快查询速度，但代价是使用大量内存。

为了帮助形象化问题，下面是 CSV 的样子:

```
zipcode,population,weather_risk
35004,123444,A
99501,84378,D
85001,9128,F
71601,47382,B
```

# 问题是

应用程序最初实际上在内存中缓存文件，但是随着文件数量的增加(由于供应商、产品和国家的增加),这不再是一个可行的解决方案。因此，决定在 Mongo 中存储(作为题外话，我认为对于这些数据，有比 Mongo 更好的存储选项，比如 Redis)。

这种改变本身很容易。然而，由于我们每次运行测试套件时都必须将数据加载到 Mongo 中，这给测试套件的执行时间增加了几分钟。

我们尝试了只导入数据子集的想法，这确实减少了停机时间，但是它给测试套件带来了不稳定性，万一测试中的邮政编码改变了，或者用不同的邮政编码添加了新的测试。

我们可以强制测试套件中只允许某些邮政编码，但是这使得测试变得很麻烦，特别是因为非工程师在系统中构建风险模型(使用 DSL)，并且严重依赖于小黄瓜特性来验证他们的模型。

总结一下问题:出于性能原因，我们需要在生产环境中将数据存储在 Mongo 中，但是在测试套件中，我们仅仅在预加载数据上就浪费了 5 分钟。

# 我们如何改进这段代码？

当应用程序的行为需要可配置时，尤其是在运行时，这通常是利用适配器模式的好机会。

简而言之，适配器模式允许您在两个不兼容的接口之间进行转换。一个非常简单的例子是当系统需要 JSON 作为输入，但是外部依赖提供了 XML。在这种情况下，您可以有一个从 XML 转换到 JSON 的适配器。

在我们的用例中，我们可以创建两个适配器——一个从 CSV 文件翻译，一个从 Mongo 翻译——并在运行时将正确的适配器注入到`RetrieveZipcodeInformation`类中。

## 创建适配器

首先，我们必须创建两个独立的适配器:

它们非常简单，数据库适配器只需从`RetrieveZipcodeInformation`类中复制逻辑，内存适配器在类被实例化时读取 CSV 文件，并存储为类变量。

在现实世界的应用程序中，CSV 文件不是硬编码的，CSV 缓存实际上是一个文件数组，我只是为了使本文的代码更简洁而进行了简化。

创建了适配器后，我们现在可以在`RetrieveZipcodeInformation`类中使用依赖注入来配置适配器:

在现实世界的应用程序中，我们在应用程序启动时创建一次适配器，每当我们使用`RetrieveZipcodeInformation`类时，它就被注入。创建的适配器的类型是基于环境决定的(例如，测试使用内存适配器，其他的都使用数据库适配器)。

# 确保适配器的接口一致

然而，我们的实施存在一个问题。适配器应该提供一致的接口，但是它们返回的实际上是不同的。

在数据库适配器的例子中，它从数据库中返回一个去序列化的行，该行公开了从字段中检索数据的方法(例如`row.field`)。)相反，内存中的适配器从 CSV 返回一行，但采用字典格式，因此使用键(例如`row['field'])`)访问数据。请记住，Ruby 没有显式接口，所以在返回不同的响应时不会有类型错误。

这种方法有两个主要问题:

1.  任何使用适配器的代码都必须处理不同的响应，这将是混乱的，并且容易在测试和非测试环境之间产生错误/差异。
2.  我们公开了外部依赖的细节，而使用适配器的类不关心——或者不需要知道——数据来自哪里。它所关心的是以合理、一致的格式接收数据。

为了解决这个问题，我们必须首先定义一个从适配器返回的类。我选择创建一个简单的[值对象](https://martinfowler.com/bliki/ValueObject.html):

我们现在可以更新适配器以返回该类的一个实例:

任何使用适配器的类现在都可以安全地依赖于一致的响应，并且作为一个额外的好处，当我们使用自己的类时，理解返回什么要容易得多，而不是依赖于从我们无法控制的库返回的那些。

# 最后的想法

这非常有效，解决了我们之前提到的问题。生产完全按照以前的方式运行，并且由于内存缓存和没有预加载数据，我们的测试恢复得像闪电一样快。

我们通过添加测试，特别是确保在非测试环境中不使用内存适配器的测试，完成了这一更改。

我没有详细讨论它，但是如果你不熟悉依赖注入，我推荐你阅读一下[依赖倒置](https://en.wikipedia.org/wiki/Dependency_inversion_principle)，因为这是使用这种模式的另一个好处。

最后，在这种情况下，我们有一个需要注入不同适配器的场景。即使您只与单个外部依赖项接口，并且不需要切换适配器，我也建议使用该模式来保护您的应用程序免受外部依赖项变化的影响。例如，假设您正在调用一个 API。该 API 可能会改变，也许字段名会改变。如果您有一个适配器，则更改仅限于该适配器，而不必在整个应用程序中重命名该字段。

```
**Want to Connect?**I run a free newsletter providing fortnightly technical book recommendations, including my key takeaways from the books. Interested? [Sign up here!](https://subscribe.technicalbookclub.com/?utm_source=medium&utm_medium=article&utm_campaign=adapterpattern)
```
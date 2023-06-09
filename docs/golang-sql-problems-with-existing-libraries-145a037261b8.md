# Go 和 SQL:现有库的缺陷(以及更好的解决方案)

> 原文：<https://betterprogramming.pub/golang-sql-problems-with-existing-libraries-145a037261b8>

## 见见 KSQL——我为解决 Golang 和 SQL 问题而编写的库

![](img/3e1a5029ccad97bf79e1bc82f0931f15.png)

照片由 [Chinmay Bhattar](https://unsplash.com/@geekgunda?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在我使用 Golang 的这些年中，我得出的结论是，使用 SQL 还不是一个很好解决的问题。我们可用的库只是不容易使用，并且有太多的方式搬起石头砸自己的脚。

为了说明这一点，我将在本文中详细描述所有这些问题，然后简要介绍我提出的解决这些问题的解决方案:我编写的名为`[KSQL](https://github.com/VinGarcia/ksql)`的库。

在接下来的章节中，我们将讨论 Golang 生态系统中三个被广泛采用的库:

*   标准库:`database/sql`
*   一个标准库的包装器，增加了一些很酷的特性，比如结构扫描:`sqlx`
*   Postgres 的专用驱动程序，比其他驱动程序更快，可以与`database/sql`或`sqlx` : `pgx`一起使用

# 问题 1:为单个查询检查错误 4 次

当编写从数据库中读取多行的查询时，这个问题最明显。

这样做时，我们实际上必须使用`database/sql`或`sqlx`测试 4 次错误，使用`pgx`测试 3 次错误。

可怕的是，大多数人甚至不知道这一点，通常只测试对`Query()`的调用是否成功，以及对`.Scan()`的每个调用是否成功。

因此，如果我们真的检查所有这些错误，我们的代码最终会是这样的:

> 注意，当使用 pgx 时，`.Close()`函数实际上并不返回错误，所以您必须测试它 3 次，而不是 4 次。

如果你曾经使用过这些库中的任何一个，我敢打赌你不会测试所有这 4 个错误，而且通常都没问题。

但是我仍然希望库不要强迫我们在非常冗长的错误检查和忽略很少发生的错误之间做出决定。

# 问题 2:忘记打电话`defer rows.Close()`

所以如上面的例子所示，我们必须调用`defer rows.Close()`。如果我们不调用它，我们会以一个非常严重的错误结束，我们的连接将永远不会被释放，使得我们的代码在执行一段时间后最终停止，没有错误消息。

更糟糕的是，如果您在整个系统中的单个查询上这样做，它仍然可能通过您所有的集成测试，甚至您的代码审查，因为不太容易发现丢失的行，这可能会导致生产中的问题。

# 问题 3:调用延迟行。在错误的时间关闭()

即使你永远不会忘记调用`rows.Close()`,仍然有另一个问题很容易在代码审查和集成测试中被忽视。下面的代码示例说明了这个问题:

在最后一个例子中，许多读者可能没有发现这个问题，如果你发现了，我敢打赌你团队中的某个人可能没有发现。

如果您运行此代码，它将在大多数情况下在生产中工作，但有时会死机。换句话说，这是最难调试的问题之一:它只是偶尔发生。那边发生了什么事？

问题出在我们调用`rows.Close()`的地方，即在检查错误之前，而不是之后，所以如果对`db.Query(...)`的调用返回一个错误，那么`rows`变量将是`nil`，这意味着对`defer rows.Close()`的调用将试图调用一个关于`nil`值的方法，当延迟被执行时，它将在函数结束时死机。

同样，使这个问题特别令人担忧的是，它可以轻易地通过代码审查和许多类型的集成测试。

# 问题 4:大结构的代码重复

这可能是一个非常熟悉的情况:我们有一个 struct，比如说一个`User` struct，我们需要在查询中列出这个 struct 的所有属性，并且在每次我们做任何读取用户的查询时都要在传递给`.Scan()`的参数中列出。

为了说明这一点，假设这些查询之一如下所示:

在这个小例子中，情况看起来还不算太糟，但是如果你的结构有超过 50 个属性，情况就会变得非常糟糕。更糟糕的是，如果我们让 5 到 7 个查询扫描每个结构。

我希望我能够描绘出这个问题在大型代码库中会变得多么糟糕。

> 但是在继续之前:重要的是要提到，当使用`sqlx`时，你并不被迫使用`.Scan()`函数，你可以使用`.StructScan()`函数来代替，所以这个例子将会改进 50%。
> 
> 还有一个很棒的库也有一个叫做 [Scany](https://github.com/georgysavva/scany) 的`StructScan()`特性。它可以用在`database/sql`和`pgx`之上，用`StructScan()`特性扩展这两个库。
> 
> 但还是。这两种解决方案仍然需要您在每个查询中列出结构的所有属性名称，因此您并没有真正消除所有的代码重复。

因此，如果上面提到的解决方案都不使用，那么这个问题的主要后果是:

1.  查询的`SELECT`部分的属性顺序和传递给`.Scan()`的属性顺序之间存在非常紧密的耦合。因为如果这两个列表的顺序不匹配，您可能会得到意外的扫描错误或更糟的情况:一个无声的错误，您加载了不正确的值的变量，但程序继续运行。这是一个特别糟糕的问题，因为它很容易逃过代码审查过程，所以建议总是进行集成测试。
2.  从结构中添加或删除属性可能会迫使您在所有查询中更新所有这些值，由于上面提到的排序问题，这是一个微妙的操作。
3.  还有一个可读性的小问题:当您试图在代码中找到特定的逻辑片段时，包含琐碎信息的大块代码没有帮助。

# 那么有没有可能有一个更好的 SQL 库呢？

是的，它是。我写了这个非常酷的库，叫做 [KSQL](http://github.com/VinGarcia/ksql) 。我相信它对上述问题有很好的解决方案。

因为解释原因和 KSQL 的所有特性不是这篇文章的目的，所以我会尽量简短，只举一个例子来说明 KSQL 是如何使处理这些问题变得更容易，并且不会增加太多的复杂性。

下面的简单示例将查询一个结构片中的几个值，它将使用 struct 标记来:

1.  为您创建查询的`SELECT`部分(不要担心，这是一个可选特性，它被缓存起来，因此非常高效，[参见我们的比较基准](https://github.com/VinGarcia/ksql#benchmark-comparison))。
2.  映射哪一列应该被扫描到结构的哪一个属性中(同样，反射部分只做一次并被缓存，所以效率很高，[参见我们的比较基准](https://github.com/VinGarcia/ksql#benchmark-comparison))。

好了，现在你可能会看到，这已经比我们以前使用的任何其他库都要简单得多，但是让我们更详细地了解一下之前提到的每个问题:

## **问题 1 的解决方案:过多的错误处理**

KSQL API 的设计理念是在所有操作完成后才返回，这意味着它只返回一个错误，确保在内部检查所有可能的错误来源，包括对`rows.Err()`、`.Scan()`和`.Close()`的调用。

## **问题 2 和问题 3 的解决方法:调用**

没有必要给`rows.Close()`打电话。

正是因为函数只有在完成整个过程后才返回，所以我们可以在内部调用它，让用户不必处理这种额外的复杂性。

## **问题 4 的解决方案:属性名不必要的重复**

为了消除名称重复的问题，KSQL 有两个特性:

1.  它将有选择地为您创建查询的`SELECT`部分，只有当您的查询以`FROM`开始时，才会触发此行为，因此，如果您想要在查询的`SELECT`部分中使用更复杂的表达式，只需编写它，它就会像预期的那样工作。
2.  它会根据标记中的名称将查询中返回的任何列自动映射到您的结构中。

如果你知道处理 SQL 有多复杂，你可能仍然会有所怀疑。

例如，如果我们加载的用户超过了内存容量，会发生什么情况？

或者，如果我们为了获得这些抽象而损失了大量的效率呢？

KSQL 确实解决了这些问题，在我有一篇更详细地解释 KSQL 设计决策的文章之前，我邀请您阅读 [KSQL 自述文件](https://github.com/VinGarcia/ksql#readme)。

# 结论

KSQL 是一个新概念，所以对它的特性、效率、测试覆盖率和稳定性持怀疑态度是一件好事(并不是说我没有解决这些问题)，但它确实解决了一些我们在一些最常用的 SQL Go 库上看到的严重问题。

同样值得注意的是，KSQL 不是 ORM，这些抽象保持简单和有限，正是为了避免常见的 ORM 陷阱，这只是对库如`sqlx`和`pgx`的明智替代，尽可能减少查询构建和效率损失。

在[我们的基准](https://github.com/VinGarcia/ksql#benchmark-comparison)中，很容易看到我们在实践中的表现与这些其他库相同，我的意思是基准值在每次运行时变化很大，所以很难精确，但我推测 KSQL 比 Postgres 上的`pgx`和所有其他数据库上的`sqlx`慢 1%。
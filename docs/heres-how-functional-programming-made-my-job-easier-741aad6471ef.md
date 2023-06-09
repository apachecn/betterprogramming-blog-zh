# 下面是函数式编程如何让我的工作变得更容易

> 原文：<https://betterprogramming.pub/heres-how-functional-programming-made-my-job-easier-741aad6471ef>

## 利用编译器的强大功能编写您的应用程序

![](img/db7d834477ea7a35049006e9ad2d7629.png)

内森·赖利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

“我们还剩十分钟。你对我们有什么问题吗？”在我们的技术面试环节结束时，我问一位受访者。“是的，我知道，”他回答说。“我看了你们公司的博客，发现你们用 Scala 而不是其他语言写代码。这是什么原因？”

我想了想，然后说，“我们使用 Scala，因为 Scala 具有我们扩展业务所需的函数式编程特性。”

他等了一会儿，思考着自己的想法。那一瞬间，我觉得我没有讲清楚函数式编程是我们团队一个巨大的实践。然后，他问道，“函数式编程有什么好处？”

这一次，我在回答之前等待了一会儿，在脑子里仔细思考了一下，然后给出了一个一般性的解释，即函数式编程(FP)的特性，如不变性和无副作用，可以帮助我们在并发环境中更好地推理代码。然后，我解释了 FP 的基本术语。然而，在那段时间里，也就是一年前，我不明白以函数式风格编写代码意味着什么，以及函数式编写代码如何帮助我的工作更有成效。

更多的开发人员使用明显偏向于 FP 的语言，比如 Scala 和 Haskell。相比之下，OOP 语言及其社区适应了这些特性和实践(想想 Javascript 社区中的 React 和 Typescript)。然而，当我第一次编写函数式代码时，将自己限制在代码的不可变性上既不能提高我生产高质量代码的效率，也不能创建一个可读的代码库(想想需要做一个递归函数来遍历元素列表)。此外，开发人员在项目期限内很难学习所有的 FP 抽象特性。

不变性和副作用是我在学习 FP 时接触到的第一件事。然而，这不是团队采用 FP 的真正原因。

当我开始走 FP 路线时，这是我的观点，但是现在我明白了为什么他们对开发者强制这些特性——为了本地推理和合成。具有讽刺意味的是，编写 FP 代码让我更有效率，让我的工作更容易，而语言特性和编程风格正好服务于这些目标。在这篇文章中，我试图解释为什么编写函数代码会让你的工作更容易。

# 明确处理所有的异常

尽管静态类型增加生产率是有争议的，但拥有类型安全是减少运行时错误和意外的最好方法之一。直到最近我试图调试我同事的一个代码库时，我才明白类型安全有多大的帮助。他问我，他是如何在一个微服务中不断获得运行时异常的，比如空指针。我们的服务是用 Scala 编写的。因此，获得运行时异常不像用 Java 写代码那样常见。

它有各种开发人员需要处理的错误类型。我多次扫描整个代码库，运行相同的失败测试用例，并跟踪 bug。三个小时后，我意识到他在获取 DynamoDB 中的一个 AttributeValue 时没有处理任何异常。

这一个案件耗费了团队三个小时的资源。当我们将 Java 代码转换成 Scala 时，经常会遇到这个错误。您看不到函数定义的返回值可能返回 null，开发人员需要不断提醒自己检查 null 情况。一种类型安全的编写方式是返回一个`Option`。在返回函数中声明一个`Option`类型向调用者表明返回值可能是空的。因此，调用者需要在编译时处理任何空的情况，避免任何意外，比如运行时的`NullPointerException`。

让我们举一个从数据库中获取条目的例子，然后解析数据库中的底层条目。

一个有经验的开发人员会马上知道至少有两种类型的错误:网络错误和解析错误。当数据库关闭时可能会发生网络错误，当模型不匹配时可能会发生解析异常。然而，这些错误对调用者是隐藏的——我们需要研究它的实现并“猜测”它有什么样的错误。

这种场景中的错误处理通常是一个`try`和`catch`错误，我们需要考虑在`catch`块中可能出现什么样的错误。

不需要处理所有的错误。因此，你看不到上面的函数有多复杂，可能会错过重要的错误，这些错误会在晚上使你的程序崩溃。

这方面的一个顿悟时刻是，异常是一种隐藏的复杂性。我们可以明确地将这两个异常作为“效果”告诉调用者让我们看看下面的代码片段会是什么样子:

我不会深入到实现中，因为我们可以通过查看函数定义本身来确定这个函数中会发生什么错误。第一个外面的值是`IO`，表示这个函数会有一些副作用。使用`IO`可以提醒调用者该函数可能会返回一个错误，这是一个网络错误。其次，我们有一个我们想要的`Either`或`ParsingError`或`Json`值。`Either`是 Scala 或其他函数式语言中的数据类型，表示结果可以是`Left`或`Right`。按照惯例，`Left`是错误情况，`Right`是调用者的结果。

你看，现在，呼叫者“被迫”处理这些场景。不处理`Left`箱，他们无法检索`Json`。他们还将知道在“编译”期间发生了什么类型的错误，以便根据他们的应用程序逻辑来处理这些错误。

更重要的是，类型安全有助于你处理棘手的情况。它让一个简单的函数变得更加复杂，因为它让一切都显而易见，而不是隐藏起来。

# 在编译时推理我们程序的流程逻辑

你是否曾经希望不用编译就能一次写完你的程序，并且当你编译你的程序时一切都正常？编写功能代码可以帮助你做到这一点。

编写函数式代码的罪恶之一是会产生副作用。当我学习函数式编程时，我必须记住的两条箴言是副作用和可变性是原罪。如果函数式程序员在 Scala 代码库中发现了一个可变引用，他们的手心就会开始冒汗。他们开始呼吸急促，并开始多次扫描所有文件，以找出可能出现代码意外的地方。

然而，在学习了两年的函数式编程之后，我意识到，函数式编程并不是不鼓励副作用或可变性。尽管如此，还是要在函数定义中尽可能多地声明你的程序意图。函数式程序中有各种各样的效果。(如果你不明白我的意思，我在这里写了一篇关于函数式编程中效果是什么意思的文章[。)](https://edward-huang.com/functional-programming/scala/monad/2020/06/21/what-is-effect-or-effectful-mean-in-functional-programming/)

在你的函数中返回一个`IO`告诉开发者这个函数正在做一个 IO 调用。因此，在运行时会出现一些副作用——很有可能是“不安全”的代码。

你写的每一个功能代码都是一个描述，你需要在你的主函数中启动那个调用。我们称之为世界末日。这种“描述”的概念告诉开发人员程序在编译时的预期行为，并向开发人员保证它将按预期行为。

一个例子是`IO`对`Future`。`Future`被热切地评价。因此，实例化一个`Future`将自动启动一个执行上下文并触发另一个线程。另一方面，`IO`被懒洋洋地评价。因此，创建一个`IO`不会自动运行里面的函数，直到你调用`unsafeRun`。具有不同行为的函数可能与`Future`具有相同的结果，但与 IO 不同。让我们看看下面的代码片段:

上面的代码是做什么的？我们想提取数据库两次。然而，当我们使用`Future`进行评估时，它只导致读取数据库一次。另一方面，用`IO`实例化只描述了程序*将*的行为。然后，`unsafeRun`将执行该程序。

上面的代码将获取数据库两次。将程序写成描述而不是函数执行有助于我们弄清楚程序在编译时是如何运行的。

# 通过抽象减少程序中的代码行数

我们通过思考我们希望我们的类型行为的“法则”来做到这一点。通常，这是所有类型范畴系统如单子或函子的来源。

您不需要重复实现函数的不同变体，并为所有程序逻辑使用最简单的原语。你开始根据你希望函数是什么样的“法则”或行为来写函数。例如，您可以创建一个函数，强制传递的参数是关联的。这意味着我们可以确定何时运行`combine(a,b)`将等于运行`combine(b,a)`。

当基于法则构造你的函数时，你不必担心函数的调用者会提供非关联的类型。因此，您可以按照任何想要的顺序实现该函数，这在运行任何并发程序或 map-reduce 类型的作业时非常有用。

范畴论中的这种结合律叫做半群。因此，你可以有一个函数，它的自变量将需要遵守半群规律。

你写的功能性代码越多，你就越会看到你的代码是构建模块。你经常看到函数式程序员是如何痴迷于创造最原始的可能。我所说的本原是函数最广义的形式。例如，如果你想写一个程序来检查某个布尔值是否为真，你必须做某个动作。一般来说，这可以通过“if-else”语句来实现，如下所示:

然而，这个功能非常有限。如果我想有基于某些条件的多个操作分支，而不是两种方式，该怎么办？如果我们有这个函数，它将比有一个布尔语句的函数强大得多。所以，想一想，我们可以用一个索引会代替`checkStatement`的列表。然后，基于该索引，我们可以执行一些值，如下所示:

现在，我们可以用`chooseN`来实现`choose`:

`chooseN`不是最原始的可能函数。你可以使它更一般化，并意识到你可以使用`map`或`flatMap`来实现这些功能。然而，我将把这部分留给您来将`chooseN`派生成`flatMap`。

# 结论

编写功能代码需要一个陡峭的学习曲线。因此，一个高增长的公司对强制执行函数式编程语言犹豫不决，因为开发人员需要时间来学习一种新的思维方式。

您可能需要更长的开发时间，因为您需要考虑所有这些错误。但是，您晚上也会睡得很好，因为您可以确保代码按照您的意图运行。一旦你克服了困难，你会意识到当你推进你的代码时，你是多么的有效率和自信。在程序运行之前，你会注意到程序中可能发生的所有错误。您不需要同时点击`run`功能来查看您的功能将如何运行。

您将编写单个负责任的函数，并减少程序中重复逻辑的数量。一旦你学习了一段时间编写函数式编程代码，并重新开始编写命令式代码，你就会成为一个更强大的程序员，可以看到程序中各种运行时异常和错误。

最重要的是，您确信您构建的功能是健壮的，并且将会创造出出色的用户体验。

# 参考

*   [什么是函数式编程，什么不是函数式编程，为什么它很重要](https://www.inner-product.com/posts/fp-what-and-why/)

*最初发表于*[*【https://edward-huang.com】*](https://edward-huang.com/functional-programming/scala/software-development/programming/2021/08/05/functional-programming-has-made-my-job-easier-as-a-software-engineer-here-s-why/)*。*
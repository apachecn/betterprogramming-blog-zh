# 如何使用 BenchmarkDotNet 对 C#代码进行基准测试

> 原文：<https://betterprogramming.pub/how-to-benchmark-c-code-using-benchmarkdotnet-6de06165f2cb>

## 这个非常简单的工具允许你客观地测量和比较你的代码

![](img/bcfdaae3f060fca34ce48aa28963e3eb.png)

丹-克里斯蒂安·pădureț在 [Unsplash](https://unsplash.com/s/photos/measure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

有时候代码的性能真的很重要。

。NET 已经改进到了这样的程度，大部分时间我们都不需要担心它，我们写的代码已经可以很好地工作了。

当你编写要运行很多很多次的代码时，问题就来了——也许一秒钟要运行几千次。当你有这样的代码时，每一毫秒都很重要，找到如何优化它可能有点棘手。

修复性能问题的第一步是诊断问题所在，为此，您需要能够准确地度量代码的性能。

向 [BenchmarkDotNet](https://benchmarkdotnet.org/articles/overview.html) 问好。

我最近在编写一个树遍历过程的原型时遇到了这个工具，我不知道导航树的哪种方式是最快的。

BenchmarkDotNet 使得对我编写的每种方法进行测试变得非常容易，并客观地指出我应该选择哪个选项。

![](img/fb6032507b7d227f35d1f0d6e0dfb946.png)

我对处理这棵树的不同方法的最终结果，由 BenchmarkDotNet 计算和显示

这就是我最后得到的结果，让我选择我的“DictionaryRecursive”方法，因为响应时间一直很低(不到 6 毫秒)，尽管内存使用率更高。对于我的原型，这就是我所需要的，我在几分钟内就得到了这些结果。

让我们来看看运行这样的测试需要做些什么。

# 测试中的代码

这些测试需要一些代码来运行。在我的例子中，我有一个可以用不同参数运行的 pure 方法的不同实现，我想测试每个实现在一系列参数下的性能。

我使用了一个接口来允许我交换实现。

然后，我用几种不同的方式实现了这个方法，这样我就可以比较结果。

我知道有些实现会比其他的慢，但是我仍然需要实际的数据来作为我决策的基础。事实证明，我对结果有点惊讶。

## 基准项目

一旦你有了一些想要测试的代码，创建一个新的控制台应用程序，并安装 [BenchmarkDotNet](https://www.nuget.org/packages/BenchmarkDotNet/) NuGet 包。

接下来，创建一个新类(我称之为`Benchmark`)，并赋予它`[MemoryDiagnoser]`属性。我还添加了一个`[RankColumn]`属性，将它包含在结果中，但这是可选的。

我们在这个类中添加了我们自己的基准测试。这些测试是应用了`[Benchmark]`属性的简单方法。

这里我添加了五个基准测试。在每个内部，它们实例化一个新的‘approach’类，并用一些生成的输入调用`Calculate` 方法。

接下来，我们需要更新`Program.cs`来运行测试。这是通过调用`BenchmarkRunner.Run<Benchmark>();`来完成的，以便执行基准测试。我用的是[。NET 6 的新顶级语句](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/tutorials/top-level-statements)(这就是为什么你在这里看不到任何`Main`方法)。

就这样，这就是你需要写的所有代码。

你可能很想马上运行这个控制台应用程序。如果您这样做，您可能会得到这个错误。

![](img/3bd85ba8fda962f23bd3691793fdd72a.png)

在调试配置中运行基准时看到的错误

完整的信息是:

> //验证基准:
> 组装节点。定义基准的基准引用非优化节点。API
> 如果你拥有这个依赖，请在发布中构建它。
> 如果没有，您可以通过使用“config。WithOptions(ConfigOptions。DisableOptimizationsValidator)。
> 装配节点。定义基准的基准是非优化的
> 基准是在没有启用优化的情况下构建的(很可能是调试配置)。请在发布时构建它。
> 如果要调试基准，请参见[https://benchmarkdotnet . org/articles/guides/trouble shooting . html # debugging-benchmarks](https://benchmarkdotnet.org/articles/guides/troubleshooting.html#debugging-benchmarks)。

。NET 在发布配置中构建某些东西时会进行很多性能优化，而在使用 Debug 构建时是没有的。用优化后的代码进行任何性能测试总是一个好主意，所以您需要改变运行这个基准项目的方式。

如果你使用的是 Visual Studio 2022，你只需从工具栏中选择发布配置，然后选择“启动而不调试”(Ctrl+F5)。

![](img/a20662a7f5d98985a91d362716b2b0dd.png)

从配置选项中选择“发布”,然后选择“启动而不调试”

一旦设置为发布，您就可以运行您的控制台应用程序。然后基准测试将开始，您将开始看到结果输出。当测试完成时，您会得到最终的摘要输出，它显示了每种方法运行的时间(平均)以及内存使用情况。

![](img/ac0469342553f8e4544a09338c565531.png)

基准测试的结果

因此，在我的测试中，我们可以清楚地看到一些方法比其他方法更快，但也有一些方法需要更多的内存。这些都是非常有用的数据，能够帮助我们决定应该使用哪种方法，或者某个特定的方法是否需要进一步优化(和基准测试)。

让我感到惊讶的一件事是，我的`BasicListFilteredApproach`实际上是所有方法中最慢的，甚至比最初的`BasicListApproach`还要慢，而这正是它应该改进的地方。

这就是为什么这些数据如此重要——如果没有这些数据，我将只能依靠自己的假设和直觉，而在这种情况下，这些假设和直觉是不正确的。不管怎样，这证明了字典比其他方式表现得好得多，这是值得知道的。

# 摘要

我们已经看到，我们可以使用 BenchmarkDotNet 轻松地对 C#代码进行基准测试。我们可以写下一系列不同的测试来运行，并比较所有的结果，看看每个测试的表现如何。我们还看到基准测试需要在发布配置下构建，否则您的结果将是不正确的。

与任何性能优化工作一样，这可能是一个迭代过程。我的下一步将是查看性能最佳的方法，看看是否有任何可以进一步改进的地方，直到我们达到满意的性能水平。

能够如此快速地获得基准测试结果意味着我们将能够衡量未来的改进，并知道我们何时达到了这一水平。
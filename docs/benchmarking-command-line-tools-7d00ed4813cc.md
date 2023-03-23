# 根据自定义的 Go Line Remover 对命令行工具进行基准测试

> 原文：<https://betterprogramming.pub/benchmarking-command-line-tools-7d00ed4813cc>

## 测试一个内置的文本过滤器违背了屡试不爽的 grep

![](img/69858521dacd29dc13927dfa684186d5.png)

在本系列的第四部分(也是最后一部分),我们将介绍如何针对 grep 中的一个等价命令对我们的自定义 Go line remover 进行基准测试。您可以在下面找到其他部分:

*   [第 1 部分:在 Go 中构建文本过滤工具](/how-to-build-a-text-filtering-log-simplifying-tool-in-go-b29d3067e092)
*   [第 2 部分:添加正则表达式支持](https://medium.com/@gourikabang1996/part-2-how-to-prepare-for-your-next-coding-interview-d048b188301e)
*   [第 3 部分:在 Go 中对程序进行基准测试](/benchmarking-in-go-substrings-vs-regular-expressions-a84de7f0eb02)

在前三段中，我们在 Go 中构建了一个工具，可以从文件中删除匹配的行。然后我们添加了正则表达式支持，最后使用内置的 Go 基准测试比较了两种方法的性能。现在，我们想要测量它在 grep 中针对反向字符串匹配(`-v`或`--invert-match`)的性能。

我们将使用 [hyperfine](https://formulae.brew.sh/formula/hyperfine) 进行基准测试，因为它是一个排名很高的工具，也可以通过我的机器(苹果芯片的 MacBook)的自制程序获得。还有各种其他工具(例如[工作台](https://formulae.brew.sh/formula/bench))可能更适合您的需求和架构。

我们将比较我们在第 3 部分中看到的相同的三个测试用例(如下所述),但是输入文本要大得多，以查看工具在需要做更多工作的情况下如何执行。

# 设置

公平的比较需要良好的设置。为此，我们将针对 grep 对 Go 工具进行基准测试，做三件事:

*   将文件加载到 grep
*   在 grep 中进行反向匹配
*   将反向匹配的结果写入文件

我们使用以下版本的 grep:

Grep 命令将采用以下格式:

`grep "<pattern>" <filepath> > <output filepath>`

Lineremover 命令将采用以下格式:

`./lineremover -file="path/to/file" -keys="keys if testing substring search" -pattern="pattern if testing regular expression search"`

我们可以按照以下方式运行基准测试:

`hyperfine '<command to benchmark>' --min-runs=10000`

其中`'<command to benchmark>'`将如上运行 grep 或 lineremover。为了保持一致性，并确保我们有足够的数据进行推断，我们将使用 hyperfine 的`--min-runs`选项来确保每个测试至少执行 10，000 次。我们还需要意识到在执行基准测试的机器上运行的是什么——如果它在一个测试期间相对于另一个测试变慢了，基准测试可以显示出来。考虑到这一点，我们将尽可能地让机器在测试中保持相同的状态，尽可能减少后台进程的运行。

我们将测试来自[拆线器报告](https://github.com/swayne275/lineremover)的`example/inputlong.txt`。这个文件本质上与`example/input.txt`相同，但是重复了 2048 次，因此相对于实际的字符串搜索工作，程序开始/停止时间对我们的基准测试结果影响较小。

# 运行基准测试

## 基准匹配“你好”

注意，对于 grep，我们告诉 hyperfine 忽略错误(`-i`)，因为 grep 会在不匹配的情况下给出一个非零退出代码(这是本测试所期望的)。

因此，我们可以看到 grep 在这个测试中快了很多——这是一种预期的结果，因为它是这个领域的标准，并且[自 1974 年](https://www.google.com/search?client=safari&rls=en&q=when+was+grep+invented&ie=UTF-8&oe=UTF-8)以来就一直存在。在`lineremover`工具中，我们可以看到模式匹配也比子串搜索慢一些。运行之间也有相当多的变化。

## 基准匹配“大”

在这里，我们看到 grep 是最慢的——这不是我所期望的！我们还可以看到，模式匹配比子串搜索略快，但这可能并不重要，而且变化也稍多。

## 基准匹配“大”、“亮”、“弯”、“亮”

随着查询变得越来越复杂，我们可以看到，与子串搜索相比，模式匹配的速度明显变慢了。这里我们再次看到，在模式匹配和子串搜索模式下，grep 都明显比`lineremover`慢。它也有更多的变化。又是一个意想不到的结果！

# 包扎

今天，我们学习了对两个命令行工具进行基准测试的机制，并设置了一些测试，这些测试可能合理地代表了这些工具在现实世界中的使用情况。然而，基准测试时真正有价值的工作是找出*要基准测试什么*，而不一定是如何运行测试。哪些测试会让你相信某个工具对你的工作负载来说更快*或者使用 cas* e？这是设计测试时需要回答的最重要的问题。只有这样，我们才能设计一个环境来回答这个问题。

就这个测试而言，它很有趣！这是我第一次使用超精细，所以这很酷。在这个工具中，我期望模式匹配比子串搜索慢，但是没有期望我们构建的 Go 工具在某些搜索条件下优于 grep。我还不确定*为什么* grep 变慢了，但一个猜测是读取输入文件和写入输出文件不如在 Go 中使用缓冲 IO 时优化了？我也认为可能是一些操作系统的东西(比如缓存)在起作用，但是我一个接一个地以不同的顺序运行测试，每次都得到相似的结果。如果你有想法，请告诉我！
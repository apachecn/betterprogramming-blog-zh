# Golang 中的优化练习

> 原文：<https://betterprogramming.pub/an-optimization-exercise-in-golang-43fea807441d>

## 这个简单的时间转换还能更快吗？提示:它快了 420 倍

![](img/20428be71738289fcf2863b51eb69ce2.png)

[哈雷戴维森](https://unsplash.com/@harleydavidson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/fast?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片。

在一个安静的日子里，我的一个朋友向我寻求一个想法:他说:“如果我给你一个 12 小时格式的一小时字符串，比如`7PM`，你会如何得到 24 小时格式的整数版本，比如`19`？”在回答了这个问题后，一些关于这个问题能有多快开始流动的想法，我决定去那个兔子洞，希望能学到一两件事。

*剧透警告:我学到了很多东西，以及我的一些期望是多么的错误。*

# 问题陈述

问题很简单:

*   输入到函数的字符串保证有一个数字，后跟`AM`或`PM`。一个有效的例子是`7PM`。
*   代码会用 Golang 写。
*   我们的目标不是编写最准确的代码或可读性最强的代码，而是尽可能快地处理给定的情况。
*   我们将明确忽略错误处理，因为我们的目标不是编写生产代码，我们是在测试单个案例的性能。

因为我们使用 Golang，所以我们有可用的 Golang 基准工具，我们将针对不同的方法运行基准测试，以查看哪种方法运行得更快。

# 1.天真的方法

Golang 的标准库令人惊叹，已经有相当多的函数处理日期和时间转换。这里最简单的方法是依靠标准库来解析字符串，并将结果数字转换为整数:

在这个版本中，我们将`inputString`值直接传递给`time.Parse`函数，后者返回一个`time.Time`值。一旦我们有了值，我们就把它格式化成一个字符串。

为了衡量性能，我们将运行一个简单的基准测试:

一旦我们有了测试，我们可以用下面的命令运行它:

```
go test -bench=.
```

输出如下所示:

```
❯ go test -bench=.
goos: darwin
goarch: amd64
BenchmarkParseWithDateTime-8    10220811               115 ns/op
PASS
```

最右边的列是这里的键值。它说一次操作平均需要 115ns 。在这一点上，对于许多用例来说，代码已经足够快了，但是为了这个原因，我们将继续下去。

我们能比这更快吗？

# 2.用 ReplaceAll 进行整数计算

假设我们的用例很简单，除了转换小时之外，不需要任何特殊的日期-时间操作，我相信我们可以在 AM-PM 时间的基础上完成简单的整数加法。

这里的简单尝试是检查时间是 AM 还是 PM，从字符串中删除 AM-PM 部分，并将结果转换为整数。代码如下所示:

我们将像之前一样测试它:

结果很有趣:我们将速度提高了三倍(简单方法的速度为 115ns/op，而新实现的速度为 44.3ns/op):

```
❯ go test -bench=.
goos: darwin
goarch: amd64
BenchmarkParseWithDateTime-8            10030531               115 ns/op
BenchmarkParseWithReplaceAll-8          25858335                44.3 ns/op
PASS
```

这很酷，但问题仍然存在:我们能比这更快吗？

# 3.使用 TrimSuffix 而不是 ReplaceAll

前面的方法很好，但是它使用了`strings.ReplaceAll`，这可能会慢一些，因为它需要搜索整个字符串。尽管字符串很小，但这可能会对性能产生影响。因此，我决定尝试使用`strings.TrimSuffix`函数。

代码在这里:

测试几乎是一样的:

然后嘭，成功了！这样，时间减少到以前的 26 %: 45 ns/op 比 12ns/op。

```
❯ go test -bench=.
goos: darwin
goarch: amd64
BenchmarkParseWithDateTime-8                     9056252               122 ns/op
BenchmarkParseWithReplaceAll-8                  24098204                45.2 ns/op
BenchmarkParseWithReplaceAllTrimSuffix-8        96514599                12.0 ns/op
PASS
```

因此，我们从 122ns/op 开始，并设法将其速度提高了 10 倍。在这一点上，停止担心速度可能是有意义的。对于那些想继续下去的人来说，这个问题仍然存在。

我们能比这更快吗？

# 4.摆脱 TrimSuffix

解决这个问题的更简单的方法可能是直接操作字符串，而不是使用`strings.TrimSuffix`，因为我们知道最后两个字母总是会被删除。为此，我编写了以下代码:

对此运行基准测试也非常简单:

是的，我们又变快了！

```
❯ go test -bench=.
goos: darwin
goarch: amd64
BenchmarkParseWithDateTime-8                     9958882               117 ns/op
BenchmarkParseWithReplaceAll-8                  25727338                44.6 ns/op
BenchmarkParseWithReplaceAllTrimSuffix-8        99211851                11.9 ns/op
BenchmarkParseWithStringManipulation-8          152646218                7.85 ns/op
PASS
```

以前的方法大约是 12ns/op，而新方法让我们达到了 7.85ns/op。此时，我不确定是否有更快的方法，但我想我们需要继续尝试。

我们能比这更快吗？

# 5.拆卸 HasSuffix

因为我们知道字符串的最后两个字符总是相同的，所以用于确定给定字符串是 AM 还是 PM 的区别值将是最后一个字符之前的字符。按照这个逻辑，也许我们可以通过直接角色访问比`strings.HasSuffix`走得更快。代码如下:

是的，这也奏效了:

```
goos: darwin
goarch: amd64
BenchmarkParseWithDateTime-8                                     9716124               115 ns/op
BenchmarkParseWithReplaceAll-8                                  26728262                45.0 ns/op
BenchmarkParseWithReplaceAllTrimSuffix-8                        97785483                11.7 ns/op
BenchmarkParseWithStringManipulation-8                          157282510                7.23 ns/op
BenchmarkParseWithStringManipulationWithoutHasSuffix-8          262210579                4.46 ns/op
PASS
```

以前的方法约为 7ns/op，我们设法将其降至 4.46ns/op。虽然这是显而易见的风险，但问题仍然存在:我们能比这更快吗？

# 6.四处打听

对于最后一次尝试，我不知道如何提高这个函数的性能。因此，我开始询问一些朋友和同事的想法。

经过一番挖掘，我的同事 [Cassondra Foesch](https://www.linkedin.com/in/cfoesch/) 想出了以下的黑魔法:

她警告我，这将被认为是一种竞争伎俩，它假设输入总是有效的，这符合我们的期望和我们一开始设定的目标。现在我们需要将它与其他算法进行比较:

```
❯ go test -bench=.
goos: darwin
goarch: amd64
BenchmarkParseWithDateTime-8                                    10635800               113 ns/op
BenchmarkParseWithReplaceAll-8                                  25383086                44.5 ns/op
BenchmarkParseWithReplaceAllTrimSuffix-8                        99624820                11.7 ns/op
BenchmarkParseWithStringManipulation-8                          170418279                7.02 ns/op
BenchmarkParseWithStringManipulationWithoutHasSuffix-8          273248098                4.35 ns/op
BenchmarkParseWithBlackMagic-8                                  1000000000               0.268 ns/op
PASS
```

哇！使用这一最新功能，时间为 0.268ns/op。这比以前的方法大约快 16 倍。在这一点上，它比我们最初的方法快了 420 倍。

# 结论

总的来说，这是一个有趣的实验，以了解更多我们用于简单操作的日常功能的特征。如果您只执行一次这个操作，纳秒并没有多大关系，但是如果这个操作在每个操作单元中执行了数百万次，纳秒就会累积起来。一般来说，了解我们使用的工具的行为是有好处的，这样我们就可以选择最适合这项工作的工具。

在这个实验中，我学到了:

*   日期库做很多事情，不适合这种简单的操作，看似日期时间的事情，实际上是字符串操作的挑战。然而，日期-时间方法仍然会产生最干净的代码。
*   如果在字符串的开头或结尾移除，字符串函数`strings.TrimPrefix`和`strings.TrimSuffix`比`strings.ReplaceAll`快。
*   直接访问字符而不是使用标准库函数来进行简单的比较要快得多。显然，这取决于用例。
*   对于最后一个算法，我尝试记忆`len`调用，因为它们是乘法，但这没有任何影响。根据[这个 StackOverflow 的回答](https://stackoverflow.com/a/26634977)，那些重复的调用已经返回了缓存的结果。
*   通过利用简单的技术并考虑如何最好地做一件事，我们可以在不牺牲代码质量的情况下引入巨大的性能改进。如果我们在最后一个神奇的步骤之前停下来，我们将会有一个比简单方法快 30 倍的易读函数。

总的来说，我知道这段代码不适合生产，原因有很多:

*   没有错误处理。
*   没有执行任何输入验证。
*   边缘情况还没有被正确测试，所以我不确定`12AM`和`12PM`是否总是被正确计算。

尽管如此，实现算法并直接测量结果而不是猜测在这里被证明是一个更好的决定。我设法进行了一次有趣的锻炼，学到了不少东西。

如果您认为上面的代码有任何错误，请随时纠正我。此外，对于那些准备接受挑战的人来说，这个问题仍然存在:我们能比这更快吗？

感谢您花时间阅读本文。如果你有进一步的建议，请在评论中告诉我！

# 资源

*   Golang 标准库中的`strings`模块文档，其中包含方法`HasSuffix`、`ReplaceAll`和`TrimSuffix`:[https://golang.org/pkg/strings/](https://golang.org/pkg/strings/)的详细信息
*   详细描述了`len`行为的 StackOverflow 答案:[https://stack overflow . com/questions/26634554/go-multiple-len-calls-vs-performance/26634977 # 26634977](https://stackoverflow.com/questions/26634554/go-multiple-len-calls-vs-performance/26634977#26634977)
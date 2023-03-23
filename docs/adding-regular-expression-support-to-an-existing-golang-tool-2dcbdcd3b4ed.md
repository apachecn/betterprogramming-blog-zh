# 向现有 Golang 工具添加正则表达式支持

> 原文：<https://betterprogramming.pub/adding-regular-expression-support-to-an-existing-golang-tool-2dcbdcd3b4ed>

## 文本过滤第 2 部分:电子正则表达式布加洛

![](img/8b52e1786c07301f877f589cd5234c6f.png)

这是四部分系列的第二部分(有些仍在进行中)。你可以在这里找到其他人:

*   [第 1 部分:在 Go 中构建一个文本过滤工具](/how-to-build-a-text-filtering-log-simplifying-tool-in-go-b29d3067e092)
*   [第 3 部分:在 Go 中对程序进行基准测试](/benchmarking-in-go-substrings-vs-regular-expressions-a84de7f0eb02)
*   [第 4 部分:针对 grep 的命令行基准测试](/benchmarking-command-line-tools-7d00ed4813cc)

之前，我们在 Go 中构建了一个[反向字符串匹配工具，目标是在调试的同时去除有噪声的日志。第一次迭代接受一个或多个关键短语来匹配源文件。这对于有些静态的、重复的行来说是很好的，但是如果我们想要删除匹配特定模式的行呢？](/how-to-build-a-text-filtering-log-simplifying-tool-in-go-b29d3067e092)

输入正则表达式。

今天，我们将介绍 Go 中正则表达式的一些基础知识，以及如何扩展现有程序以向后兼容的方式添加功能。

# 什么是正则表达式？

正则表达式(简称 regex)是一系列指定搜索模式*的字符。字符串搜索算法使用它们来识别文本的相关部分。*

您可能已经熟悉了通配符——使用`*`来表示任意数量的字符(包括空字符串)。Regex 更进了一步。

它们允许您更具体地进行模式匹配。您可以使用`[^<characters to avoid>]`搜索除之外的任何字符*。你可以用`^<phrase>`搜索以*开头的短语。同样，您可以使用`<phrase>$`搜索以结尾的内容。你可以做的还有很多，你可以在这里了解[，在这里](https://www.computerhope.com/jargon/r/regex.htm)测试[。](https://regex101.com)

# 我们为什么关心？

日志消息通常非常适合子串匹配，但并不总是如此。如果我们想要删除两个特定时间戳之间的日志行，比如 2022 年 8 月 1 日至 4 日，会怎么样？我们可以使用现有的子串匹配工具，通过重复我们自己:

```
-keys="logcreated-20220801:|logcreated-20220802:|logcreated-20220803:|logcreated-20220804:
```

或者我们可以搜索匹配以下正则表达式的模式:

```
^logcreated-2022080([1-4]):
```

随着查询复杂性的增加，正则表达式成为定制、可重复搜索的强大工具。

# 添加新功能

正如在[上一篇文章](/how-to-build-a-text-filtering-log-simplifying-tool-in-go-b29d3067e092)中，我们应该考虑用户将如何与新功能交互。为了简单起见，我们可以假设用户将提供一组匹配的子字符串(`-keys`)或一个模式(`-pattern`)，或者两者都提供。这将使指令和错误处理逻辑变得容易——如果既没有给出`keys`也没有给出`pattern`就抱怨吧。

要添加反向模式匹配，我们需要执行以下操作:

*   更新自述文件和帮助说明
*   添加新标志和错误处理
*   编写正则表达式匹配代码
*   更新处理循环以在正则表达式和子字符串匹配之间进行选择

为简洁起见，可在[工具报告](https://github.com/swayne275/lineremover)中查看更新的帮助和自述文件指南。

## 添加新标志和错误处理:

因为我们通过`getUserInput()`获取用户输入，并将配置存储在`config`中，所以我们只需要更新这些实体来添加一个新的标志。这里，我们只是在第 11 行定义新的标志，并验证它或`keys`是否已经在第 19 行被传入。

对于这段代码，我们使用另一个标准库包 [regexp](https://pkg.go.dev/regexp) 来处理正则表达式匹配。我们还验证了所提供的模式实际上可以用作正则表达式。通过在这里这样做，我们可以在一个地方对用户输入相关的事情进行早期的失败。

注意，Go 的 regexpr 不支持某些功能，比如[否定前瞻](https://stackoverflow.com/questions/26771592/negative-look-ahead-in-go-regular-expressions)。做一些快速的谷歌搜索，似乎有各种各样的 RE2 实现应该提供额外的功能，但这是另一个时间的探索。

## 编写正则表达式匹配代码:

因为我们已经编译了正则表达式并将其包含在配置中，所以我们只需要对正则表达式调用`MatchString()`，传入要检查的字符串。这将产生一个关于是否匹配的布尔值。类似于子串检查，我们可以遍历输入文件中的所有行，对它们调用`MatchString()`，并保存不匹配的行。

为了简化处理逻辑，让我们给`config`添加一个接收器:

可以在一个`config`实例上调用`lineMatches()`来确定给定的字符串是否匹配模式中提供的内容，或者关键短语中提供的内容。

## 更新处理循环

`transformInputImpl()`没有太大变化。我们将接受一个`*config`作为输入(而不是几个变量)，我们将交换:

```
if !substrInLine(line, keys) {
```

用于:

```
if !cfg.lineMatches(line) {
```

这样做的一个好的副作用是，所有的行匹配逻辑都被捆绑在`lineMatches()`中，这使得对处理的进一步调整变得清晰和集中。

# 使用工具

基于我们之前的示例，我们现在可以替换:

```
go run main.go -file="example/input.txt" -keys="hello"
```

使用:

```
go run main.go -file="example/input.txt" -pattern="hello"
```

这将完成同样的事情，但不是很令人兴奋或新的。不如我们这样做:

```
go run main.go -file="example/input.txt" -pattern=".*b([r]?)ig([ht]?).*"
```

使用 [regex101](https://regex101.com) 可以进一步解释这种模式，但本质上它匹配任何包含“big”、“brig”、“bight”或“bright”的输入行。在`examples/input.txt`中，这将只剩下`hello world`了！

如果我们愿意，我们甚至可以提供一组关键短语和一个模式！

```
go run main.go -file="example/input.txt" -keys="hello" -pattern=".*b([r]?)ig([ht]?).*"
```

将不会在我们的输入文件中留下任何内容。

# 包扎

今天，我们扩展了一个已有的工具，在不需要对核心结构做太多改变的情况下添加了额外的功能。当构建软件时，我们必须考虑当前的用例，但是考虑未来的用例是谨慎的。在设计和构建阶段考虑这一点可以允许软件在对现有代码进行最小改动的情况下进行扩展，因此减少了出现错误、麻烦和问题的空间。

进一步扩展`lineremover`以接受目标文件的路径留给读者作为练习。

如果你克隆了 [repo](https://github.com/swayne275/lineremover) 并遵循上面的过程，你就可以进行一个快速而无痛的支线任务，让你的手变得有点脏！
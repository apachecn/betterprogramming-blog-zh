# Rust 程序员对 Go 的早期印象

> 原文：<https://betterprogramming.pub/early-impressions-of-go-from-a-rust-programmer-f4fd1074c410>

## Go vs. Rust

![](img/3a7ea0f9f719898c92d56e142229ba94.png)

图片来源:作者

过去几周我一直在用 [Go](https://en.wikipedia.org/wiki/Go_(programming_language)) 。这是我第一次用 Go 做一个大型的严肃的项目。我之前看了很多 Go，在研究 [Rust](https://en.wikipedia.org/wiki/Rust_(programming_language)) 的特性时，我玩过一些例子和玩具程序。真正的编程是一种非常不同的体验。

我认为写下我的印象会很有趣。我会尽量避免和 Rust 做太多的比较，但是因为那是我来自的语言，所以会有一些。我应该提前声明对 Rust 的强烈偏见，但我会尽最大努力做到客观。

# 总印象

用 Go 编程很不错。它有我在图书馆想要的一切，而且没有太多粗糙的边缘。学习它是一种顺利的经历，它是一种设计良好的实用语言。一些例子:一旦你知道了语法，许多来自其他语言的习惯用法就会延续下去。一旦你学会了一些围棋，预测其他特征就相对容易了。有了一些其他语言的知识，我能够阅读 Go 代码，并且不用太多的谷歌搜索就能理解它。

与使用 C/C++、Java、Python 等相比，它少了很多挫败感，也多了很多生产力。然而，它确实感觉像是那一代语言的一部分。它从他们那里学到了一些经验，我认为它可能是那一代人中最好的语言，但它绝对是那一代人的一部分。这是一种渐进的改进，而不是完全不同。

明确一点，这不是一个价值判断；增量在软件工程中通常是好的。一个很好的例子是`nil`:Rust 和 Swift 这样的语言去掉了`null`的概念，消除了一整类错误。Go 使它变得不那么危险:没有空值，并且它区分了`nil`和`0`。但是核心思想仍然存在，取消引用空指针的常见运行时错误也是如此。

# 可学性

围棋非常容易学。我知道这是一个经常被吹捧的好处，但是我真的很惊讶我这么快就变得有效率了。多亏了语言、文档和工具，我在两天后就写出了有趣的、可提交的代码。

促成可学性的几个因素是:

*   围棋很小。许多语言都试图变小，但是 Go 真的很小——非常小。(这在很大程度上是一件好事，我对这种自律印象深刻。)
*   标准库很好(同样，很小)。从生态系统中查找和使用库非常容易。
*   这种语言中很少有其他语言没有的。Go 从其他已建立的语言中吸取了很多东西，对它们进行了润色，并很好地将它们组合在一起。它极力避免新奇。

# 样板文件

Go 代码很快变得非常重复。它缺少任何减少重复的机制，比如宏或泛型(接口对于抽象来说很好，但是对于减少代码重复来说不太好)。我经常得到很多函数，除了类型之外都是一样的。

错误处理也会导致重复。许多函数的样板文件多于有趣的代码。

使用泛型或宏来减少样板文件有时会受到批评，因为这会使代码更容易编写，但代价是代码更难阅读。但是我发现围棋正好相反。复制和粘贴代码既快速又简单，但是阅读 Go 代码却令人沮丧，因为你必须忽略其中的大部分内容或者寻找细微的差别。

# 我喜欢的东西

编译时间——这肯定很快，而且肯定比 Rust 快得多。但是他们实际上没有我预期的那么快。对我来说，对于大中型项目来说，它类似于 C/C++或者比 C/c++快一点，我有点期待即时。

Go 例程和通道— 拥有生成 Go 例程和使用通道的轻量级语法真的很好。这确实显示了语法的力量，如此小的细节使得并发编程感觉比其他语言好得多。

界面——它们不是很复杂，但是很容易理解和使用，在很多地方都很有用。

`if ...; ... { }`语法——能够将变量的范围限制在`if`语句的主体中是一件好事。这与迅捷和铁锈中的`if let`效果类似，但更通用。(围棋没有 Swift 和 Rust 这样的模式匹配，所以无法使用`if let`。)

测试和文档注释——这些都很容易使用。

`Go`工具——把所有东西都放在一个地方比在命令行上需要多个工具要好。

垃圾收集器(GC)！—不考虑内存管理确实会让编程变得更容易。

瓦拉格斯。

# 我不喜欢的东西

排名不分先后:

`nil`切片- `nil`、`nil`切片和空切片都是不同的——我很确定你只需要其中的两个，而不是三个。

没有第一类枚举——使用常量感觉落后。

不允许导入周期——这实际上限制了包对于模块化项目的有用性，因为它鼓励将大量文件放在一个包中(或者有许多小的包，如果应该放在一起的文件没有放在一起，这也一样糟糕)。

`switch`可能并不详尽。

`for ... range`返回一对索引/值——只获取索引很容易(忽略值即可),但只获取值需要显式。对我来说这似乎是颠倒的，因为在大多数情况下我需要的是值而不是索引。

语法:

*   定义和用途不一致
*   编译器的选择性(例如，要求或禁止尾随逗号)；这通常可以通过良好的工具来缓解，但是仍然有几次会产生令人讨厌的额外步骤。
*   当使用多值返回类型时，类型上需要括号，但在`return`语句中不需要。
*   声明一个结构需要两个关键字(`type`和`struct`)。
*   使用资本化标记变量公共或私人。就像匈牙利符号，但更糟。

隐式接口——我知道我也把它列在我喜欢的列表中，但是有时它真的很烦人，例如，当试图找到实现一个接口的所有类型或者对于一个给定的类型实现了哪些接口时。

你不能在一个不同的包中编写带有接收器的函数，所以即使接口是鸭类型的，它们也不能为上游类型实现，这使得它们用处不大。

上面我已经提到了泛型和宏的缺乏。

# 一致性

作为一名语言设计师和程序员，关于 Go 最让我惊讶的事情大概就是内置的东西和用户可用的东西经常不一致。许多语言的目标都是尽可能地消除魔法，让用户可以使用内置特性。运算符重载是一个简单但有争议的例子。围棋有很大的魔力！你很容易碰到一堵墙，不能做内置的东西可以做的事情。

一些让我印象深刻的事情:

对于返回多个值和通道有很好的语法，但是这两者不能一起使用，因为没有元组类型。

有一个`for ... range`语句用于对数组和切片进行迭代，但是不能对其他集合进行迭代，因为没有迭代器的概念。

像`len`和`append`这样的函数是全局的，但是没有办法让你自己的函数也是全局的。这些全局函数只适用于内置类型。它们也可以是泛型的，即使 Go 应该没有泛型。

不存在运算符重载。这对于`==`来说尤其令人讨厌，因为这意味着你不能使用自定义类型作为映射中的键，除非它们是可比较的。该属性是从类型的结构派生的，不能被程序员重写。

# 结论

Go 是一种简单、小巧、有趣的语言。它有一些奇怪的角落，但大部分是精心设计的。它学习起来非常快，并且避免了任何在其他语言中不为人所熟知的功能。

Go 和 Rust 是非常不同的语言。尽管两者都可以被模糊地描述为系统语言或 C 语言的替代品，但它们有不同的目标和应用、语言设计风格和优先级。垃圾收集是一个非常大的区别。在 Go 中使用 GC 使得语言变得更简单、更小，也更容易推理。Rust 中没有 GC 使得它真的很快(特别是如果你需要有保证的延迟，而不仅仅是高吞吐量)，并且支持 Go 中不可能的特性和编程模式(或者至少不会牺牲性能)。

Go 是一种编译语言，具有实现良好的运行时。它很快。Rust 也可以编译，但运行时间要短得多。它真的很快。假设没有其他限制，我认为在使用 Go 和 Rust 之间的选择是在更短的学习曲线和更简单的程序(这意味着更快的开发)与 Rust 非常非常快并拥有更具表现力的类型系统(这使您的程序更安全，意味着更快的调试和错误查找)之间的权衡。

感谢阅读！
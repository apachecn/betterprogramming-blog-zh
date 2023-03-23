# 像 Ruby 一样流畅，像 C 一样快速——这样的语言存在吗？

> 原文：<https://betterprogramming.pub/slick-like-ruby-fast-like-c-does-such-a-language-exist-9066fc16e6f1>

## 是的，是的，它是

![](img/10428fe027cc610a627acd782659ee83.png)

rawpixel.com 创建的背景向量—[www.freepik.com](http://www.freepik.com)

在我永无止境地寻找做事速度(开发速度)和做事速度(性能)之间的和谐的过程中，我遇到了一个处于孵化阶段的项目，它让我思考:“这可能是一种适合我的语言。”

对为人类和计算机设计的编程语言 [Crystal](https://crystal-lang.org/) 一见钟情。多么崇高的事业。作为 Ruby 语法之美的粉丝，类似 Ruby 的语法和 C 语言的速度的前景非常吸引人——甚至改变了生活。

从那天起，我就一直密切关注着 Crystal 的进展，今天，我将为您解释为什么您应该关注它。老实说，它是最激动人心的语言之一，有着巨大的潜力。

在我们开始之前，请记住 Crystal 还没有准备好投入生产，但是你仍然可以找到许多已经在使用它的项目——比如用 Crystal 编写的[这个版本的 Sidekiq](https://github.com/mperham/sidekiq.cr) 。

# 为什么是水晶？

那么，为什么有人会关心另一种编程语言呢？嗯，因为 Crystal 结合了令人难以置信的令人信服的成分，这在许多其他语言中是找不到的。

## 漂亮的语法

Crystal 最吸引人的地方之一是清晰易读的类似 Ruby 的语法。这种语言的创造者理解 Ruby 作为视觉上最吸引人的语言之一的吸引力，并把它作为 Crystal 的灵感来源。

所以，如果你来自 Ruby 世界，过渡到 Crystal 会很简单。大多数时候，你可以直接在 Crystal 中运行 Ruby 代码，或者在 Ruby shell 中运行 Crystal 程序。

更重要的是，你甚至可以在 Crystal 中使用 Ruby 语法高亮。与大多数解释语言相似，Crystal 将让你用几行代码构建你最疯狂的想象。

## 极快的性能

Crystal 是一种静态编译的语言，建立在受人尊敬的 LLVM 框架之上，可以抵御 C、 [C++](http://www.cplusplus.com/) 和 [Rust](https://www.rust-lang.org/) 之类的语言。

让我们稍微理解一下 Ruby 语法的开发速度与 C……

这非常有说服力，希望你能像我第一次听到这个说法时一样兴奋。不相信我？只需查看一些最新的基准测试；[这个基准](https://github.com/tbrand/which_is_the_fastest)和[这个基准](https://github.com/drujensen/fib)。

## 通过简单的 C 绑定实现超高性能

如果应用程序或算法的一部分需要极高的性能，一种策略是将功能卸载到 C 扩展或库。

使用 Crystal，无需编写一行 C 代码就可以绑定到现有的 C 库或您自己的 C 库。

考虑一个 C 库`hello.c`的简单例子，我们可以使用 GCC 编译器`gcc -c hello.c -o hello.o`来构建它。

```
#include <stdio.h>void hello(const char * name){
  printf("Hello %s!\n", name);
}
```

构建二进制文件后，您可以使用`Link`轻松地链接二进制文件，并定义`lib`声明，该声明将属于该库的函数和类型分组，然后调用您的函数。瞧啊。

```
#hello.cr@[Link(ldflags: "#{__DIR__}/hello.o")]lib Say 
  fun hello(name : LibC::Char*) : Void
endSay.hello("your name")
```

## 静态打字

Crystal 是一种静态类型语言，允许它在编译时排除许多与类型相关的错误，并为优化做好准备，这在动态类型语言如 Ruby 或 Python 中是不可能的。

这直接有助于 Crystal 的性能，更令人印象深刻的是，Crystal 中的编译器只要求您在不明确的情况下显式指定类型——其余时间您可以像使用任何动态语言一样使用它。

## 宏指令

宏是一种修改抽象语法树的方法，它是在编程语言的符号化和解析阶段创建的，允许我们在编译时添加方法或者创建和修改类。

这样做的主要优势是速度——因为您节省了编译器调用函数所花费的大量时间。

在编写宏时，Crystal 允许您使用大部分语言，这意味着您可以完成在静态编译语言中通常闻所未闻的疯狂的魔术。

## Web 框架

如果不谈论该语言中可用的 web 框架，任何比较都是不完整的，如果你已经爱上了 Rails 和[Phoenix](https://github.com/phoenixframework/phoenix)——你会对 Crystal 的 web 框架 [Amber](https://amberframework.org/) 感到如鱼得水。

它完全是按照 Rails 设计的，但显然比 Rails 快一个数量级——加载时间以微秒计，而不是以毫秒计。

如果你属于辛纳特拉爱好者的阵营，不要害怕，因为你已经得到了[凯末尔](https://kemalcr.com/)框架的简单性。

我提到过 Crystal 的内置 HTTP 服务器在基准测试中每秒能够处理超过[200 万个请求吗？大多数框架也为 web 应用程序提供了亚毫秒级的响应时间。](https://www.techempower.com/benchmarks/previews/round15/#section=data-r15&hw=ph&test=plaintext&l=zdk8an&c=3)

## 并发

Crystal 目前以一等公民的身份支持并发性，而并行性正在逐步提升——你可以在 Crystal 网站上了解它的进展[。](https://crystal-lang.org/2019/09/06/parallelism-in-crystal.html)

Crystal 使用*纤程*支持并发——一个轻量级的操作系统线程。不像线程那样先发制人，纤程明确地告诉运行时调度程序何时切换上下文。这有助于 Crystal 避免不必要的上下文切换。

如果你想增加并发性，你会发现 Crystal 中的`spawn`方法类似于 Go[Go](https://golang.org/)中 goroutines 的用法——我个人非常喜欢这种用法。

```
# Simple example
spawn do 
  sleep 5.seconds 
end
```

Crystal 还支持受 CSP 启发的通道，允许在光纤之间传输数据，无需共享内存，也不必担心锁或信号量。

# 但是等等，有什么不喜欢的？

作为软件工程师，我们总是在选择编程语言或框架时进行权衡，就像任何语言一样，Crystal 不是您所有祈祷的答案，它有自己的局限性。

*   Crystal 仍然相对年轻和不成熟，这导致在这个特定的时间点缺乏社区和软件包。
*   这也导致了可用开发工具的稀缺，即使它们很容易获得。
*   如果你的目标是做一些难以置信的具体的事情，你会很难找到文档，但这只是意味着我们有机会成为第一批采用者，一起黑客很酷的项目。
*   尽管 Crystal 内置了并发性，但并行性仍然是一等公民。
*   由于该语言的预生产状态，在达到 v1.0 之前，有可能发生突破性的更改。
*   它也没有很好的 Windows 兼容性，但老实说，这对我来说不是一个缺点。

# 外卖食品

Crystal 可能还需要一段时间才能投入生产使用，因为它背后有很好的工具和繁荣的社区。但令人欣慰的是，像 Crystal 这样精彩的语言正在幕后工作。

纵观 Crystal 汇集的所有特性，它值得大量关注和普及。如果我激起了你的兴趣，我会鼓励你去看看 Crystal，自己做决定。

与此同时，我将继续宣传 Crytal，并密切关注它的进展。

# 开始使用这些资源

*   [牛逼水晶— GitHub](https://github.com/veelenga/awesome-crystal)
*   [水晶编程— Reddit](https://www.reddit.com/r/crystal_programming/)
*   [凯末尔](http://kemalcr.com/)
*   [琥珀色框架](https://amberframework.org/)
# 为什么一些资深开发者不喜欢 Python

> 原文：<https://betterprogramming.pub/why-some-senior-developers-dont-like-python-974c5361fff2>

## 编程；编排

## 以及为什么他们提倡像 C++这样的静态语言

![](img/76104ace93e26e55d0a8fbf07c4d70a6.png)

Alex Kotliarskyi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在我的[上一篇关于最慢的现代编程语言的文章](https://medium.com/better-programming/the-slowest-modern-programming-languages-d29ea2306675)中，我讨论了像 Python 这样的动态类型语言是如何用户友好和不易出错的。

事实证明，并非所有人都同意这一观点。

许多高级开发人员表示，他们发现使用动态类型语言是一件痛苦的事情。下面的回复收集了大部分的掌声。

> “请问，什么？动态类型语言比静态类型语言更不容易出错？很抱歉，但是，在我 21 年的软件开发生涯中没有。”— [int main(void)
> {int i = 50;if (i % 2 == 0)
> {printf("inside if statement\n");printf("i is even\n");}}](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)
> 
> [输出:](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)
> 
> ```
> ~/ $ ./test1
> inside if statement
> i is even
> ```
> 
> [与 C 版本不同，C 版本抵制了混乱的代码结构，并以正确的输出结束，Python 生成了最初由放错位置的语句引起的语法错误。所以有人可能会说 Python 不如 C++或者 raw C 健壮。](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)
> 
> [具有讽刺意味的是，虽然许多专业程序员认为空白敏感令人讨厌，但许多 Pythonistas 认为处理空白问题比最后必须进行列追踪要好。](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)
> 
> [最后，在处理大块代码时，空格敏感性问题很烦人。然而，如果良好的编码习惯被灌输到您的团队中，空白敏感性问题可以很容易地被克服。](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

# [向后兼容性](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[不支持向后兼容意味着旧版本的 Python 代码可能无法在新版本下工作。换句话说，我们绝对需要查找新版本中出现的语法变化，并相应地重写代码。](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[向后兼容性成为严重问题的一个很好的例子是第一次从 Python 2 到 3 的过渡。](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[Python 核心开发团队认为，人们将 Python 2 代码转换成 Python 3 代码不成问题。但是他们错了。](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[Python 的作者自己也承认了这一点，他说:](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

> [“我们低估了有多少人已经编写了大量的 Python 代码，然后基本上忘记了它是如何工作的。所以，他们没有能力升级它。我们意识到我们有一个问题。”](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[对这个问题的结论是延长 Python 2.7 的生命周期。](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

# [外卖食品](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[编程语言一直是一个热门话题，所以没有对错之分。至少一个人有一些理由喜欢一种语言胜过另一种语言。](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[一般来说，每种通用编程语言都适合特定的人群。Python 的官方作者说:](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

> [“学习 Python 编程比学习 Java 或 Swift 编程容易得多。对于学习计算机科学的专业软件开发人员来说，Java 和 Swift 是很好的编程语言。然后开始在一家软件开发公司工作。但是 Python 可以教给中学的孩子。”](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[除此之外，最好的情况是拥有一种能够结合 C++和 Python 优点的编程语言。](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[如今，最接近这个目标的人是朱莉娅。](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)

[](https://medium.com/u/1e3c943f9aef#include<stdio.h></span><span id=)
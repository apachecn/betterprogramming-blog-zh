# r 和 C++一起:Rcpp 简介

> 原文：<https://betterprogramming.pub/r-and-c-together-an-introduction-to-rcpp-b995b01cdfb6>

## 在研发中实现高绩效

![](img/5d554980d845fe03efc07cccde5f4b38.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

r 是一种漂亮的编程语言，它有很多优点。

不幸的是，性能不是其中之一。R 虽然可读性强，功能多，但并不以性能见长。作为一种动态类型和解释的语言，R 容易出现属于这些类别的语言的所有不足之处。编程语言的性能由许多因素决定。但两个事实是，我们每次运行 R 脚本，都会有一个叫“解释器”的信使来传达和翻译代码。而且每次我们运行 R 脚本时，在翻译过程中指定的每个变量的类型，称为“运行时”，足以使 R 成为一种缓慢的编程语言。

好消息是有很多解药可以克服慢的困难。使用一套合适的工具，我们可以获得显著的性能提升。最合法的方法是从低级编程语言中获得帮助。本文旨在让您开始用 C++重写代码的瓶颈。

# 什么是 C++，为什么它很重要？

C++是世界上最强大的编程语言之一，它无处不在。我们每天使用的成千上万的设备都运行在 C++上。它是另一种著名的编程语言 c 的继承者。

举一点历史的例子，C 语言是由丹尼斯·里奇在 1972 年创造的。当时，C 语言在计算世界引起了一场革命，但是随着时间的推移，它的用户意识到它缺乏其他语言所拥有的一些最好的特性。其中之一是面向对象编程。由于这种认识，丹麦计算机科学家比雅尼·斯特劳斯特鲁普于 1985 年创造了 C++。C++扩展了 C 的功能，增加了面向对象的范例。事实上，C++只是意味着“有类的 C”

几十年来，C++在编程世界中扮演着重要的角色，几乎影响了编程世界的每一个部分。幸运的是，对于我们 R 程序员来说，C++与 R 有着非常密切的联系，因此我们可以从这种联系中受益，并增强我们 R 项目的能力。当您经历 R 之旅时，您会惊讶于少量 C++代码在 R 项目中的作用。

# 扩展 R 的老方法

传统上，R 提供了几个内置工具来与另外两种语言 C 和 Fortran 接口。随着 Fortran 的过时，C 已经成为 R 的首选语言。为此，标准 R 提供了两个函数来连接 R 和 C: `.C()`和`.Call()`。然而，这种传统的方式是如此的过时和痛苦。因此，我们将着眼于一种更加现代和用户友好的方式，在本文中不提及这两个功能。

# Rcpp

帮助我们用 C++美化 R 项目的那片天，是一个叫 Rcpp 的包。Rcpp 是由另一位来自加拿大的计量经济学家 Dirk Eddelbuettel 领导的团队开发的。Rcpp 提供了一个 R 和 C++之间的接口，这个包非常强大，以至于 Springer 出版了一整本书“用 Rcpp 无缝集成 R 和 C++”。

您可以通过运行以下代码开始:

```
library(Rcpp)
```

# 在脚本中将 C++转换成 R

提高 R 脚本性能的最简单快捷的方法是修改脚本中有问题的部分，并用 C++重写。`Rcpp::cppFunction()`功能正是你需要的东西。既整洁又方便。`cppFunction()`取一个 C++函数，转换成 R 函数。你所要做的就是把你的 C++函数作为一个字符串来传递，它会做剩下的事情。

```
Rcpp::cppFunction()
```

为了简单起见，我们将编写两个额外的函数:一个用 R 编写，另一个用 C++编写。这些将说明`cppFunction()`的用法。

下面你可以找到用 R 写的附加函数:

```
addition_r <- function(a , b) {
  a + b
}addition_r(5 , 10)## [1] 15
```

现在我们用 C++写同样的函数，并像用 r 写一样使用它。

```
addition_cpp <- cppFunction('int additioncpp(int a, int b) {
  int sum = a + b ;
  return sum;
}')addition_cpp(5 , 10)## [1] 15
```

这非常简单，可以节省你大量的时间。

尽管对于几百行的脚本来说，使用上面这样快速而肮脏的方法很有诱惑力，但这并不是构建整个项目最理想的方式。大多数 R 项目不是一个脚本，而是由数十个脚本组成。在这种情况下，我们可能希望将整个 C++文件导入到我们的 R 会话中。这是下一个话题的主题。

# 在 R 项目中使用单独的 C++文件

为了使用一个单独的 C++文件的各个部分，我们受益于`Rcpp::sourceCpp()`函数。

```
Rcpp::sourceCpp()
```

而窍门就是，对于每一个你想从 C++文件导入的函数，你都必须加上这个指示符:`// [[Rcpp::export]]`。

下面，你可以看到一个准备好导入的 C++文件的例子。保存为`arithmetic.cpp`:

现在，下面的代码导入了预先指定的`arithmetic.cpp`内容。

```
sourceCpp("C:/Users/ugurc/Desktop/Medium Blog/High Performance in R/arithmetic.cpp")subtractioncpp(a = 10 , b = 5)## [1] 5multiplicationcpp(a = 10 , b = 5)## [1] 50
```

# 结论

每天，在世界各地，R 开发人员创造了成千上万的精彩项目，成千上万的应用程序运行 R 代码，数百万美元的行业使用 R。每当这种美丽的语言短缺时，我们就部署各种解决方案。

应用低级语言是解决这些缺点的最有效的方法。现在，根据本文中的信息，您可以通过用 C++重写 R 应用程序的瓶颈来重新创建 R 应用程序。
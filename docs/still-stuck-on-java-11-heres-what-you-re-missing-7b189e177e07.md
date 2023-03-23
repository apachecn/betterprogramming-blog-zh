# 还卡在 Java 11 上？这就是你错过的

> 原文：<https://betterprogramming.pub/still-stuck-on-java-11-heres-what-you-re-missing-7b189e177e07>

## 超过 60%的开发者希望在未来 12 个月内升级到 Java 17 LTS。了解最新最棒的功能。

![](img/373be10a261f1df5eb959c62819fbe80.png)

图为 [RShunev](https://unsplash.com/@rshunev?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Java 11 已经存在了惊人的时间。它已经是近四年来的长期支持版本(LTS)。尽管新的更新每半年发布一次，但对于许多公司来说，避免这些临时发布并不罕见。相反，他们更喜欢坚持使用更受支持的语言版本。

根据 JRebel 最近发布的《2022 年 Java 开发人员生产力报告》( Java Developer Productivity Report 2022,只有 12%的开发人员在他们的应用程序中使用 Java 12 或更高版本，其中 Java 8 和 11 分别占 37%和 29%(其他 JVM 语言占其余部分)。

> “…只有 12%的开发人员使用 Java 12 或更高版本…”—JRebel 2022

不过，对我们程序员来说，未来看起来确实更光明。截至 2021 年 9 月，Java 17 成为新的长期支持版本。这提供了更多的保证，并使规避风险的企业能够推进他们的技术堆栈。事实上，根据 JRebel 的说法，超过 60%的开发者希望在未来 12 个月内升级到最新的 Java LTS。

过去的几年感觉无穷无尽，很容易忘记 Java 在这段时间里发布了哪些特性。在这篇文章的其余部分，我们将简要地浏览不同的版本，从 11 到 17，并提醒我们自己等待的款待。

不过，在我们开始之前，值得注意的是，我们将主要关注核心语言特性，而不是预览版或实验版。这里的重点是你可以放心地在日常工作中使用的工具，而不是对未来的一瞥。

# Java 11 (LTS)功能概述

与其直入主题——因为这么多开发人员都是从 Java 8 开始的——不如让我们快速回顾一下 Java 11 给了我们什么。

## 用一个命令运行文件

从 Java 11 开始，作为`java` 命令的一部分，可以直接从源文件运行代码，而不必通过写-编译-执行循环来加速代码。

```
$ java RunMe.java
Yoo hoo!
```

对于较大的项目来说不是特别有用，但是如果您想编写小的实用程序、脚本之类的东西，它会很有帮助。

您甚至可以在文件中包含 shebang 头文件，并像运行任何其他脚本一样运行它。

```
#!/usr/bin/java --source 11
```

## 新的字符串方法

String 中增加了一些新的实用方法，包括:

*   `.isBlank` —如果为空或仅包含空白，则返回 true。
*   `.lines` —将字符串转换为由行分隔符分隔的字符串流。
*   `.strip` —从字符串中删除所有前导和尾随空格。
*   `.repeat` —将一个字符串重复 n 次，生成一个新字符串。

## 新文件方法

引入了两种方便的方法——`readString`和`writeString`——直接从文件中读取和写入字符串。不再需要摆弄`InputStreams`或依赖第三方库来实现这一点。

## 集合到数组

一个稍微方便一点的 *toArray* 方法，它采用一个`[IntFunction](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/function/IntFunction.html)`而不是一个数组的实例。

## 非谓词

颠倒谓词的一种简便方法——*n*ot*——*。与*求反*功能相同，但在许多情况下可读性更好(*无效*比*求反(有效)*更容易阅读)

## Lambda 局部变量语法

包括了在 Lambda 中使用 *var* 的能力。

既然可以忽略类型信息，为什么还要麻烦呢？它允许你对论点使用修饰语，比如`@NotNull` *，*，当你不使用任何声明时，这是不可能的。当然，您可以对完整类型使用注释，但这要简洁得多。

```
(@NotNull var b) -> b
```

## 新建 HTTP 客户端

在 *java.net.http* 包中可以找到一个新的内置 HTTP 客户端。

## 飞行记录器

在 Java 11 之前，Java 飞行记录器曾经是 Oracle JDK 的商业部分。现在它已经开源了，是 OpenJDK 的一部分。

这是一种分析应用程序的轻量级方法，并提供关于 JVM 行为的统计数据，包括内存使用、对象分配和垃圾收集。捕获之后，您可以将输出输入到 Java Mission Control 工具中，以可视化您的发现。

# Java 12

## 新的字符串方法

Java 12 首先引入了对 String API 的进一步增强。这些增加的内容似乎集中在即将到来的文本块上(参见 Java 15)。

*   `.indent` —根据给定的偏移量调整每行的缩进，偏移量可以是正的，也可以是负的。
*   `.transform` —将变换函数应用于产生任何类型的字符串。

## 文件不匹配

`Files.mismatch`为我们提供了一个方便的函数，可以在两个文件的比较中找到第一个不匹配的字节。

## 发球收集器

收集器是两个下游收集器的组合。传递给结果收集器的每个元素都由两个下游收集器处理，然后使用指定的合并函数将它们的结果合并到最终结果中。

这里的经典用例是计算平均值。所有的值都被传递给两个下游收集器，一个是 sum，一个是 count，最后的结果汇总在一起(`sum` / `count`)。

## 新数字格式化程序

压缩数字格式化程序提供了一个本地化的、简短的数字表示形式，比如“10K”或“一万”。

# Java 13

我们跳过这个，好吗？Java 13 没有引入任何新的语言特性。

# Java 14

## 切换表达式

Switch 表达式为我们提供了将 switch 语句的结果赋给变量的有用能力。这(希望)是更强大的模式匹配之旅的第一步，就像你在 [Scala](https://docs.scala-lang.org/overviews/scala-book/match-expressions.html) 、 [Rust](https://doc.rust-lang.org/rust-by-example/flow_control/match.html) 或其他函数式/函数式语言中发现的那样。

## 有用的空指针

生产日志中的 NullPointerException 甚至会让最顽强的开发人员感到恐惧。预防胜于治疗(空值可能是一个巨大的错误，但至少 Java 现在在 NPE 的情况下给了我们更详细的错误消息——例如*“不能调用“String.toLowerCase()”，因为“missing”是空值”。*

# Java 15

## 文本块

终于来了！通过支持文本块，Java 已经赶上了几乎所有其他语言。没有更多的 *"…\n" + "…\n"* 链，这些链可以简化构建 DB 查询、测试中的 JSON 块等工作。

## 隐藏类

这是一个有点神秘的问题。Java 15 引入了使用不能被其他类链接或通过反射发现的类的能力。然而，它们非常高效，这就是吸引力所在。主要供使用动态字节码或 JVM 语言(Scala、Groovy、Clojure 等)的人使用。

例如，在 Java 15 中，lambda 表达式使用隐藏类，这使得它们更有效。

## 谢南多厄河

Shenandoah 是一个低暂停时间垃圾收集器，它通过在运行 Java 程序的同时执行更多的垃圾收集工作，以更高的 CPU 利用率为代价来减少 GC 暂停时间。

尽管它已经在许多版本中处于预览状态，但它终于为黄金时间做好了准备。

## ZGC

另一个生产就绪的垃圾收集器，目标与 Shenandoah 相似。Z 垃圾收集器(ZGC)是一个非常可伸缩的低延迟垃圾收集器。ZGC 可以同时执行所有高开销的工作，不会停止应用程序线程的执行超过 10 毫秒，这使得它非常适合要求低延迟和/或使用非常大的堆(高达数 TB)的应用程序。

# Java 16

## 白天

`DateTimeFormatter` 支持新符号 *B* 作为 am/pm 的替代日时段。比如*“上午”*或者*“下午”*。

## Stream.toList

与其用`.collect(Collectors.toList())`把`Stream`变成`List` *，*流 API 引入了一个便利的方法*。为了达到同样的目的。*

## 记录

记录是该语言的另一个重要补充，在某些方面类似于枚举，是一种更严格的类形式，专门促进不变性。

用 Java 创建不可变的数据对象总是有点麻烦。你必须确保所有的字段都被标记为最终的，并且适当的`equals` / `hashCode` / `toString`实现都存在并被检查。

记录为我们提供了一个开箱即用的不可变结构，从而使这变得容易得多。字段不能修改，另外`accessors`、`equals`、`hashCode`和`toString`都是免费提供给你的。

## 实例的模式匹配

这种简单形式的模式匹配为您提供了匹配的对象，正确地输入并准备好，而不是在`instanceof`检查后用强制转换来丢弃您的代码。

## 本地枚举

Java 15 允许我们创建作用于一个方法的枚举。尽管用途可能相当有限，但在函数的上下文中标记数据可能会被证明是有用的。

## 本地接口

与前面的特性相似，我们也可以创建接口，并在方法的上下文中用匿名类实现它们。

# Java 17 (LTS)

## 密封类/接口

Java 17 最大的特点是*密封类*。在某些方面类似于枚举，密封类限制了可以扩展或实现类/接口的类的集合。

枚举和密封类之间的主要区别在于，前者将您限制在一组固定的实例中。密封类更进了一步，它把你限制在可能有多个实例的值的种类上。

例如，一群狮子不能用一个枚举值来表示，因为只有一只狮子。或者，一个密封的接口将允许许多不同的 lions 存在，同时仍然遵守对类型的限制。

这有助于增强语言的类型安全性，并将再次增加 Java 即将推出的模式匹配特性。通过了解可以实现接口的完整类集，编译器可以更有力地保证您已经满足了每一种边缘情况，例如，switch 语句。不再需要使用*默认*处理，因为编译器可以在编译时而不是运行时捕获错误。

## 还原总是严格的浮点语义

这主要是针对科学应用，并使浮点运算始终严格。在 Java 1.2 之前就已经是这种情况了，但是由于硬件架构的挑战，不得不被削弱。现在已经恢复了。

# Java 18 和 LTS 的未来

Java 18 在撰写本文的一周前发布，即 2022 年 3 月 22 日。它提供了成千上万的性能、稳定性和安全性改进，尽管这次没有主要的语言特性(尽管默认情况下*UTF-8*和包含一个*简单网络服务器*)。这不是一个 LTS 版本，但是延续了 Java 快节奏的发布节奏。与往常一样，还包括了许多预览功能，请参见[https://www . Oracle . com/news/announcement/Oracle-releases-Java-18-2022-03-22/](https://www.oracle.com/news/announcement/oracle-releases-java-18-2022-03-22/)了解更多详细信息。

至于 Java 的长期支持版本， [Oracle 最近提议](https://blogs.oracle.com/java/post/moving-the-jdk-to-a-two-year-lts-cadence)将其从目前的 3 年周期加速到更短的 2 年周期。这将导致 Java 21 成为下一个 LTS，而不是 Java 23，并将加快采用的步伐，让我们更多的人比以前更早地开始使用这种语言的最新和最棒的特性。

# 资源

*   [https://www . JRE bel . com/resources/Java-developer-productivity-report-2022](https://www.jrebel.com/resources/java-developer-productivity-report-2022)
*   [https://www.baeldung.com/java-11-new-features](https://www.baeldung.com/java-11-new-features)
*   [https://mkyong.com/java/what-is-new-in-java-11/](https://mkyong.com/java/what-is-new-in-java-11/)
*   [https://www.baeldung.com/java-12-new-features](https://www.baeldung.com/java-12-new-features)
*   [https://www . infoworld . com/article/3301197/JDK-12-the-new-features-in-Java-12 . html](https://www.infoworld.com/article/3301197/jdk-12-the-new-features-in-java-12.html)
*   https://www.baeldung.com/java-13-new-features
*   【https://mkyong.com/java/what-is-new-in-java-13/ 
*   [https://www.baeldung.com/java-14-new-features](https://www.baeldung.com/java-14-new-features)
*   [https://www.techgeeknext.com/java/java14-features](https://www.techgeeknext.com/java/java14-features)
*   [https://www . infoworld . com/article/3534133/JDK-15-the-new-features-in-Java-15 . html](https://www.infoworld.com/article/3534133/jdk-15-the-new-features-in-java-15.html)
*   [https://www.baeldung.com/java-16-new-features](https://www.baeldung.com/java-16-new-features)
*   [https://www . infoworld . com/article/3569150/JDK-16-the-new-features-in-Java-16 . html](https://www.infoworld.com/article/3569150/jdk-16-the-new-features-in-java-16.html)
*   [https://www.baeldung.com/java-17-new-features](https://www.baeldung.com/java-17-new-features)
*   [https://mkyong.com/java/what-is-new-in-java-17/](https://mkyong.com/java/what-is-new-in-java-17/)
*   【https://www . Oracle . com/news/announcement/Oracle-releases-Java-18-2022-03-22/
*   [https://blogs . Oracle . com/Java/post/moving-the-JDK-to-a-two-year-lts-cadence](https://blogs.oracle.com/java/post/moving-the-jdk-to-a-two-year-lts-cadence)
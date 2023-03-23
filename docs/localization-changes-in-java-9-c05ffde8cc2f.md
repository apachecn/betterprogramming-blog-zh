# Java 9 中的本地化变化

> 原文：<https://betterprogramming.pub/localization-changes-in-java-9-c05ffde8cc2f>

## **当日期和时间打破了我们的项目**

![](img/83bbc592e580174244bcd0b3b3a1930f.png)

照片由[克里斯汀·罗伊](https://unsplash.com/@agent_illustrateur?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/world-map?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我们使用`java.util.Locale`来格式化日期、数字、货币等等。但是在某些情况下，这些格式化的字符串随着 [JDK 9](https://www.oracle.com/java/technologies/javase/v9-issues-relnotes.html#JDK-8008577) 发生了变化，导致了大量微妙的(有时不那么微妙的)错误。

```
**TABLE OF CONTENTS**[What Are Locales?](#b698)
[Where Do Locales Come From?](#ea74) [What Changed With JDK 9?](#67e8)
[How to Deal With the Changes](#490a) [Conclusion](#1847)
```

# 什么是区域设置？

为了更好地理解一个问题，我们必须首先知道我们在处理什么。

> *“区域设置是一组参数，用于定义用户的语言、地区以及用户希望在其用户界面中看到的任何特殊变量首选项”——*[*维基百科*](https://en.wikipedia.org/wiki/Locale_(computer_software))

区域设置的核心用途是格式化多种不同的数据以供输出:

*   数字
*   日期、时间、工作日、纪元等。
*   货币
*   字符串排序
*   等等。

区域设置由属性层次结构定义:

```
<language>[_<COUNTRY>[_<variant>]]language:  ISO 639 (mandatory)
COUNTRY:   ISO 639 (optional)
variant:   any string (optional)
```

这三个参数足以定义大多数可能的组合。通常一种语言就足够了，但有时我们需要更具体一些。变体用于进一步指定语言环境:

```
Locale.**GERMAN**        => de
Locale.**GERMANY**       => de_DELocale.**ENGLISH**       => en
Locale.**UK**            => en_GB
Locale.**CANADA**        => en_CALocale.**FRENCH**        => fr
Locale.**CANADA_FRENCH** => fr_CA
```

我们不限于预定义的设置，还支持自定义语言环境:

# 语言环境来自哪里？

一个公认的语言环境来源是“Unicode 公共语言环境数据库”( [CLDR](http://cldr.unicode.org/) )。它是世界上最大和最广泛的可用区域数据库，被操作系统、谷歌浏览器、MediaWiki 等使用。

但是就像任何标准一样，它不是语言环境的唯一来源。

![](img/2ce240e4d5f4bf4aa4a0812e2d827654.png)

XKCD:标准—[https://xkcd.com/927/](https://xkcd.com/927/)

## JDK 默认区域设置提供程序

Java 从`[LocaleProviderAdapter](https://hg.openjdk.java.net/jdk9/jdk9/jdk/file/tip/src/java.base/share/classes/sun/util/locale/provider/LocaleProviderAdapter.java)`中检索语言环境。在 JDK 8 之前，默认的提供者是 JRE，它有自己的区域设置定义列表。

JRE 不是 JDK 唯一的供应商:

*   JRE (Java 自己的列表)
*   CLDR (Unicode 公共语言数据库)
*   SPI(服务提供商接口)
*   主机(由操作系统提供)

# JDK 9 号有什么变化？

[JEP 252](https://openjdk.java.net/jeps/252) 从 JDK 9 开始，将默认提供者从 JRE 改为 CLDR。这种变化导致微小的差异，最终可能导致大问题。

这些是我们升级到 JDK 11 号时遇到的问题。

## 不同的日期格式

对我们来说，第一个突破是用 Google 的 [GSON](https://github.com/google/gson) 来(反)序列化 JSON 数据。我们为 [Mailchimp](https://mailchimp.com/) 用户使用定制的同步服务，保存相关数据。在 JDK 11 中，我们不再能够读取以前持久存储的数据。

在构建了一些测试场景之后，我们发现了日期格式中的一个细微差别:

```
Before: Jul 15, 2020 2:20:32 AM
After:  Jul 15, 2020, 2:20:32 AM
```

注意到年份后面的逗号了吗？这是从哪里突然冒出来的？

事实证明， [CLDR](http://cldr.unicode.org/) 定义的一些格式与以前的 JDK 略有不同。

## 一周什么时候开始？

另一个问题来自一周的第一天。

我们在大多数系统中使用的是 Apache Tapestry，为了最大的兼容性，我们使用了“纯语言”的语言环境。这意味着我们使用的是`Locale.GERMAN` (de)而不是`Locale.GERMANY` (de_DE)。

之前没有任何问题；所有内容都按照预期的样子进行了格式化。正如所料，一周的第一天是星期一。

在我们从 JDK 时间 8 点转换到 11 点后，我们的约会对象从周日开始。通过相关代码的调试发现，语言环境仍然是`Locale.GERMAN`。但是一周的开始日期被返回为星期日。

发生了什么事？

CLDR 和 JRE provider 之间的一个微妙区别是不同种类的格式如何与一个地区的特殊性相关联。JRE 提供了周一作为`Locale.GERMAN`一周的开始日，尽管它不应该是！

CLDR 没有一周的开始日，这仅仅是因为日历不应该被视为语言相关的。相反，一个地区(或者用 Java 术语来说，一个“国家”)负责定义日历，包括一周的开始日。因此，我们的“纯语言”德语地区突然回落到默认的国家/地区 001: Sunday。

## 货币格式

我们有一个简单的货币格式单元测试:

它以前运行良好，但现在失败了，因为货币格式发生了重大变化:

```
¤ = Unicode Currency Symbol Placeholder JDK 8      | JDK 9+
------------|----------------- 
 ¤ 1.234,99 | 1.234,99\u00a0¤
```

就像本周初一样，问题出在“纯语言”地区。但这一次，情况正好相反。

CLDR [确实为基于`de`的语言提供了默认的格式](https://unicode-org.github.io/cldr-staging/charts/37/by_type/numbers.number_formatting_patterns.html)，有更具体的模式，例如`de_AT`。另一方面，JDK 没有针对`de`的模式，所以它退回到默认模式。

更糟糕的是，一个“不间断空格”现在被用来分隔值和符号。

如果我们在 JDK 8 号上使用`Locale.GERMANY`而不是`Locale.GERMAN`，我们会得到和 JDK 11 号几乎一样的结果。

# 如何应对这些变化

既然我们在格式代码中发现了多个重大变化，我们应该如何处理它们呢？

## 更新我们的依赖性

修复我们的 GSON 问题就像更新依赖关系一样简单。这个问题在 [2.8.3](https://unicode-org.github.io/cldr-staging/charts/37/by_type/numbers.number_formatting_patterns.html) 中得到修复，我们使用的是 2.8.2。在 Maven 依赖地狱中过了一段时间后，(反)序列化再次完美地工作了。

## 适应变化

如果可能的话，我们应该尽可能多地接受代码中的变化——CLDR 的语言环境定义比以前更加准确和深思熟虑。

使用许多不同系统使用的定义明确的标准，我们可以确保更好的互操作性，并且更面向未来。如果有必要，我们仍然可以将不可修复的代码包装在我们自己的兼容性包装器中，或者自己提供不同的日期格式。

## 兼容模式

在软件开发的世界里，技术上正确有时是不可能的。想想遗留代码、不可替代的依赖、我们必须与之交互的封闭系统等等。为什么我们不能仅仅适应特定的变化，原因有很多。

但是如果我们不能适应它们，JDK 会支持我们:

```
java -D*java.locale.providers=<order of providers>*Available providers:
- CLDR (default)
- HOST (OS provided)
- SPI (Service Provider Interface)
- COMPAT (formerly JRE)
- JRE (alternative to JRE, but disfavored over COMPAT)
```

通过指定区域设置提供者的查找顺序，我们可以获得与以前相同的结果，而无需更改一行代码。这可能是最快的修复方法，但是依赖兼容模式很可能会成为一个问题。

# 结论

查看 [JDK-8008577](https://bugs.openjdk.java.net/browse/JDK-8008577) 、 [JDK-8145136](https://bugs.openjdk.java.net/browse/JDK-8145136) 及其相关子报告中的错误，似乎切换默认提供商的风险和影响并没有得到应有的重视。

其实，那其实挺好理解的。根据您使用的语言环境，您甚至可能不会受到任何问题的影响。

在我看来，我们应该尽可能地适应变化，否则从长远来看，任何修复都会变成技术债务。JDK 9 是一个大规模的发布，有许多突破性的变化，所以它不太可能在一个更大的项目中一切都开箱即用。修正基于区域设置的格式应该明确地列在我们的任务清单上。

# 资源

*   [语言环境(电脑软件)](https://en.wikipedia.org/wiki/Locale_(computer_software))(维基百科)
*   [JDK 9 发布说明](https://www.oracle.com/java/technologies/javase/v9-issues-relnotes.html#JDK-8008577)(甲骨文)
*   [JEP 252:默认使用 CLDR 地区数据](https://openjdk.java.net/jeps/252) (OpenJDK)
*   [JDK 9 中的国际化增强功能](https://docs.oracle.com/javase/9/intl/internationalization-enhancements-jdk-9.htm#JSINT-GUID-AF5AECA7-07C1-4E7D-BC10-BC7E73DC6C7F)(甲骨文)
*   [Unicode 公共区域数据库](http://cldr.unicode.org/)
*   [ISO 639](https://en.wikipedia.org/wiki/ISO_639) (维基百科)
*   [ISO 15924](https://en.wikipedia.org/wiki/ISO_15924) (维基)
*   [ISO 3166](https://www.iso.org/iso-3166-country-codes.html) (维基)
*   UN M.49 (维基百科)
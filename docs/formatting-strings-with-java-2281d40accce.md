# 用 Java 格式化字符串

> 原文：<https://betterprogramming.pub/formatting-strings-with-java-2281d40accce>

## 不仅仅是 String.format

![](img/e5c20a61a6f827c346959770d8cfb7e8.png)

[Jelleke Vanooteghem](https://unsplash.com/@ilumire?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/letters-on-wall?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我们都知道`[**String**.format](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#format(java.lang.String,%20java.lang.Object...))`，但是还有其他选择。Java 有多种格式化、对齐、填充和调整字符串的方法。

# 字符串格式化的历史

编程语言有格式化字符串的悠久传统。

60 多年前，`[Fortran](https://en.wikipedia.org/wiki/Fortran)`以`FORMAT`关键词发布:

```
 WRITE OUTPUT TAPE 6, 601, IA, IB, IC, AREA
601  FORMAT (4H A= ,I5,5H  B= ,I5,5H  C= ,I5,
   &         8H  AREA= ,F10.2, 13H SQUARE UNITS)
```

更广为人知的`C`风格的格式字符串，由`[printf](https://en.wikipedia.org/wiki/Printf_format_string)`使用，而 Java 的`[**String**.format](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#format(java.lang.String,%20java.lang.Object...))`起源于`[BCPL](https://en.wikipedia.org/wiki/BCPL)`:

```
WRITEF("%I2-QUEENS PROBLEM HAS %I5 SOLUTIONS*N", NUMQUEENS, COUNT)
```

Java 不支持`[**String**.format](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#format(java.lang.String,%20java.lang.Object...))`。直到 Java 1.5 发布，它才被包含进来。在此之前，`[java.text.MessageFormat](https://docs.oracle.com/javase/7/docs/api/java/text/MessageFormat.html)`是让弦向你的意志弯曲的方式。

# 格式指定符

所有格式字符串都以`%`开头，由多个可选部分和实际转换说明符组成。

通用语法可以分为三个不同的组:

*   常规、字符和数字类型`%[argument_index$][flags][width][.precision]conversion`
*   日期和时间`%[argument_idnex$][flags][width][.precision]conversion`
*   无论据说明符`%[flags][width]conversion`

转换参数也可以分成不同的组:

大多数转换都支持小写和大写，通过使用小写或大写的转换说明符来区分。为简单起见，本文只列出小写版本。

如果提供了一个`null`参数，它将被转换为`"null"`。

## 一般转换

示例:

## 特性

## 积分

示例:

## 浮点型

示例:

## 日期/时间

日期和时间格式说明符以`%t`为前缀，后跟特定部分。

许多转换是特定于语言环境的，如果没有提供替代语言，则默认为 JVM 的默认语言环境。

## 其他转换

## 旗帜

标志可用于修改转换:

## 宽度

输出的绝对宽度可以是特定的，但行分隔符转换除外:

## 精确

该行为取决于转换类型。

对于常规参数类型，精度是结果格式化字符的数量。

浮点转换(`%a`、`%e`、`%f`)限制小数点后的位数。

一个特例是`%g`，它将定义舍入后的结果幅度的位数。

对于其他转换(字符、整数、日期/时间、百分比和行分隔符转换)，指定精度**不**适用，并将抛出异常。

## 参数索引

*自变量索引*表示自变量在自变量列表中的位置，例如`1$`表示第一个，`2$`表示第二个，依此类推。

这样，我们可以使用不同的格式说明符，而不用改变实际的参数列表。

如果我们想要重用之前使用的参数位置，我们可以使用`<` ( `\u003c`)来代替:

# 格式选项

## java.lang.String.format

可用的最简单的格式化选项是这两个静态方法:

```
String **format**(String format,
              Object... args);String **format**(Locale l,
              String format,
              Object... args);
```

实际上，这些只是将工作委托给`[java.util.**Formatter**](https://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html)`:

如果没有提供`[java.util.**Locale**](https://docs.oracle.com/javase/7/docs/api/java/util/Locale.html)`，则使用 JVM 默认值。

## java.util.Formatter

`[java.util.**Formatter**](https://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html)`类是`[printf](https://en.wikipedia.org/wiki/Printf_format_string)`格式说明符的实际解释器，并且在我们的格式需求中做了所有繁重的工作。

在内部，这一切都归结于`[java.lang.**Appendable**](https://docs.oracle.com/javase/7/docs/api/java/lang/Appendable.html)`接口和一个`[java.util.**Locale**](https://docs.oracle.com/javase/7/docs/api/java/util/Locale.html)`。

通过它的多个构造函数，`[java.util.**Formatter**](https://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html)`可以为格式化的结果设置不同的目标:

我们从`[java.lang.**String**](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html)`中知道的两个`format`方法都存在，但是没有返回新的字符串，而是返回了`Formatter`实例。

根据支持，`[java.lang.**Appendable**](https://docs.oracle.com/javase/7/docs/api/java/lang/Appendable.html)`附加调用`[flush()](https://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html#flush())`和`[close()](https://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html#close())`。

## System.out.printf

方法`System.out.**printf**`在内部使用一个`[java.util.**Formatter**](https://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html)`，就像`[java.lang.**String**](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html)`一样。

最大的不同是呼叫被同步到`[System.out](https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#out)`，这是一个`[java.io.**PrintStream**](https://docs.oracle.com/javase/7/docs/api/java/io/PrintStream.html)`。

# Java . text . message 格式

我们现在已经了解了使用`[printf](https://en.wikipedia.org/wiki/Printf_format_string)`格式说明符的不同方法，但是 Java 有一种更基于自然语言的格式化字符串的方法。

大括号中使用了更简单的说明符，而不是相当隐晦的格式字符串:

`{argument index , format type , style }`

参数索引是必需的。格式类型和样式是可选的，但是样式不能单独使用。它必须始终遵循格式类型。

## 格式类型和相应的样式

不同的类型支持不同的样式:

**类型:** `none`

**类型:**

**类型:**

除了这些预定义的样式，我们还可以提供自己的子格式模式:

示例:

# 结论

现在我们知道了格式化字符串的不同方法。选择哪一个取决于我们的要求。`[java.text.**MessageFormat**](https://docs.oracle.com/javase/7/docs/api/java/text/MessageFormat.html)`对眼睛来说更容易，尤其是如果你对`printf`不流利的话。但是`String.format`被广泛使用和理解，所以我们不能出错。并且`printf`格式说明符不仅仅被 Java 使用，所以我们学习了一个更通用的技能。

# 资源

## Java 文档(Oracle)

*   `[java.lang.**String**](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#format(java.lang.String,%20java.lang.Object...)`
*   `[java.util.**Formatter**](https://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html)`
*   `[java.text.**MessageFormat**](https://docs.oracle.com/javase/7/docs/api/java/text/MessageFormat.html)`

## 其他的

*   `[printf](https://en.wikipedia.org/wiki/Printf_format_string)` [格式字符串](https://en.wikipedia.org/wiki/Printf_format_string)(维基百科)
*   `[java.util.**Formatter**](https://www.baeldung.com/java-string-formatter)`指南(Baeldung)
*   [用](https://www.baeldung.com/java-printstream-printf) `[printf](https://www.baeldung.com/java-printstream-printf)` (Baeldung)格式化
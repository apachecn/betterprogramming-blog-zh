# Java 13 特性:开关增强和文本块

> 原文：<https://betterprogramming.pub/java-13-features-switch-enhancements-and-text-blocks-3be53a6d7ed3>

## 最有趣、最易于使用的新功能

![](img/8ce64b108fc9bd0b0f94a2dbdd2e6605.png)

在 [Unsplash](https://unsplash.com/s/photos/thirteen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Franck V.](https://unsplash.com/@franckinjapan?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

新的 Java 来了！

在过去的几年里，这是非常频繁的新闻。我仍然不习惯一年两次而不是每五次发布新的 Java。

我认为在这个快速发布周期中最好的事情是它唤醒了沉睡的 Java 社区，使它比前些年更加活跃。当然，其数量甚至还没有接近在线社区之王 JavaScript，但随着更频繁的发布周期，事情似乎正在朝着正确的方向发展。这对 Java 或全栈开发人员来说绝对是个好消息。

我喜欢用 Java 进行敏捷开发的想法，每六个月发布一个小版本，增加一些新的特性和修改。它让语言再次变得有趣和令人兴奋，并证明了你不需要革命性的改变(像 Java 8 中的 streams 和 lambdas)来证明一个发布的合理性。大量的小改进也一样好。

让我们来看看 Java 13 的新特性和改进，比如使用开关表达式的新方式和原本为 Java 12 设计的文本块特性。

# 切换表达式增强功能

*Switch 表达式*在 Java 12 中作为预览功能引入。在过去的几个月里，社区向 Oracle 发送了反馈，现在在 Java 13 中，他们发布了一些程序员要求的改进。

让我们快速回顾一下 Java 12 中引入的 switch 表达式，然后谈谈 Oracle 为这个版本所做的改进。

是一个容易出错的组件，通常很少在日常开发中使用。必须在每个案例的结尾键入`break;`看起来总是像样板文件。Java 12 通过做两件事解决了这个问题:它使`switch`成为一个表达式(不仅仅是一个语句)，这意味着它现在返回一个可以赋给变量的值，或者作为一个返回语句，或者任何你能想到的其他用例。Java 12 做的第二件事是，当你使用新语法`case ->`而不是`case:`时，通过使`switch`而不是在情况之间穿越，来消除烦人的`break;`。

当然，每个新的 Java 版本都是向后兼容的，使用旧的语法会给你带来旧的失败行为。没有遗留代码被破坏或改变行为。

```
// Legacy Switch Statement
private String javaReleaseDate(int version) {
  String result = "TBD";
  switch (version){
    case 12:
      result = "19.3";
      break;
    case 13:
      result = "19.9";
      break;
  };

  return result;
} // Arrow Syntax Switch Expression // no fall-through
private String javaReleaseDate(int version) {
  return switch (version) {
    case 12 -> "19.3";
    case 13 -> "19.9";
    default -> "TBD";
  };
}
```

由于社区对 switch 表达式的反馈，在 Java 13 中，如果你想使用`switch`作为表达式而不是语句，并且你想使用冒号语法，关键字‘break’被替换为关键字`yield`。这一改变消除了混淆，并使您在使用`switch`作为表达式或陈述时变得明显，从而减少了无意中使用您不想要的东西的可能性。`yield`语句与`return` 语句非常相似；当`case`匹配时，它返回当前分支结果，没有遗漏。

```
// Switch Expression using yield over break
private  String javaReleaseDate(int version) {
  return switch (version){
    case 12: yield "19.3";
    case 13: yield "19.3";
    default: yield "TBD";
  };
}
```

这个特性还在预览版中，所以在未来的 Java 版本中，我们可能会看到进一步的变化和改进。

# 文本块

*文本块*只是 Java 12 即将推出的巨大功能“原始字符串”的一小部分，但由于社区反馈提出了太多重要的问题，该功能被推迟了。

文本块允许更好地编写，更重要的是读取 Java 代码中的多行文本。这是 Java 所渴望的，尤其是因为运行在 Java 虚拟机上的其他语言，如 [Kotlin](https://kotlinlang.org/) 和 [Scala](https://www.scala-lang.org/) ，已经支持多行文本很长时间了。

文本块解决的主要问题是在 Java 代码中编写其他语言的代码。以前，您总是需要像`/n`这样的样板文件，以便在每一行的末尾换行，这使得代码容易出错并且难以阅读。

文本块为您提供了一种在 Java 中编写多行文本的更好方法。它们使用三重引号作为分隔符，可以用在常规字符串可以用的任何地方。

```
// Multi line text with regular String
String html = "<html>\n" +
            "   <title>\n" +
            "     Text Blocks\n" +
            "   </title>\n" +
            "   <body>\n" +
            "     <p>...</p>\n" +
            "   </body>\n" +
            "</html>\n"; // With Text Blocks  
String html = """
              <html>
                <title>
                  <p>Text Blocks</p>
                </title>
                <body>
                  <p>...</p>
                </body>
              </html>""";
```

Java 编译器会把它编译成常规的字符串，如果它原本是字符串或文本块，将不会有任何提示。

请注意，文本块不能在一行中使用:左引号后面必须跟一个行结束符，否则代码将无法编译。

Java 13 中有更多的变化。如果你看一下官方的[变更日志](https://www.oracle.com/java/technologies/javase/13-relnote-issues.html)，你会看到一个相当大的新特性列表，但是也有不赞成和将要不赞成的组件。

对于开发人员来说，我们刚刚提到的两个是最有趣的，也是您可以立即使用的。请记住，这两个功能都处于预览状态，可能会在功能发布中发展。
# 用于 console.log 的 IntelliJ Live 模板将改善您的日常开发生活

> 原文：<https://betterprogramming.pub/intellij-live-template-for-console-log-that-will-improve-your-daily-development-life-ef1320a8fe81>

## 出于免费的故事？在[https://aiko.dev/intellij-live-template-for-console-log/](https://aiko.dev/intellij-live-template-for-console-log/)免费获得这篇文章

## 我们最常见的任务之一的便捷快捷方式

![](img/4c43c42bc8d856e8621a2a0775ca6e08.png)

由[布莱克·康纳利](https://unsplash.com/@blakeconnally?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

(这篇文章的更新版本可以在[这里](https://aiko.dev/intellij-live-template-for-console-log/)找到)

我们都经历过。我们的代码没有像我们希望的那样运行，原因也不清楚。获得正在发生的事情的线索的最简单和最快的方法是简单地在代码中放一个 print 语句。不好听；我们应该使用适当的调试器，但我们还是这样做了。

以下 live 模板会将 console.log(' ')语句添加到您的代码中；自动在日志消息中插入类名、函数名和行号；为您要记录的消息提供代码完成功能；把光标放在你需要的地方。

根据 Perl 编程语言的原作者拉里·沃尔的说法，程序员的三大美德之一是懒惰。

所以让我们把无聊的东西自动化吧！

下面有一个视频展示了实时模板的设置和使用。您可以暂停和跳过视频的某些部分，或者按照下面的说明进行操作。

# **后续步骤**

实时模板首选项:

*   前往偏好设置(⌘ +，)。
*   选择编辑器/实时模板(可以输入*实时模板*查找)。
*   选择 Javascript。
*   按下窗口右上角的“+”号(⌘ + N)。
*   选择实时模板(按 1 或 Enter)。

编辑模板:

*   输入要分配给模板的缩写。在这种情况下，我们将使用 *cl* 。
*   输入模板的描述(例如“inserts console.log(')”)).
*   粘贴以下模板代码:

```
console.log('Class: $CLASS$, Function: $FUNCTION$, Line $LINE$ $PARAM_TEXT$($EXPECTED$): '
, $PARAM$);$END$
```

*   单击模板文本字段下方的定义，并选择该模板应适用的上下文(例如 JavaScript、Typescript 等)。)

编辑变量(随意添加或删除你(不)需要的模板变量。):

*   单击编辑变量。
*   分别选择“jsClassName()”、“jsMethodName()”、“lineNumber()”作为类、函数、行的表达式。
*   添加“PARAM”作为 PARAM_TEXT 的默认值。
*   如果为这些变量定义了跳过，请选中跳过。
*   对变量进行排序，使 PARAM 位于 PARAM_TEXT 和 EXPECTED 之前。

现在键入 *cl* 并按 Enter 键(或者 Tab 键，如果这是您的默认设置)。Tada！我们的模板已经有了行号、类和函数名，光标正等待您键入第二个参数，该参数将立即出现在日志消息的字符串中。按 Enter 键，光标将跳到下一个未定义的参数。键入一些内容，再次按回车键，就到了行尾。

这就是你的日志声明:

# **解说**

$PARAM$是要记录的表达式的占位符。

由于默认值绑定，$PARAM_TEXT$将被替换为此表达式。

$CLASS$，$FUNCTION$，$LINE$将替换为指定的类名、方法名和行号。

$END$表示设置每个变量后光标所在的位置。

变量的顺序很重要，因为它定义了按 Enter 键后光标跳转到下一个位置的顺序。这种情况下的特殊之处在于，游标将跳过 PARAM_TEXT，因为它之前是通过 PARAM 定义的。

这里使用的机制可以应用于许多其他用例，比如定制日志记录器或其他通用语言特性。可以分配给模板变量的一些其他有用的表达式是 complete()和 completeSmart()，这允许您在字符串中使用自动完成功能。

我很想在评论区看到你最喜欢的用例及例子。
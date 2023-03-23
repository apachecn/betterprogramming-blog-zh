# Java 15 特性:密封和隐藏类、模式匹配和文本块

> 原文：<https://betterprogramming.pub/java-15-features-sealed-and-hidden-classes-pattern-matching-and-text-blocks-38f4efdc8adc>

![](img/0406f2d551aac87ca42fc07c3c6f113a.png)

由 [Alexander Sinn](https://unsplash.com/@swimstaralex?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

自从疫情于 3 月中旬在美国和欧洲开始以来，时间似乎过得很快，就在 Java 14 发布的时候，现在，经过六个月的隔离，Java 15 终于来了！

15 是另一个特性版本，16 也是，下一个 LTS(长期支持版本)计划是 Java 17，将在一年后的 2021 年 9 月发布。

这个版本给我们带来了新的预览功能，比如密封类，接下来我们有记录的第二次预览和 instanceof 的模式匹配。当然，我们已经准备好使用预览，文本块和隐藏类。

大约一年前，我在我的 [Java 13 博客](https://medium.com/better-programming/java-13-features-switch-enhancements-and-text-blocks-3be53a6d7ed3)中写了关于文本块的文章，当时它们是新的预览功能，在过去的两个版本中它们经过了测试，Java 团队从社区中收集了所有的反馈，现在它们终于可以投入生产了。

让我们先来看看新的预览功能！

# 密封类

这是一个巨大的变化，Java 在 25 年前的 1.0 版本中遇到的一个问题现在终于得到了解决。

当我们创建新的类和接口时，一个常见的过程是决定我们应该使用哪个范围修饰符，它总是一种情况一种情况的，直到现在语言提供的选项还不够细粒度。

当你有一个官方风格指南不鼓励使用默认(包私有)范围修饰符的项目时，这个问题会更加普遍。我的项目目前就有这种情况，让类(需要在某个时候继承)成为公共的或受保护的有时太慷慨了。

有了 Java 15，这个问题就解决了，现在我们有了对类和接口使用`sealed`范围修饰符的细粒度继承控制。

因此，如果你需要你的超类是可广泛访问的，但不是任意扩展的，那么 sealed modifier 就是你的朋友。

来自 [JDK 文档](https://openjdk.java.net/jeps/360)的示例:

```
package com.example.geometry;

public **sealed** class Shape
    **permits** Circle, Rectangle, Square {...}
```

如果在同一个源文件中有嵌套类或者不止一个类，那么可以省略这些类的`permits`，只要它们在同一个文件中，Java 编译器就会推断出允许的子类。

这意味着在上面的例子中，只要 Shape、Circle、Rectangle 和 Square 在同一个源文件中，你就可以省略`permits`,你的代码可以是这样的:

```
package com.example.geometry;

**sealed** class Shape {...}
... class Circle    extends Shape {...}
... class Rectangle extends Shape {...}
... class Square    extends Shape {...}
```

这可能是后一种情况，但是如果你想通过不写上面例子中的`permits`来节省几个字节，你可以这样做，编译器会知道该怎么做。

允许的类别可以是`final`、`sealed`或`non-sealed`。

```
package com.example.geometry;

public **sealed** class Shape
    **permits** Circle, Rectangle, Square {...}

public **final** class Circle extends Shape {...}

public **sealed** class Rectangle extends Shape 
    **permits** TransparentRectangle, FilledRectangle {...}
public **final** class TransparentRectangle extends Rectangle {...}
public **final** class FilledRectangle extends Rectangle {...}

public **non-sealed** class Square extends Shape {...}
```

Final 将阻止它在类层次结构中的部分被进一步扩展。

Sealed 将允许它的层次结构部分比最初定义的超类进一步扩展，但只能由 sealed subclass 允许的类来扩展。

最后`non-sealed`将把东西放回野外，并把类层次结构恢复为对未知子类的扩展开放，当然从子非密封类开始，超类仍然是密封的，所以它仍然是未知扩展的禁区。

关于这一点，`non-sealed`是(我相信)Java 中第一个有连字符的关键字，我不知道他们为什么在语言中引入这种不一致，已经有两个单词的关键字，比如 instanceof，他们不使用连字符，所以我认为他们应该保持一致，使用`nonsealed`

Java 的反射 API 也被扩展以增加对密封类的支持，有两个新的方法，它们都是不言自明的:

```
java.lang.constant.ClassDesc[] **getPermittedSubclasses**();boolean **isSealed**()
```

您应该密切关注密封类/接口特性，因为在未来的 Java 版本中，这将非常适合开关表达式中的模式匹配。

# 隐藏类

隐藏类是框架开发人员最感兴趣的特性。

它允许它们创建不能被其他类的字节码直接使用的类，它们是供框架本身使用的，框架在运行时生成类并通过反射间接使用它们。

关于这个功能背后的全部动机，你可以阅读[开放 JDK 文档](https://openjdk.java.net/jeps/371)，但这里是它的要点。

定义类`ClassLoader::**defineClass**`和`Lookup::**defineClass**`的标准 API 不关心类的字节码是动态生成的(运行时)还是静态生成的(编译时)。这些 API 总是定义一个*可见的*类，每当同一个加载器层次结构中的另一个类试图链接一个名为的类时，[将被使用。](https://docs.oracle.com/javase/specs/jvms/se14/html/jvms-5.html#jvms-5.3.2)

因此，如果一个标准的 API 可以定义*隐藏的*类，这些类是不可发现的，并且具有有限的生命周期，那么 JDK 内部和外部的动态生成类的框架可以定义隐藏的类，这些隐藏的类具有更低的可见性和开销，这将提高所有基于 JVM 的语言实现的效率。

增加这个特性也允许弃用[非标准 API](https://blogs.oracle.com/jrose/anonymous-classes-in-the-vm) `sun.misc.Unsafe::**defineAnonymousClass**`。隐藏类不会支持`defineAnonymousClass`所做的每一项功能，这不是我们的目标，但我们的目标是弃用该 API，并在即将发布的版本中完全移除它。

我们不会深入隐藏类，因为这主要适用于开发底层内部东西的框架开发人员，所有其他相关信息可以在[官方 JEP](https://openjdk.java.net/jeps/371) 找到。

# 实例的模式匹配(第二次预览)

这最初是作为 Java 14 中的预览特性引入的。Java 15 中的第二个预览版没有任何新的变化，功能与 Java 14 中引入的一样。

这次重新预览的主要目的是从社区获得更多的反馈。

我们都熟悉习语实例，它被广泛应用于所有更大的代码库:

```
if (obj instanceof String) {
    String s = (String) obj;
    s.contains("Java 13");
} else {
    ...
}
```

instanceof 的模式匹配提供了一种更干净的方式来进行这种检查，`instanceof`操作符现在被扩展来接受类型测试模式，而不仅仅是类型:

```
if (obj instanceof String **s**) {
    **s**.contains("Java 15 with Pattern Matching");
} else {
    ...
}
```

不需要在`true`块中进行显式转换，它比以前的代码更少，可读性更好。

# 记录(第二次预览)

记录是作为预览特性在 Java 14 中引入的。

这是一个重新预览版，包含了基于以前(Java 14)预览版的社区反馈的更改。

records 的最终目标是使开发人员能够比以前更容易地用 Java 创建不可变的、数据驱动的结构。

记录是一种新的类，它的声明由名称、标题和主体组成。标题列出了记录的组成部分，即构成其状态的变量。

```
**record** Point(int x, int y) { }
```

所有变量，在这种情况下是`x`和`y`，将自动拥有一个与该变量同名且返回类型相同的公共访问器。

在记录体中，您可以添加任意方法来操作记录状态。

反射 API 已更新，因此您可以操作记录类型类:

```
// return all variables from the header
RecordComponent[] **getRecordComponents**();// is given class declared as a record
boolean **isRecord**();
```

记录是用少量代码在 Java 中创建数据类的一种简洁方式。所有记录都是最终的，他们不能玩继承游戏。

# 文本块

在 Java 12 中作为预览开始的东西现在已经成为永久特性了！

文本块允许更好地编写，更重要的是读取 Java 代码中的多行文本。这是 Java 所渴望的，尤其是因为运行在 Java 虚拟机上的其他语言，如 [Kotlin](https://kotlinlang.org/) 和 [Scala](https://www.scala-lang.org/) ，已经支持多行文本很长时间了。

文本块解决的主要问题是在 Java 代码中编写其他语言的代码。以前，您总是需要像`/n`这样的样板文件，以便在每一行的末尾换行，这使得代码容易出错并且难以阅读。

文本块为您提供了一种在 Java 中编写多行文本的更好方法。它们使用三重引号作为分隔符，可以用在常规字符串可以用的任何地方。

```
// SQL example// Using "one-dimensional" string literals
String query = 
    "SELECT \"EMP_ID\", \"LAST_NAME\" FROM \"EMPLOYEE_TB\"\n" +
    "WHERE \"CITY\" = 'INDIANAPOLIS'\n" +
    "ORDER BY \"EMP_ID\", \"LAST_NAME\";\n";// Using a "two-dimensional" block of text
String query = """
               SELECT "EMP_ID", "LAST_NAME" FROM "EMPLOYEE_TB"
               WHERE "CITY" = 'INDIANAPOLIS'
               ORDER BY "EMP_ID", "LAST_NAME";
               """;
```

请注意，文本块不能在一行中使用:左引号后面必须跟一个行结束符，否则代码将无法编译。

Java 编译器会将文本块编译成常规的字符串，并且不会提示它原来是字符串还是文本块。

很棒的功能，对吧？

Java 正全速进行这个六个月的发布时间表，15 几乎还没有发布，我已经想讨论一下 16 和下一个 LTS 17 会带来什么。写 Java 代码的大好时机！

你可以在他们的官方网站[上阅读完整的发行说明并亲自尝试 JDK 15。](https://openjdk.java.net/projects/jdk/15/)
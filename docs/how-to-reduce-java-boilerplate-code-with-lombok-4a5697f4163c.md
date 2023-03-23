# 如何用 Lombok 减少 Java 样板代码

> 原文：<https://betterprogramming.pub/how-to-reduce-java-boilerplate-code-with-lombok-4a5697f4163c>

## 用 Lombok 编写更少的样板 Java 代码

![](img/fab0fdc5b2b83299414f4c80068efcdf.png)

[亚辛·穆罕默迪](https://unsplash.com/@yassimmo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

Java 是一种广泛使用的编程语言。这有很多原因:Java 非常稳定，有一个很大的 Java 生态系统，许多开发人员将 Java 作为他们的第一编程语言学习(例如在大学)。我也是从 Java 开始的。首先是 Java 基础知识，然后是 Android 应用程序的 Java，最后是服务器端 Java。

对 Java 的一个常见抱怨是，你需要编写大量代码来实现目标。如果您有使用 Python 或 JavaScript 等语言的经验，那么编写 Java 代码有时会感觉有点乏味。*样板*是一个术语，用来描述在一个应用程序的许多部分中几乎没有改变地重复的代码。对于典型的 Java 项目来说，将数百行代码用于定义简单数据类所需的样板文件并不少见。这些类通常包含许多字段、这些字段的 getters 和 setters 以及`equals`和`hashCode`实现。

如果你开始一个新项目，那么你可以选择一种需要更少代码来完成事情的语言。然而，在更大的项目中，你不能只是切换到另一种编程语言。一位同事建议我们应该考虑使用 Project Lombok 在 Java 项目中编写更少的样板代码。最近，我们基于 Java 创建了一个新的微服务，所以我认为这是一个尝试 Lombok 项目的好机会。

# 迎接龙目岛计划

Project Lombok (简称 Lombok)旨在通过使用一组简单的[注释](https://en.wikipedia.org/wiki/Java_annotation#:~:text=In%20the%20Java%20computer%20programming,be%20read%20from%20source%20files.)来减少样板代码的数量。通过集成到您的 IDE 中，Lombok 能够注入开发人员可以立即使用的代码。例如，简单地将`@Data`注释添加到数据类中，就会在 IDE 中产生许多像 setters 和 getters 这样的新方法。在最简单的场景中，Project Lombok 可以将这些类减少到必需的字段和一个`@Data`注释。

Lombok 支持各种流行的 ide，如 [IntelliJ IDEA](https://projectlombok.org/setup/intellij) 、 [Visual Studio Code](https://projectlombok.org/setup/vscode) 和 [Eclipse](https://projectlombok.org/setup/eclipse) 。如果您决定在 Java 项目中引入 Lombok，那么您的同事也需要在他们的 ide 中设置 Lombok。设置完成后，您可以使用 Lombok 提供的注释。

然而，Lombok 不会将 Java 转变成一种全新的语言。归根结底，它仍然是 Java 代码，但是您避免了编写大量样板代码，从而节省了时间。您可以轻松地向 Lombok 介绍新的或现有的项目。对于更成熟的项目，可以开始对新代码使用 Lombok，然后逐步迁移现有代码。

# 普通 Java 类与带有 Lombok 的 Java 类

首先，让我们看看一个典型的 Java 数据类。乍一看，对于一个简单的类来说，它看起来已经有很多代码了。最后，这个表示用户的类只包含一个类型为`List`的字段。如果我们开始引入更多的字段，如姓名、电子邮件等，您可以看到这个类会变得多么大。

让我们看看如何使用 Lombok 简化上面的代码。很快，您就会发现涉及的代码少了很多。没有指定 getters、setters、constructors 或`equals` / `hashCode`方法。而是使用类似`@Data`的注释。随着代码的减少，Java 类的目的和功能对于任何阅读源代码的人来说都变得更加清晰。

如果您在 IDE 中启用了对 Lombok 的支持，并开始输入，您会看到所有这些东西实际上都在那里。例如，我们可以通过调用`new UserDataWithLombok().getPermissions()`来获得一个用户的权限。多亏了 Lombok，添加新字段(如名称)只需一行就可以完成。

还有龙目岛提供的[更多注解](https://projectlombok.org/features/all)，大家可以随意查看！

# 结论

感谢您阅读这篇关于 Project Lombok 如何帮助我们在 Java 项目中编写更少的样板代码的短文。在我们的例子中，引入 Lombok 得到了回报，因为需要编写的样板代码更少。就个人而言，我会尽早在 Java 项目中引入 Lombok。

你知道在 Java 中用更少的代码实现更多的其他方法吗？请在评论中告诉我。
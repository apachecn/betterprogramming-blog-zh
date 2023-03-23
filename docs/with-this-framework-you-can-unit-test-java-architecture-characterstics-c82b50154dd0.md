# 用这个框架对 Java 架构特性进行单元测试

> 原文：<https://betterprogramming.pub/with-this-framework-you-can-unit-test-java-architecture-characterstics-c82b50154dd0>

## 易于使用的架构适应性功能测试

![](img/eea2ed07bf984a18a3d4c6fb498c5d1b.png)

照片由 [Hitesh Choudhary](https://unsplash.com/photos/pMnw5BSZYsA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

作为一名架构师，你不可能总是接近编码。你能看到代码的唯一时间是当你审查别人的代码的时候。

由于测试驱动开发、静态代码分析工具(如 [PMD](https://pmd.github.io/) 和 [Checkstyle](https://checkstyle.sourceforge.io/) )和持续集成(CI)管道，您至少可以确保代码、代码质量和代码格式的任何突破性变化都将得到维护。

此外，由于代码覆盖工具，例如 [Cobertura](https://cobertura.github.io/cobertura/) ，您知道真正经过单元测试的代码的百分比。

# 一般代码审查

即使有了所有这些工具，当我进行代码评审时，我还是会寻找这样的东西:

*   任何类都不应该抛出泛型异常。
*   不写入`System.out`(而是使用日志记录)。
*   任何类都不应该使用 Java util 日志记录([阅读为什么](https://stackoverflow.com/questions/11359187/why-not-use-java-util-logging))。
*   所以持家之道类应该在道包中，`Entities`应该驻留在`Domain` 或`Model` 包中。
*   接口不应该放在实现包中。
*   接口类不应该以*接口*字开始或结束。
*   分层依赖关系。例如，`Services`不应访问`Controller` 类，`DAO/persistence` 类不应访问服务。
*   正确使用 Java 安全包。
*   自觉使用第三方库。

以及其他类似的事情。

我并不总是有机会坐在一起检查清单并验证最佳实践。人类的错误是自然的。因此，我在寻找一些可以做这些检查的东西，作为我们 CI 构建的一部分。

我寻找这种东西的探索在一个名为 [ArchUnit](https://www.archunit.org) 的库中停止了。

# 什么是 ArchUnit？

如 ArchUnit 网站所述:

> ArchUnit 是一个免费的、简单的、可扩展的库，用于使用任何普通的 Java 单元测试框架来检查 Java 代码的架构。
> 
> 也就是说，ArchUnit 可以检查包和类、层和片之间的依赖关系，检查循环依赖关系，等等。它通过分析给定的 Java 字节码，将所有的类导入到 Java 代码结构中。

# 如何使用 ArchUnit

您可以使用 [Maven](https://maven.apache.org/) 在测试范围内添加依赖项，如下所示:

或者，如果你用的是 [Gradle](https://gradle.org/) :

```
dependencies {
    testCompile 'com.tngtech.archunit:archunit:0.8.0'
}
```

一旦添加了依赖项，就可以开始为上面提到的规则编写单元测试用例了。

您可以按原样使用内置的通用规则。这些规则可在以下软件包中找到:

```
com.tngtech.archunit.library.GeneralCodingRules
```

详情可以在[这里](https://www.archunit.org/userguide/html/000_Index.html#_general_coding_rules)找到。

# 编写自己的规则

是的，你可以写你自己的规则。

每个架构师或团队领导都可以有自己的规则。好消息是，ArchUnit 的 API 具有足够的可扩展性，可以定义您自己的规则。

阅读[这篇关于如何构建自己的规则的信息](https://www.archunit.org/userguide/html/000_Index.html#_composing_rules)。

# 例子

您可以查看下面的 GitHub repo 以了解详细示例:

[](https://github.com/TNG/ArchUnit-Examples) [## TNG/建筑单位-示例

### ArchUnit(一个 Java 架构测试库，用于指定和断言架构规则…

github.com](https://github.com/TNG/ArchUnit-Examples) 

# 类似于 JUnit

这与我们在 [JUnit](https://junit.org/junit5/) 中的工作非常相似。您可以使用注释来启用或禁用测试。

# 建筑单位测试

测试类似于在 Maven 中运行单元测试的方式:

```
mvn test
```

或者，如果你用的是 Gradle:

```
gradle test
```

# 结论

我认为这是实施精益架构的良好开端。

最近我写了关于[轻量级架构决策记录](https://medium.com/@tanmay.avinash.deshpande/here-is-a-simple-yet-powerful-tool-to-record-your-architectural-decisions-5fb31367a7da) (LADRs)。它与 ArchUnit 密切相关。

[](https://medium.com/@tanmay.avinash.deshpande/here-is-a-simple-yet-powerful-tool-to-record-your-architectural-decisions-5fb31367a7da) [## 这里有一个简单而强大的工具来记录你的架构决策

### 自从我接手技术架构师的角色以来，我总是被这样的想法逗乐，为什么…

medium.com](https://medium.com/@tanmay.avinash.deshpande/here-is-a-simple-yet-powerful-tool-to-record-your-architectural-decisions-5fb31367a7da) 

如果你尝试这个，请让我知道你的经历。
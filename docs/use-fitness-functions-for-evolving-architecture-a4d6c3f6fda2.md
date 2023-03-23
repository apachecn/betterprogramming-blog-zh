# 使用适应度函数进化建筑

> 原文：<https://betterprogramming.pub/use-fitness-functions-for-evolving-architecture-a4d6c3f6fda2>

## 用简单的例子介绍适应度函数概念

![](img/40b82ee58aca8590e121cd84842a32ba.png)

杰森·布里斯科在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在本文中，我将介绍适应度函数的概念，并提供几个简单的例子。在本文结束时，您将了解这些函数的用途。希望你能收集到关于测试项目需求的新想法。

# 什么是健身功能？

适应度函数被用来衡量架构离实现目标有多近。他们引导我们经历系统的进化。

软件架构考虑诸如性能、可靠性、安全性、可操作性、编码标准等关键需求。

例如，我们可以编写测试或度量来满足所需的标准。考虑一个业务需求，该需求规定所有服务都应该在 X 毫秒内响应。我们可以编写一个测试来测量响应时间，如果结果大于该值，则测试失败。

每个新服务都应该通过这些测试标准。这样，如果开发按照计划进行，我们会不断地得到反馈。

# 什么时候使用健身功能？

我们的目标应该是在软件开发的早期阶段确定适应度函数。你的产品最重要的标准是什么？通常，涉众和业务分析师提供规范。根据行业不同，要求也不同。例如，银行业的稳定表现和安全性至关重要。

架构标准不断发展。适应度函数有助于将复杂的系统分解成更小的部分。这样，在早期阶段对软件进行任何更改并识别任何问题会更安全。

# 适应度函数示例和工具

从关键人物那里收集必要的标准后，将它们集成到测试框架中。考虑将测试合并到 CI/CD 管道流程中。

您可能熟悉确保代码质量得到满足的单元测试。让我们涵盖其他非功能性需求，比如可伸缩性、性能和其他架构方面。

## 架构约束测试

让我们编写一个测试来验证架构约束。

考虑在技术架构级别定义的适应度函数，以控制组件之间的依赖关系。比如对于 Java，有一个工具叫做 [ArchUnit](https://www.archunit.org/) 。它分析包和类、层和片之间的依赖关系，检查循环依赖关系，等等。

这些测试可以作为 JUnit 测试运行。它们防止开发人员违反架构设计原则。如果有人不小心提交了不符合规范的代码，构建就会失败，开发人员会及时发现问题。

例如，让我们验证控制器类应该只调用安全方法:

使用 ArchUnit 进行控制器测试，摘录自[https://github . com/TNG/arch unit/blob/main/arch unit-example/example-JUnit 5/src/test/Java/com/tngtech/arch unit/example test/JUnit 5/controllerrulestest . Java](https://github.com/TNG/ArchUnit/blob/main/archunit-example/example-junit5/src/test/java/com/tngtech/archunit/exampletest/junit5/ControllerRulesTest.java)

## 架构设计测试

如果您正在应用[分层软件架构](https://en.wikipedia.org/wiki/Multitier_architecture)，您可以添加 ArchUnit 测试来确保设计不被破坏:

分层架构测试，摘自[https://github . com/TNG/arch unit/blob/main/arch unit-example/example-JUnit 5/src/test/Java/com/tngtech/arch unit/example test/JUnit 5/layeredacturetest . Java](https://github.com/TNG/ArchUnit/blob/main/archunit-example/example-junit5/src/test/java/com/tngtech/archunit/exampletest/junit5/LayeredArchitectureTest.java)

该测试确保只有允许的层才能访问指定的层。

## 架构和操作测试

您还可以度量体系结构和操作质量的组合。对于云环境，有一个工具叫做[混沌猴](https://github.com/netflix/chaosmonkey)。网飞的工程师发明了它来测试 AWS 的弹性。

它随机地制造混乱——延迟增加，进程终止，等等。故障的暴露确保了开发人员使服务对实例故障具有弹性。如果你想了解更多关于混沌工具的知识，可以看看我关于混沌网格的相关文章:

[](/ensure-a-stable-production-environment-with-chaos-mesh-54057773d582) [## 用混沌网确保稳定的生产环境

### 介绍混沌网格，一个强大的混沌工程工具

better 编程. pub](/ensure-a-stable-production-environment-with-chaos-mesh-54057773d582) 

## 安全测试

为了安全，您可以使用静态应用程序安全测试(SAST)或图像扫描工具。您可以防止使用具有已知漏洞的容器映像。例如， [JFrog Xray](https://jfrog.com/xray/) 这样的工具会递归扫描容器中的所有层。

# 结论

具有适应性功能可确保软件符合所需的架构标准。通过将软件分解成更小的单元，更容易实现新的变化。适应度函数不断地提供关于开发过程的反馈。

我希望你今天学到了一些新东西。感谢您的阅读，祝您编码愉快！
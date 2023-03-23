# 用 Ktlint 格式化 Kotlin 代码

> 原文：<https://betterprogramming.pub/format-your-kotlin-code-with-ktlint-6fd1321504d0>

## 将关于代码风格的讨论自动化，关注真正重要的事情:将产品交付给用户

![](img/801cb8251bfaf89cce4f2611974671e1.png)

Mahmudul Hasan Shaon 在 [Unsplash](https://unsplash.com/s/photos/text-style?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

当审查合并请求时，我总是发现很难提出代码风格的问题。

代码风格是主观的。我对干净代码的样子有自己的看法，这个看法可能与我团队中其他人的看法大相径庭。

代码风格也有不同的层次。一方面，我们有直接影响代码可维护性的最佳实践:

*   小方法
*   描述性方法名称

另一方面，我们遇到的问题不一定会使代码更难维护，但通常会使代码库更一致、更易读:

*   一致缩进
*   换行
*   避免代码墙

虽然我发现对第一组中不遵循最佳实践的代码进行评论很容易，但我发现由于第二组中的问题，将合并请求发送回作者会更难。

如果代码可以工作并且测试通过，那么因为作者使用了制表符而不是空格而推迟这个特性值得吗？

这就是自动化的用武之地。虽然不可能自动化所有事情，但是有一些工具可以自动检测代码风格违规，并在编码过程或构建管道中标记它们。

一些工具甚至可以自动修复一些代码风格违规。

在我看来，实现这种检查的第一步是将团队聚集在一起，并就一组代码风格的规则达成一致——这些规则可能局限于特定语言的最佳实践，或者还包括您的项目的特定规则。一旦确定了规则，将该工具集成到您的开发/构建过程中，以标记违反这些规则的代码。通过这种方式，开发人员可以在早期获得关于违反样式规则的代码的反馈，甚至可以在提交代码供审查之前修复它们。

不同的语言有不同的工具来实现这个目的。当我在做一个 Kotlin 项目时，我使用 Ktlint。

# 什么是 Ktlint？

Ktlint 是一个工具，它根据一组代码风格规则扫描您的代码库，以找到违规之处。它自称为:

> 内置格式化器的防自行车脱落 Kotlin 棉绒

术语“骑车族”指的是当一个团队面临一个大的或复杂的决策时，他们会在琐碎和不重要的决策上花费太多时间。[在最初的比喻中](https://en.wiktionary.org/wiki/bikeshedding)它指的是一个负责批准核电站计划的委员会，结果却把大部分时间花在不重要的决定上，比如员工自行车棚的材料。

Ktlint 试图通过为你做一些不重要的决定——关于遵循什么样的风格规则——来避免掉自行车。

它附带了一套基于官方 Kotlin 风格指南的风格规则。但这并不意味着你不能配置它。您可以随意禁用一些规则或创建新规则，但在大多数情况下，标准规则就足够了。

您可以使用它来检查您的代码是否符合所有规则，或者作为格式化程序来自动修复不符合规则的代码。格式化程序无法修复某些问题，但是根据我的经验，它可以正确地修复最常见的问题。

# 整合 Ktlint 和 Gradle

我喜欢 Ktlint 的一点是它能够使用 maven 或 gradle 将其集成到您的构建管道中。您可以在 [Ktlint 自述文件](https://github.com/pinterest/ktlint#installation)中找到安装说明。就我个人而言，我使用它与 gradle 和我更喜欢设置它没有插件。

安装非常简单——下面显示的`build.gradle`文件包含了设置它的所有部分。两个主要步骤是 1。将 Ktlint 添加为依赖项，并将 2。添加新的 gradle 任务来针对您的代码库运行 Ktlint。

我们还将检查任务链接到依赖于`ktlint`任务。这意味着如果您的代码没有遵循您为项目配置的样式指南，那么`gradle check`命令将运行代码样式的验证并失败。

# 运行 Ktlint

既然已经设置了 gradle 文件，运行 Ktlint 就很简单了。您可以运行`gradle check`来运行所有检查，或者运行我们刚刚创建的定制任务之一:

```
// To check your code follows style rules
gradle ktlint// To attempt to fix code that does not follow style rules
gradle ktlintFormat
```

您还可以将这些任务作为 CI/CD 管道的一部分运行，并要求它们在合并新功能之前通过。通过这种方式，您可以确保您的代码始终符合您的团队同意的风格指南。

你可以看看我的 [Kotlin 项目模板](https://gitlab.com/hectorjsmith/kotlin-template)，看看我是如何为我的 Kotlin 项目设置 Ktlint 和 Gitlab CI/CD 的。

感谢阅读。您可以通过下面的链接来了解更多关于 Ktlint 以及我是如何使用它的。

# 资源

*   Ktlint 主页 — [https://ktlint.github.io/](https://ktlint.github.io/)
*   Ktlint GitHub repository — [https://github.com/pinterest/ktlint](https://github.com/pinterest/ktlint)
*   Ktlint Installation Instructions — [https://github.com/pinterest/ktlint#installation](https://github.com/pinterest/ktlint#installation)
*   My Kotlin Template Project — [https://gitlab.com/hectorjsmith/kotlin-template](https://gitlab.com/hectorjsmith/kotlin-template)
# 应用于 JavaScript 的干净代码(第 6 部分:避免条件复杂性)

> 原文：<https://betterprogramming.pub/clean-code-applied-to-javascript-part-6-avoid-conditional-complexity-5ee9cbb1b26a>

## 关于编写和维护干净代码的惊人好处的系列文章

![](img/f3270d77c4ae716df6fc5096fc47ce05.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com/s/photos/team?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

本文是深入研究干净代码主题(应用于 JavaScript)的系列文章的第六篇。

在本系列中，我们将讨论每个程序员都应该知道的关于干净代码的经典技巧，并将它们应用于特定的 JavaScript/TypeScript 语言。

*   [第 1 部分:开始之前](https://medium.com/better-programming/clean-code-applied-to-javascript-part-1-9f3badd5715)
*   [第二部分:变量](https://medium.com/better-programming/clean-code-applied-to-javascript-part-ii-variables-8302f01e539c)
*   [第 3 部分:功能](https://medium.com/@ccaballero/clean-code-applied-to-javascript-part-3-functions-f9f26b221736)
*   [第 4 部分:注释](https://medium.com/better-programming/clean-code-applied-to-javascript-part-4-3f6db21ee4e5)
*   [第 5 部分:例外情况](https://medium.com/better-programming/clean-code-applied-to-javascript-part-5-exceptions-d46e07691c19)
*   [第 6 部分:避免条件复杂性](https://medium.com/better-programming/clean-code-applied-to-javascript-part-6-avoid-conditional-complexity-5ee9cbb1b26a)

# 介绍

条件复杂性导致代码更难理解，因此更难维护。此外，条件复杂度通常是代码耦合的一个指标。

在我们想要提高代码质量的情况下，避免生成有条件复杂性的代码是明智的。

这篇文章将介绍一些可以应用于任何代码以避免条件复杂性的技术和建议。

在这种特定的情况下，我们将使用 JavaScript/TypeScript 编程语言，但我们在这篇文章中讨论的概念可以推广到任何编程语言，因为我们所做的是给出建议和技术，而不是针对特定编程语言的技巧

# 不要使用标志作为函数参数

为了避免复杂性，我必须给你的第一个建议是消除作为函数参数的标志。

相反，我们必须创建两个函数来实现我们问题的逻辑，而不是使用一个函数，其中我们有两个功能的逻辑，因为它们是不同的。

第一个例子展示了如何使用参数`isPremium`，它将决定使用一个函数还是另一个函数。

另一方面，最正确的途径是我们用两个不同的函数用声明的方式来描述这两个功能。

# 封装条件

不要让我思考！请将条件封装在具有语义值的函数中。

在第一个例子中，你可以看到有一个复杂的条件是如何让任何人思考的，而在第二个例子中，当阅读函数的名称时，它是很容易理解的。

# 用保护子句替换嵌套的条件句

这个建议在程序员的生活中至关重要。你不应该有嵌套的条件。

允许我们避免嵌套条件的主要技术之一是保护子句技术。想象一下，开发时完全不需要`else`关键字。

下面的示例显示了演示代码中的 guard 子句，通过一种甚至可以由 IDE 自动执行的技术，代码的阅读能力得到了很大的提高。

因此，当它有趣时，不要犹豫使用它，你只需要想到他们在编程课程中教给你的相反的逻辑。

如果你想更深入地研究你所保留的守卫条款，我推荐你阅读我的具体文章: [*守卫条款*](https://medium.com/better-programming/refactoring-guard-clauses-2ceeaa1a9da) 。

# 空对象模式

在初级程序员的代码中可以看到的另一个常见错误是不断检查对象是否为空，并根据这一点检查默认操作是否显示。这种模式被称为空对象模式。

下面的例子展示了当动物为空或者不能发出声音时，如何检查数组中的每个对象。

另一方面，如果我们创建一个封装了空对象行为的对象，我们就不需要执行所述验证，如应用该模式的代码所示。

如果你想更深入的了解这个模式，推荐你阅读我的具体文章: [*空对象模式*](https://medium.com/better-programming/design-patterns-null-object-5ee839e37892) *。*

# 使用多态性删除条件

如果(不管他们是否有不同的行为)我们应该考虑其他的事情，大多数程序员认为`switch`控制结构是一个比嵌套更干净的工具。

如果我们的代码中有一个`switch`,我们一定会认为我们刚刚给代码引入了巨大的复杂性，这最终会让我们想得太多。

下面的例子显示了这些条件的误用，根据对象的类型来定义方法的逻辑。

在这种情况下，我们可以利用基于继承的解决方案，该方案利用多态性来避免这种复杂性，因为将为这些特定类型中的每一个创建一个类。

这样，我们将有一个更具声明性的解决方案，因为我们将在每种具体对象类型中有方法的定义。

# 使用策略模式(组合)/命令模式删除条件

允许我们在代码中避免条件复杂性的其他模式是策略和命令设计模式的应用。

如果你想了解更多关于这两种模式的知识，我推荐你阅读我曾深入研究过这些模式的具体文章: [*策略模式*](https://medium.com/better-programming/design-patterns-using-the-strategy-pattern-in-javascript-3c12af58fd8a) *和 c* [*ommand 模式*](https://medium.com/better-programming/the-command-design-pattern-2313909122b5) 。

在说明本节的具体示例中，您可以看到动态选择策略的策略模式。

注意`switch`控制结构的复杂性是如何通过使用不同的策略来解决这个问题的。

# 结论

在这篇文章中，我们提出了一些避免条件复杂性的建议。

条件复杂性使得代码阅读起来更加复杂。此外，这通常表明代码是耦合的，因此不太灵活。

在本文中，介绍了不同的技术和建议，通过使代码攀登一个质量台阶，允许我们避免代码中的条件复杂性。

最后，我们讨论的要点如下:

*   不要使用标志作为函数参数。
*   封装条件句。
*   用[保护子句](https://medium.com/better-programming/refactoring-guard-clauses-2ceeaa1a9da)替换嵌套条件。
*   移除使用多态性的条件。
*   使用[空对象模式](https://medium.com/better-programming/design-patterns-null-object-5ee839e37892)删除条件。
*   使用[策略模式](https://medium.com/better-programming/design-patterns-using-the-strategy-pattern-in-javascript-3c12af58fd8a) [(组合)](https://carloscaballero.io/stategy-pattern-in-javascript-typescript/)(composition))移除条件句。
*   使用[命令模式](https://medium.com/better-programming/design-patterns-using-the-strategy-pattern-in-javascript-3c12af58fd8a)删除条件。
# 停止记忆设计原则，开始学习设计价值观

> 原文：<https://betterprogramming.pub/stop-memorizing-design-principles-and-start-learning-design-values-bb6ab1300256>

## 价值观是我们决策的核心。原则是实现这些价值的工具

![](img/3d21b07388f7c7c79601ddf8efc1308c.png)

杰森·斯特鲁尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

亲亲。干的。坚实。如果你是一名软件工程师，你会马上认出这些术语。您可能在课堂上听过它们，在书籍和博客文章中读到过它们，并在代码评审中被提醒过。然而，尽管如此频繁地遇到它们，我们发现自己每隔几个月就会查阅一些实体信件。随着这种在最流行的设计原则中出现的记忆缺失，几乎可以肯定的是，一些更模糊的原则和模式将会逃离我们。

我们需要更简单的东西，一些基础的东西，一些帮助我们获得失去的知识而不是被提醒失去的知识的东西。我们需要设计价值。

# 什么是设计价值观？

价值观是我们决策的核心。原则是实现这些价值的工具。

我们必须停止将设计原则视为软件的目标，而是作为实现我们所重视的目标的一种方式。考虑 SOLID 中的依赖倒置原则，它要求编码到依赖的接口，而不是编码到具体的实现。对一个接口编码没有内在的价值。相反，当对一个接口编码时，我们使程序更容易维护和改进。只要依赖关系维护它的公共 API，软件的其余部分就不会受到变化的影响。这允许单点更改，并防止意外后果的连锁反应。

在这个例子中，我们重视易于更改的代码，并使用 SOLID 的依赖倒置原则来追求这一价值。

写代码的时候，你需要选择你的设计价值观。这个选择因人而异，取决于代码的用例。每个设计决策都会有取舍，但是价值观可以帮助你决定哪些取舍是可以接受的，哪些是必须的。

一旦你定义了你的价值观，你将能够评估哪些设计原则将有助于实现你对好代码的定义。你可能会遇到一个你不知道的没有原则或模式的设计决策。通过询问哪条道路更符合您的价值观，您将能够做出明智的决定，而不是盲目地编写代码。

# 编写代码时的三个常见价值观

请记住，并非所有这些价值都适用于您的用例，有时甚至可能与它不一致。

## 1.易于更改的代码

您的设计选择应该使代码更容易更改。这可能是最普遍的价值，因为代码几乎总是在变化*。*虽然不要重复自己(干)对于程序员来说似乎是一种节省工作的方式，但它在帮助代码更容易更改方面更有价值。当代码中的知识不重复时，对代码的更改只能发生在一个地方。这比在代码中寻找所有知识重复的隐藏角落要容易得多。同样，保持简单愚蠢(KISS) 是不言自明的，简单代码比复杂代码更容易更改。虽然我不会深入研究每一个坚实的原则，但我相信您会看到每一个都有助于使代码更易于维护。

## 2.可靠代码

您的设计选择使代码更有可能按照最初的意图执行。也许您的应用程序处理关键任务流程或管理大量资金，您需要它完全按照预期执行。虽然价值不同于易于更改，但它有许多相同的设计原则。最值得注意的是，KISS 可以作为实现弹性的工具。一个系统越不复杂，它就越不可能崩溃或产生意想不到的后果。

## 3.执行代码

您的设计选择优化了程序的内存或速度性能。虽然现代计算的能力已经让许多开发人员不那么担心这个领域，但是仍然有一些应用程序将这个领域作为主要的优先事项。通常，追求性能会导致不直观但有创造性的解决方案。这种增加的复杂性可能与您的可靠性和易于改变的价值观背道而驰。

# **使用您的设计值**

一旦你明白你的设计价值是什么，你的决定和原则就会随之而来。不要怀疑代码选择是否遵循了坚实的原则，问问自己:

*   哪种设计更符合我的代码价值观？
*   容易改变吗？
*   可靠吗？
*   是表演性的吗？

如果你能批判性地思考这些问题，你不需要记住设计原则。即使当价值彼此不一致或者代码不符合千篇一律的原则时，你也能够做出细微的决定。

一个很好的例子是用汇编语言编写 Microsoft Excel 部分的设计选择。虽然它允许在大型电子表格上进行快速计算，但我同情那些不得不修复 bug 或改变算法的开发人员。这并不意味着 Excel 写得不好。相反，这是一个很好的例子，告诉我们应该如何了解自己的价值观，并相应地为之优化。
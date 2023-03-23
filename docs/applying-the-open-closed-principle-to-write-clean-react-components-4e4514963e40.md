# 应用开闭原则编写干净的 React 组件

> 原文：<https://betterprogramming.pub/applying-the-open-closed-principle-to-write-clean-react-components-4e4514963e40>

## 看看实际应用中的可靠原则

![](img/49f75ab233635a4236f3f89702a776c0.png)

来自 [Pexels](https://www.pexels.com/photo/black-camera-accessory-lot-1476316/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的免费创意素材。

坚实是一套原则。它们主要是为关心代码质量和可维护性的软件专业人员提供的指南。

React 本质上不是面向对象的，但是这些原则背后的主要思想可能是有帮助的。在本文中，我将尝试演示如何应用这些原则来编写更好的代码。

在[之前的一篇文章](/how-to-apply-solid-principles-to-clean-your-code-in-react-cdfd5e0a9cea)中，我们谈到了单一责任原则。今天，我们将讨论固体的第二个原理:开闭原理。

# 本系列的其他文章

1.  [单一责任原则](/how-to-apply-solid-principles-to-clean-your-code-in-react-cdfd5e0a9cea)
2.  [利斯科夫替代原理](/applying-the-liskov-substitution-principle-in-react-3a0614a42a08)
3.  [界面分离原理](/how-to-apply-interface-segregation-principle-in-reactjs-fadf77113c5d)
4.  [依存倒置原则](/apply-the-dependency-inversion-principle-in-react-c20a0afc3d64)

# 开闭原理是什么？

根据索本·让桑在 Stackify 上的说法:

> 罗伯特·c·马丁认为这个原则是“面向对象设计最重要的原则”但他不是第一个定义它的人。Bertrand Meyer 在 1988 年他的书《面向对象软件构造》中写到了这一点。他把开/关原理解释为:
> 
> 软件实体(类、模块、函数等。)应该对扩展开放，但对修改关闭。"

这个原则告诉你以这样一种方式编写代码，你将能够在不改变现有代码的情况下添加额外的功能。

让我们看看在哪里可以应用这个原则。

# 让我们从一个例子开始

假设我们有一个`User`组件，我们在其中传递用户的详细信息，这个类的主要目的是显示特定用户的详细信息。

这是足够简单的开始。但是我们的生活并不那么简单。几天后，我们的经理告诉我们，在我们的系统中有三种类型的用户:`SuperAdmin`、`Admin`等。

并且它们中的每一个都将具有不同的信息和功能。

# 糟糕的解决方案

第一个显而易见的解决方案是在我们的组件中包含一个条件，并根据不同的用户类型呈现不同的信息。

User.js

你看出这里有什么问题了吗？

首先，我们的代码现在很乱。

其次，如果我们需要另一类用户呢？然后，我们需要进入`User.js`，为该特定类型的用户添加另一个条件。

这显然违反了开闭原则，因为我们不允许修改`User`组件中的代码。

# 有什么解决办法？

好了，我们可以在这个场景中应用两种主要技术:

1.  高阶分量
2.  成分组成

尽可能走第二条路会更好，但是在某些情况下，使用特设是必要的。

现在，我们将使用脸书推荐的技术，称为[组件组合](https://reactjs.org/docs/composition-vs-inheritance.html)。

# 让我们创建单独的用户组件

现在我们需要设计我们的代码，这样我们就不需要在`User.js`组件中添加条件。让我们为`SuperAdmin`创建一个单独的组件:

SuperAdmin.js

类似的，另一个给`Admin`用户的:

管理员. js

现在我们的 App.js 文件变成了

App.js

现在，我们可以根据需要创建任意多的用户类型。我们针对特定用户的逻辑被封装，我们不需要重新访问代码进行任何额外的修改。

有些人可能会认为我们增加了不必要的文件数量。
当然，您现在可以保持原样，但是随着应用程序复杂性的增加，您肯定会感到痛苦。

# 警告

坚实是一套原则。它们不是强制要求你在每种情况下都应用。作为一名经验丰富的开发人员，您应该在代码长度和可读性之间找到一个良好的平衡。

不要过分纠结于这些原则。事实上，有一个著名的短语可以解释这些场景:

> “固体太多了。”

所以知道这些原则是好的，但是你得保持平衡。对于一两个额外的字段，你可能不需要这些组合，但是从长远来看，将它们分开肯定会有帮助。

# 结论

了解这些原则会让你走得更远，因为归根结底，一段好的代码才是最重要的，做事没有单一的方式。

祝您愉快！

固体的第三个原理(利斯科夫替代原理)在这里[讨论](/applying-the-liskov-substitution-principle-in-react-3a0614a42a08)

[](/applying-the-liskov-substitution-principle-in-react-3a0614a42a08) [## 里斯科夫替代原理在 React 中的应用

### 看看实际应用中的可靠原则

better 编程. pub](/applying-the-liskov-substitution-principle-in-react-3a0614a42a08) 

**有什么话要说？通过** [**LinkedIn**](https://www.linkedin.com/in/56faisal/) 与我联系

# 资源

*   [https://stackify.com/solid-design-open-closed-principle/](https://stackify.com/solid-design-open-closed-principle/)
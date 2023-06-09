# 为什么 Equals 是一个强大的运算符，可以帮助您编写更简洁的代码

> 原文：<https://betterprogramming.pub/why-equals-is-a-powerful-operator-to-help-you-write-cleaner-code-ecf462c56241>

## 利用平等的简单性来减少膨胀&将责任保持在它们应该在的地方

![](img/fabb0c776ab033f4bd30f172c2601ee1.png)

迈克尔·泽兹奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

编程语言如何确定两个对象是否相等？

他们当时可能没有意识到，但是工程师首先要学习的事情之一就是平等。第一次写类似于`if x == y`的东西时，你使用的是等式。

然而，只使用与[原语类型](https://en.wikipedia.org/wiki/Primitive_data_type)相等会导致代码臃肿、代码重复和代码异味。以我的经验来看，平等并没有被充分利用，而且在许多用例中可能会异常强大。

# 什么是平等？

相等是确定两个事物相等的机制。考虑下面的 JS 代码:

```
if (1 == 1) {
  console.log("equal!");
}
```

如果没有定义两个整数相等的语言，这个语句永远不会返回 true。没有一种编程语言定义原始类型(例如整数、字符串、布尔等)的等式。)对我们来说，编写任何代码都是极其困难的——我肯定无法实现在引擎盖下使用的逻辑来比较`1 == 1`。

这个逻辑是为您处理的，因为语言的创建者理解在基本类型上实现相等所需的复杂性和逻辑。

因此，谁最适合定义什么使得我们在应用程序中定义的类是相等的呢？添加这些类的工程师。

# 行动平等

下面的代码样本是用 Ruby 编写的，但是几乎所有的语言都允许你通过重写方法来定义对象的等式。比如[下面是](https://www.geeksforgeeks.org/overriding-equals-method-in-java/)用 Java 怎么做。

首先，让我们先来看一个例子，它没有在我们的任何类上定义等式。以下示例是许多应用程序中的常见场景—更新地址。

运行时，这将输出`addresses`匹配，但是这两个地址是否匹配的核心逻辑在`AddressUpdater`中定义。这不是这个类的责任——特别是当我们考虑到在代码库的其他地方可能需要地址比较的时候。

如果我们通过在`Address`类上定义等式来实现它，这段代码会怎么样？

代码本身并没有太大的不同，但是现在的含义和责任在哪里。`AddressUpdater`类不再知道是什么使两个地址相同，而是可以专注于它的主要职责:更新地址。

要考虑的另一点是，如果地址匹配的逻辑发生变化，您需要找到实现该逻辑的所有位置并更新它们。一个国家的逻辑不太可能改变，但是如果你支持多个国家，或者扩展到新的国家，每个国家的逻辑都会改变，从而导致困难和昂贵的重构。

你可能会争辩说，我们可以在`Address`类上定义一个像`identical_address?(other_address)`这样的方法，而不是重写`==`方法，这样会达到同样的结果。然而，你就失去了这种语言免费给你的能力。例如:

大多数语言都会为数组提供一个方法来检查传入的值是否存在于该数组中。在这种情况下，我们不想添加重复的地址，所以我们可以简洁地实现这一点，因为我们在自己的类上定义了等式。

# 在类中定义等式的好处

我在上面谈到了在你自己的类中实现等式的一些好处，但是清单并没有到此为止。为了完整起见，我将重述上面的几个。

## 运用语言的力量

更进一步说，我在上面提到的数组，如果你在你自己的类上定义了等式，他们可以直观地写出他们为原始类型而不是你自己的类所写的代码。

另一个需要考虑的问题是，如果您定义了一个额外的方法来确定等式，比如`x_identical?`，那么对该方法的名称或者结构的更改可能会导致整个存储库中的一系列失败。如果我们自己定义平等，这不是一个问题。

## 去除原始的执念

[原语痴迷](https://refactoring.guru/smells/primitive-obsession)是一种常见的代码味道，它不依赖于[复合类](https://en.wikipedia.org/wiki/Composite_data_type)，而是使用整数和字符串等原语。

例如，假设您需要查看两个订单是否相等，而没有在订单类上定义相等，您可能会得到如下结果:

```
if first_order_id == last_order_id
  # do something
end
```

在这种情况下，完整的订单对象可能对我们是可用的，但是因为没有在对象上定义等式，工程师可能会选择自己实现这个逻辑，而不是依赖于一个原语:订单 ID。

## 减少重复，保持职责清晰

我们在上面提到了这一点，但这对我们自己定义平等是一个很大的帮助。这样做可以确保其他工程师有一个简单的方法来比较对象——允许将两个对象定义为相等的逻辑在各自的类中定义，而不是在整个代码库中以不同的方式实现。

此外，它保持了职责的清晰——例如，您的服务类不需要关心计算出相等性，它们可以专注于手头的任务。

另一个需要考虑的是，如果你自己没有定义等式，你就把它留给了另一个工程师去解释——或者更糟的是，随着时间的推移，留给了多个工程师去解释，这可能导致代码库的不同部分出现不同的逻辑。

## 识别实体和值对象

在[领域驱动设计](https://en.wikipedia.org/wiki/Domain-driven_design)中，有[实体和值对象](https://enterprisecraftsmanship.com/posts/entity-vs-value-object-the-ultimate-list-of-differences/)的概念。对于不熟悉给定的[有界环境](https://martinfowler.com/bliki/BoundedContext.html)的工程师来说，仅仅从定义等式的文档就可以让他们容易地识别什么是实体，什么是值对象。

# 摘要

当正确使用的时候，平等是非常强大的，但是对于以前没有接触过这个概念的人来说，可能会感到奇怪或陌生。当在 PR 上使用这种方法时，我现在确保在描述中包含一些文章的链接，以供不熟悉的人参考。

这篇文章的灵感来自最近与一些同事的讨论。引发了一个 PR，需要它来确保两个对象相等，但它没有使用语言内置的相等运算符。相反，它实现逻辑来确定它们在服务类中是否相等。

虽然这不是本文的重点，但我从那次经历中学到的不是假设某人熟悉某个特定的概念。然而，提升你的同事对于任何工程师来说都是一个关键的角色——如果你选择在项目中实现这一点，我很乐意听到结果！

> 希望扩展您的技术知识，但不确定阅读什么？我办了一份免费的时事通讯，提供两周一次的技术书籍推荐，包括我从书中获得的关键信息。感兴趣吗？[在这里报名吧！](https://subscribe.technicalbookclub.com/?utm_source=medium&utm_medium=article&utm_campaign=equality)
# 如何使您的 TypeScript 泛型函数更安全、更易于使用

> 原文：<https://betterprogramming.pub/figuring-types-in-typescript-generic-functions-9479dd2fc3ea>

## 找出 TypeScript 泛型函数中的类型

![](img/879d244603d7ee2f71b76871a60ba87a.png)

穆罕默德·拉赫马尼在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

对于来自 JavaScript 世界的人来说，在 Typescript 中正确使用类型可能会非常令人困惑(如果不是疯狂和令人震惊的话)。我们公司不久前才开始从 JavaScript 迁移到 TypeScript，所以大多数开发人员仍在学习如何使用它(包括我自己)。对于一些人来说，这篇文章可能是微不足道的，但对我来说，这是一个真正的挑战。我相信分享关于挑战性事物的经验和知识是我们所能做的最好的事情，因为它可以帮助面临同样挑战的人。

# 问题

因此，有一次，当对来自另一个团队的合并请求进行代码审查时，我和我的同事偶然发现了这两个特殊的函数:

让我们跳过对它们实际代码的回顾，这与本文并不相关，它们本质上是对象的转换器，通过这个对象键将某个对象的值映射。引起我们注意的不是函数的内部，而是这些函数使用 TS 类型的方式。我和我的同事立即发现了这些函数类型的缺陷，这要归功于我们以前分别在 Kotlin 和 C++中的经验。

这些函数应该是这样使用的:

这是来自同一个合并请求的真实函数，但是名字和对象结构已经被改变了——对不起，NDA 让我这么做的，☹️

## 总之在这里我们看到两个主要问题:

1.  我们没有安全参数——我们可以将“blah”和“blahblah”作为参数传递给我们的`mapByKey`函数，Typescript 不会阻止我们这样做。
2.  我们必须做[类型断言](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-assertions)来“平息”类型脚本，这增加了出错的巨大风险。这是因为 TS 将把`result`变量作为任何`T[keyof T]`到任何`T[keyof T]`的`Map`(在这个例子中`T[keyof T]`将是`string | Date | { name: string } | number`)，并且任何满足那些类型的类型断言将是“合法的”。

如果你不确定这一切意味着什么，我推荐你阅读一些关于操作符的[键和](https://www.typescriptlang.org/docs/handbook/2/keyof-types.html)[泛型](https://www.typescriptlang.org/docs/handbook/2/generics.html)的类型文档。

# 局部解决

我们想到的第一个解决方案不是在我们的`mapByKey`函数中使用`string`类型，而是使用`keyof T:`

它肯定会帮助 Typescript 防止我们犯拼写错误:

看起来已经好多了，但是第二个问题我们该怎么办呢？我们如何摆脱这种类型断言，让代码更安全？我们如何“告诉”Typescript 那些函数中的结果`Map`包含的不是一些抽象的`keyof T`元素，而是我们 100%确定的类型(因为我们知道我们传递给函数的对象的“键”是什么，并且我们从对象结构中知道它们的类型)。

# 真正的解决方案

经过一番挖掘和头脑风暴(以及长时间的绝望)，我们终于找到了解决方案——我们需要添加更多的通用参数，并使用带约束的[类型参数](https://www.typescriptlang.org/docs/handbook/2/generics.html#using-type-parameters-in-generic-constraints)。这样，Typescript 将能够知道函数参数的确切类型。我们的函数现在看起来像这样:

现在使用它们变得更加容易和安全:

现在，Typescript 处于警戒状态，甚至正在帮助我们编写代码！

# 结论

泛型和其他概念可能很可怕，但是要努力让 Typescript 成为你的朋友，而不是你的敌人。广泛探索 Typescript [文档](https://www.typescriptlang.org/docs/)和其他资源，不要害怕尝试一些东西，也不要因为某些东西不起作用而失去动力。最终它肯定会解决的。当它到来时，它将为你创造奇迹。
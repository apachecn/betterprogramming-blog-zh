# 在 TypeScript 4.1 中我们可以期待什么？

> 原文：<https://betterprogramming.pub/what-can-we-expect-in-typescript-4-1-822a59d89dc2>

## 了解下一版本的 TypeScript 将会带来什么

![](img/9d66d47a4c29a1a8ec3d4d6356d40be5.png)

由[阿莫斯 G](https://unsplash.com/@amosg?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

8 月 20 日，TypeScript 团队发布了 Typescript 4.0。大约三个月后，他们将在 11 月发布另一个版本，名为 TypeScript 4.1。这将是今年 TypeScript 的最后一次发布。根据[的迭代计划](https://github.com/microsoft/TypeScript/issues/40124)，似乎他们正在各方面努力，包括语言特性、性能和基础设施，为你提供最好的特性。

在本文中，我将简要讨论 TypeScript 4.1 测试版中发布的一些特性。

# 映射类型中的键重新映射

映射类型最初是在 TypeScript 2.1 中引入的，它们已用于基于您提供的任意键或基于其他对象类型创建新的对象类型，如下所示:

在 4.1 版本中，TypeScript 通过引入一个名为`as`的新子句，启用了映射类型中的重映射。使用此选项，您可以根据输入创建新的键和过滤键:

# 模板文字类型

自早期以来，字符串文字类型就存在于 TypeScript 中，并且它们主要用作映射类型中的属性名。此外，我们可以使用字符串来建模函数和 API，拼写检查字符串值，等等。除了这些用途，我们可以使用字符串文字的最重要的情况之一是构建其他字符串文字类型。

> “这就是 TypeScript 4.1 引入模板文字字符串类型的原因。它与 JavaScript 中的[模板文字字符串具有相同的语法，但是用于类型位置。当您将它与具体的文本类型一起使用时，它会通过连接内容来生成一个新的字符串文本类型。—](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) [微软开发者博客](https://devblogs.microsoft.com/typescript/announcing-typescript-4-1-beta/)

```
type Student = "student";

type Greeting = `hello ${Student}`;
// same as
//   type Greeting = "hello student";
```

这一新特性的另一个有趣的用途是，我们可以使用联合生成两个不同文字的所有可能的字符串文字:

你可以在 TypeScript 4.1 的[文档](https://devblogs.microsoft.com/typescript/announcing-typescript-4-1-beta/)中找到更多关于文字类型的细节。

# 递归条件类型

虽然有一些黑客或非正统的方法，但在以前版本的 TypeScript 中，不可能轻松地解开嵌套数组或承诺，使它们扁平化。但是在最新的版本中，TypeScript 允许一些条件类型来解决这个问题。

根据[文档](https://devblogs.microsoft.com/typescript/announcing-typescript-4-1-beta/):

> 在 TypeScript 4.1 中，条件类型现在可以立即在其分支内引用自身，从而更容易编写递归类型别名

如果我们举一个解开深度嵌套承诺的例子，我们可以使用`Awaited`类型来这样做，如下所示:

但是关于这些递归类型，我们需要记住一些事情:

*   我们应该只在必要的场合使用它们。
*   他们非常强大。
*   计算成本可能很高。
*   您可以达到最大递归深度限制。

TypeScript 已经在 GitHub 上提供了递归条件类型[的实现。](https://github.com/microsoft/TypeScript/pull/40002)

# React 17 家 JSX 工厂

React 的最新版本将是 17，我们将能够在今年内看到 React 17 的稳定发布。因此，TypeScript 做了一些修改，以支持 React 17 最新版本 4.1 的最新功能。

TypeScript 4.1 引入了两个新的 jsx 编译器选项(react-jsx 和 react-jsxdev)来支持 React 17 中的`jsx`和`jsxs`工厂函数:

在这个代码片段中，您可以看到生产和开发配置文件以及这些`jsx`选项是如何在其中使用的。

# 其他的

除此之外，TypeScript 4.1 中还有一些其他重要的功能更新和突破性变化。

## **突破性的变化**

*   您不能再将抽象成员标记为异步。
*   `any` / `unknown`在虚假位置繁殖。
*   `--declaration`和`--outFile`需要一个包名 root。
*   `resolve`的参数在`Promises`中不再可选。

## **其他变更**

*   迂腐的索引签名检查(`--noUncheckedIndexedAccess`)。
*   `paths`不带`baseUrl`。
*   `checkJs`寓意`allowJs`。
*   JSDoc `@see`标签的编辑器支持。

虽然 4.1 测试版已经发布，但我们还看不到很多性能或编辑器支持方面的改进。但是在 TypeScript 4.1 的稳定发布之前还有足够的时间。让我们期待在接下来的日子里会有更多现有的功能被添加进来。

你可以在 [GitHub](https://devblogs.microsoft.com/typescript/announcing-typescript-4-1-beta/#no-unchecked-indexed-access) 上找到 TypeSCript 4.1 的迭代计划，也可以在[文档](https://github.com/microsoft/TypeScript/issues/40124)中找到关于上述特性的更多细节。

# 参考

*   [打字稿的文档](https://devblogs.microsoft.com/typescript/announcing-typescript-4-1-beta/#no-unchecked-indexed-access)
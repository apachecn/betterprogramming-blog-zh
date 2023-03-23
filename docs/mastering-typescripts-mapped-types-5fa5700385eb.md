# 掌握 TypeScript 的映射类型

> 原文：<https://betterprogramming.pub/mastering-typescripts-mapped-types-5fa5700385eb>

## 像专家一样学习处理 Typescript 映射类型

![](img/36553d1e9d548419faa0638d18f1daa8.png)

照片由[丹尼斯·简斯](https://unsplash.com/@dmjdenise?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

TypeScript 最早出现在 2012 年 10 月，它从一开始就对它持怀疑态度。难道不是扼杀了 Javascript 的精神吗？这难道不是在我们的代码库中添加更多的样板文件吗？我个人认为没有必要。

那是在映射类型发布之前。它们是在四年后的 [TypeScript 2.1](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html) 版本中出现的。这个新功能立刻吸引了我。正是这缺失的部分将 Typescript 带到了一个全新的水平。

你知道映射类型的真正潜力吗？你在你的代码库中使用了他们所有的好处吗？我们将一起检查用它们你能做出什么了不起的东西。

# 基础知识

什么是映射类型？它们是避免重新定义接口的一种方式。使用它们，你可以将一个类型建立在另一个类型或接口的基础上，这样你就不用手工操作了。总结一下:这是一种基于另一种类型创建新类型的方法。有效的转型类型。

为了更好地理解本文，您可以运行 Typescript Playground 中的所有示例，在这里找到。是玩 Typescript 和练习技能的绝佳工具。

让我们开始看看一些运行中的类型脚本。

如果您想要扩展这个接口，您可以执行以下操作。

让我们做同样的事情，但是使用映射类型:

很酷，不是吗？尽管在这个场景中，我们并没有真正展示任何不能用界面完成的事情，不是吗？不过，我们才刚刚开始。

在这个特定的虚拟示例中，最好使用接口。这只是一个展示，所以你可以看到它的多功能性。

让我们看一个使用映射类型更有意义的例子。让我们将两个界面结合在一起:

这是它真正开始发光的地方。我们可以看到这变得多么容易做到和可读。

在深入研究之前，我们先来看看`keyof`原语。它非常直观:它将通过一个联合类型公开任何给定接口/类型的键。

# 内置实用程序类型

TypeScript 附带了许多实用工具类型，因此您不必在每个项目中重写它们。让我们来看看一些最常见的:

`Omit`、`Partial`、`Readonly`、`Exclude`、`Extract`、`NonNullable`、`ReturnType`——这些都是最常见的。你可以点击查看完整名单。

你不局限于一次只使用一个，而是可以根据需要组合使用，这才是真正的乐趣所在。它们就像乐高积木一样帮助你实现目标。

# 映射类型在钩子下是如何工作的？

在开始之前，让我们了解一下如何访问接口/类型属性。

我们如何迭代一个类型的属性？以类似于迭代 javascript 对象的方式:

```
[P in K]: T[P]
```

有了这些知识，我们现在就可以开始检查 TypeScript 如何构建它的实用程序了。让我们看看`Omit`，它使用了两种实用程序类型:`Pick`和`Exclude`。让我们递归地检查一下。

当把它分解成小单元时，更容易理解。`keyof`到底是做什么的？

> `keyof`操作符接受一个对象类型，并产生一个字符串或其键的数字联合——MDN

它在映射类型中起着很大的作用。这里，通过将第二个参数设为`extends keyof T`，我们可以确保我们想要排除的属性属于该类型。

总之`Omit`正在做的是:

*   使用`Pick`循环输入我们的类型键。
*   通过将其转换为`never`来排除我们的目标键。

# 构建您自己的实用程序类型

现在，让我们创建我们的第一个自定义实用程序。假设我们想要定义一个对象，其中所有的键值都来自一个特定的类型。

```
type GenericString = { [key: string]: string }
```

很简单，对吧？让我们使用`Generics`并使`string`类型可配置。

```
type GenericObject<T> = { [key: string]: T }
```

让我们添加另一个泛型类型来创建一个泛型键对值类型:

```
type GenericKeyObject<K extends keyof any, T> = {
  [P in K]?: T;
};
```

让我们看看实际情况。

当构建复杂的映射类型时，对我有用的是从简单开始，然后逐渐增加复杂性。你可能会发现开始太复杂令人沮丧。

# 提升你的效用类型和用途

很容易用映射类型构建一个非常简单的例子。现在让我们更深入地挖掘一下。

不过，让我们先看看`never`原语。它是在 2.0 版本中引入的。它表示该值永远不会出现。

现在让我们来看看 2.8 版本中添加的`infer`原语。

TypeScript 非常依赖类型推断。有一个`infer`原语，它赋予映射类型显式推理的能力。您可以使用它来提取条件类型中的类型。

什么是条件类型？它只是一个表达式，用于根据表示为类型关系的条件从两个可能的类型中选择一个。

```
T extends U ? X : Y
```

让我们把这些都发挥出来。

如果您想从由`getData`返回的`Promise`中解包该类型呢？简单:使用`infer`创建自己的实用程序。

那里发生了什么事？如果`T`延伸一个`Promise`或者一个`Function`，你只是在推断类型；否则，您只是返回给定的类型`T`。

让我们做最后一个作为奖励。让我们做一个接口，如果对象是空的，它将返回`true`，如果类型有一些属性，它将返回`false`。是的，我们可以在 TypeScript 中做这样疯狂的事情。

```
type Empty<T extends {}> = {} extends Required<T> ? true : false;type isEmpty = Empty<{}>; // truetype isNotEmpty = Empty<{ name: string}>; // false
```

为什么这很有用？您可以将映射类型`Empty`与任何条件推断/映射类型相结合，以创建更多的定制类型。所以我们刚刚创建了一个工具，它将帮助我们创建更多的映射类型。

你能达到的目标很疯狂，不是吗？极限是你的想象力。

# 类型脚本元组

最后增加和改进的是`Tuples`。`Tuples`是元素个数固定的数组。它们的类型是已知的，并且它们不一定是相同的类型。

请注意，具有多种类型的非固定大小数组不会被推断到`Tuple`中。因为数组是动态的，可能会改变`Typescript`不知道正确的类型。我将返回所有可能类型的联合。

要强制数组推断到一个`Tuple`中，使用`as const`。这将告诉 Typescript 数组是固定的，类型的顺序将被保留。

`Tuple`很有用。现在有一种情况很常见:反应钩子。Hooks 实现通常会返回一个包含结果和函数的数组，对该数组进行类型化确实非常方便。

# 包裹

![](img/c71f9562dd53ab8e49aef6b2960237dc.png)

照片由[埃迪·比拉德](https://unsplash.com/@eddybllrd?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我们从一些基本的映射开始，以一些 Typescript 高级类型结束。到目前为止，映射类型是 TypeScript 中最有趣的特性。

它们很好地展示了 TypeScript 的强大和动态。如果您对尝试 TypeScript 犹豫不决，我希望我的文章给了您所需要的最后一击。即使你现在不打算使用它，明智的做法是知道有什么，以防在不久的将来会派上用场。

将来会有更多的 TypeScript 内容出现——干杯！

你不是中等会员吗？支持我[成为这里的](http://dioxmio.medium.com/membership)。

# ***相关***

[](https://medium.com/better-programming/typescript-4-1s-advanced-mapped-types-eba9a2ba7a9) [## TypeScript 4.1 的高级映射类型

### 看看递归条件类型、模板文字类型等等

medium.com](https://medium.com/better-programming/typescript-4-1s-advanced-mapped-types-eba9a2ba7a9)
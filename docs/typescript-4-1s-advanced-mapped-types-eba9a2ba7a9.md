# TypeScript 4.1 的高级映射类型

> 原文：<https://betterprogramming.pub/typescript-4-1s-advanced-mapped-types-eba9a2ba7a9>

## 看看递归条件类型、模板文字类型等等

![](img/40e10f2114c11c2c9917b15b85cda1f4.png)

[Tine ivani](https://unsplash.com/@tine999?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

如果您曾经读过我的任何 TypeScript 文章，您就会知道我是映射类型的忠实粉丝。映射类型出现在 TypeScript 版本`2.1`中，并且在每个版本中都有所改进。

它们是我一直以来最喜欢的功能，因为一旦你掌握了它们，它们很有趣，也很容易使用。如果你不知道什么是映射类型，我建议阅读我的前一篇文章，在那里我详细解释了它们。

在本文中，我们将探索在`4.1`中引入的一些特性，以及如何将它们应用到映射类型中。

# TypeScript 4.1

[TypeScript 4.1](https://devblogs.microsoft.com/typescript/announcing-typescript-4-1/) 最近发布了，里面有很多好东西。在本文中，我们将特别关注:

*   递归条件类型
*   模板文字类型
*   映射类型中的键重新映射
*   检查索引访问

我们将深入研究这些问题，看看我们能做些什么新的事情。

我强烈推荐使用 [TypeScript playground](https://www.typescriptlang.org/play) 来最好地理解这篇文章。您可以调整所有配置，甚至切换 TypeScript 版本。对于新手来说，这是一个必须使用的工具，因为它还会显示 JS 代码的输出。

# 简短的复习

在了解新特性之前，让我们简单回顾一下。这将有助于我们在进入新领域之前热身。

什么是映射类型？

> “当你不想重复自己的时候，有时候一种类型需要建立在另一种类型的基础上。
> 
> 映射类型建立在索引签名的语法之上，索引签名用于声明尚未提前声明的属性类型。”— [打字稿的文档](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)

总而言之，映射类型允许您基于现有类型创建新类型。让我们以`Readonly`实用程序类型为例:

让我们回顾一下`Readonly`是如何工作的:

```
type Readonly<T> = { readonly [P in keyof T]: T[P]; }
```

只是为了热身，让我们创造出对面的`Readonly`。我们称之为`Writable`:

```
// Writeable<Teacher> will be equivalent tointerface WriteableTeacher {
  name: string;
  email: string;
}// 'readonly' modifier will be subtracted with the `-` modifider
```

*注意:注意* `*-*` *修饰符。在这个场景中，它被用来删除* `*readonly*` *修改器。它可以用来从* `*?*` *等属性中移除其他修改器。*

# 递归条件类型

考虑上面的例子。假设我们感兴趣的是递归地将所有嵌套类的接口属性标记为`readonly`:

在`4.1`之前，我们被允许一些基本的递归行为:

`ReadonlyRecursive`对接口/类型的每个属性反复调用自己:

然而，如果不使用映射键，我们就不能使用递归行为。它只是局限于这种行为:

随着`4.1`的发布，这现在可以像预期的那样工作了。为什么？TypeScript 放宽了对递归条件类型的一些限制。他们现在让我们变得更有想象力:

这非常强大，但也有代价。这意味着当我们添加更多的递归检查时，我们的 TypeScript 编译器可能会变慢。如果你看到你的编译时间增加，这将是你的主要怀疑。

# 模板文字类型

在进入重映射键特性之前，我们必须检查模板文字类型。他们也在`4.1`中被介绍。没有它们，重新映射功能就不可能实现。

这个特性允许我们在 TypeScript 中操作文字并改变它们的形状。TypeScript 包含了一些内在的字符串操作类型来帮助字符串操作:`Uppercase`、`Lowercase`、`Capitalize`和`Uncapitalize`。你可以在文档中看到更详细的信息[。](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html#capitalizestringtype)

还有更多。您甚至可以将它们与模板文字一起使用:

如果我们的属性有多个值，它将生成所有可能组合的并集:

让我们结合多种类型:

您可以在新的重新映射键中看到这个新特性是多么重要。

请注意，这里有一点不一致。表达式文字类型将总是解析为`string`类型。这可能会造成不兼容，如下所示:

`get${propertyName}`正在解析为与`PropertyName`不兼容的`string`类型。

但是不要惊慌。这个问题已经解决了，即将进入 TypeScript 4.2。由于这个特性是新的，所以没有其他的那么成熟。您可以在[合并拉取请求](https://github.com/microsoft/TypeScript/pull/41891)中查看更多信息。

在 4.2 发布之前，您可以使用这个简单的解决方法:

```
const x = `get${propertyName}` as PropertyName;
```

# 重新映射类型

这个功能只是前一个功能的自然演变。有了它，您可以将键和值重新映射到特定的模式。到目前为止，TypeScript 只能用现有的键创建新的类型。现在，您可以创建新的密钥。

语法非常简单，类似于 JavaScript 的`import`重映射。这个是用`as`子句完成的。

让我们来看一个基本实体:

```
type Person {
  name: string;
  surname: string;
  email: string;
}
```

让我们为它创建一个名为 Factory 的不可变样式类型:

您可以使用`as`子句在`never`原语的帮助下移除`keys`。由于`Exclude`在引擎盖下使用`never`，它也能完成这项工作。

```
type Exclude<T, U> = T extends U ? never : T
```

映射类型将允许您仅基于泛型和模板文字操作来创建复杂的模式。这减少了样板文件，帮助我们以动态模式而不是特定的界面来思考。您可以创建自定义的最佳代码实践，并将其输入。

您可以使用最佳实践创建重新映射的映射类型，并使用它们来创建您的协定。

# 检查索引访问

让我们简单介绍一下这个特性。乍一看，这似乎无关紧要，但它将使我们能够使映射类型更加安全。

通过启用`--noUncheckedIndexedAccess`，默认情况下会变成`off`，解析为索引签名的映射类型将会附加`undefined`。

# 包扎

![](img/df0b9ff51c4c57ffbdc80ba0fdfff075.png)

[瓦西里·科洛达](https://unsplash.com/@napr0tiv?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上的照片。

我们已经看到这些新功能是多么的酷和有用。随着时间的推移，它们将成为真正的游戏规则改变者，因为它们将帮助我们编写更具表现力的界面。随着 TypeScript 变得更加灵活，我们可以减少代码库中样板文件的数量。

TypeScript `4.1`提供了更多好东西。我可能会在另一篇文章中更深入地探讨这些问题。本文只是展示了这个新版本对映射类型的影响。

如果你想继续学习关于 TypeScript 的知识，可以看看下面我写的关于 TypeScript 的`strict`模式的文章:

[](https://medium.com/better-programming/getting-strict-with-typescript-2e906b48c0a) [## 对类型脚本越来越严格

### 使您的类型脚本代码更具可读性和类型安全

medium.com](https://medium.com/better-programming/getting-strict-with-typescript-2e906b48c0a) 

将来会有更多的 TypeScript 内容出现。干杯！
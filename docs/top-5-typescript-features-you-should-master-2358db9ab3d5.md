# 您应该掌握的五大打字稿功能

> 原文：<https://betterprogramming.pub/top-5-typescript-features-you-should-master-2358db9ab3d5>

## 使用这些必须知道的特性来提高您的打字稿知识

![](img/59f33bff9100cded817c0f4836d43803.png)

阿诺德·弗朗西斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

TypeScript 的影响力与日俱增。它现在是任何新的 web/Node 项目的首选配套工具。使用 TypeScript 的好处不能被夸大。然而，了解和理解 JavaScript 超集拥有的所有工具非常重要。

你在投资时间来提高你的打字技能吗？你想充分利用它吗？有时，由于没有使用正确的 TypeScript 特性，也没有遵循它的最佳实践，可能会有大量的代码重复和样板文件。

在本文中，我们将研究 TypeScript 赋予我们的五个最重要的特性。通过确保我们理解它们并知道它们的用例，我们可以构建一个更好、更全面的代码库。

# 1.联合

联合是最基本和易于使用的类型脚本特性之一。它们让我们可以轻松地将多种类型组合成一种。交集和并集类型是我们可以组合类型的方式之一。

```
function logIdentifier(id: string | number) {
  console.log('id', id);
}
```

当我们想表达某个类型可以为空时，它们非常有用:

联合不仅限于`undefined`或原语。它们可以用于任何接口或类型。

给定如上的联合类型，我们如何区分`Bike`和`Plane`？通过使用区别并集特征。我们将创建一个名为`Vehicles`的枚举，并将其用作属性值。

让我们看看我们的代码看起来怎么样:

我们刚刚看到了工会是一个简单而强大的工具，它有一些技巧。然而，如果我们想以更强大和动态的方式表达类型/接口，我们需要使用泛型。

# 2.无商标消费品

让我们的方法/API 可重用的最好方法是什么？仿制药！这是大多数类型化语言中的一个特性。它让我们以更通用的方式表达类型。这将增强我们的类和类型。

先说一个基本的例子。让我们创建一个向数组添加任何已定义类型的方法:

如果我们想为一个`int`类型创建相同的实用程序呢？要不要重做同样的方法？通过简单地使用泛型，我们可以重用代码，而不是添加更多的样板文件:

我们如何防止不需要的类型在`T`中被使用？为此，我们可以使用`extends`关键字:

泛型将使我们能够为我们的类型构建全面的动态接口。它们是我们日常开发中必须掌握的特性。

# 3.元组

什么是元组？让我们来看看定义:

> 元组类型允许您用固定数量的元素来表示数组，这些元素的类型是已知的，但不必相同。例如，您可能希望将一个值表示为一对`string`和`number`。— [打字稿的文档](https://www.typescriptlang.org/docs/handbook/basic-types.html)

最重要的一点是，这些数组的长度是固定的。有两种方法来定义元组:

*   明确地:

```
const array: [string, number] = ['test', 12];
```

*   隐含地:

```
const array = ['test', 12] as const;
```

唯一的区别是`as const`将使数组只读，这在我看来更可取。

注意，元组也可以被标记:

标签不要求我们在析构时用不同的名字命名变量。它们纯粹是为了文档和工具。标签将有助于使我们的代码更具可读性和可维护性。

注意，使用带标签的元组时有一个重要的规则:当标记一个元组元素时，元组中的所有其他元素也必须被标记。

# 4.映射类型

什么是映射类型？它们是一种避免反复定义接口的方法。您可以将一个类型建立在另一个类型或接口的基础上，从而省去手工操作。

> “当你不想重复自己的时候，有时候一种类型需要建立在另一种类型的基础上。
> 
> 映射类型建立在索引签名的语法之上，索引签名用于声明尚未提前声明的属性类型。”— [打字稿的文档](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)

总而言之，映射类型允许我们在现有类型的基础上创建新的类型。

TypeScript 附带了许多实用程序类型，所以我们不必在每个项目中重写它们。我们来看一些最常见的:`Omit`、`Partial`、`Readonly`、`Readonly`、`Exclude`、`Extract`、`NonNullable`、`ReturnType`。

让我们来看看他们中的一个。假设我们想要将一个名为`Teacher`的实体的所有属性转换为`readonly`。我们可以使用什么工具？我们可以使用`Readonly`实用程序类型。让我们来看看它的实际应用:

让我们回顾一下`Readonly`是如何工作的:

```
type Readonly<T> = { readonly [P in keyof T]: T[P]; }
```

现在，让我们创建我们的自定义实用程序来取乐。让我们反转`Readonly`类型来创建一个`Writable`类型:

*注:注意* `*-*` *修饰语。在这个场景中，它被用来移除* `*readonly*` *修改器。它可以用来从* `*?*` *等属性中移除其他修改器。*

# 5.防护类型

类型守卫是一套帮助我们缩小对象类型的工具。这意味着我们可以从更一般的类型到更具体的类型。

有多种技术可以执行类型保护。对于本文，我们将只关注用户定义的类型保护。这些基本上是断言——类似于任何给定类型的函数。

我们如何使用它们？我们只需要定义一个返回类型为 a `type predicate`的函数，它返回`true/false`。让我们看看如何将一个`typeof`操作符变成一个类型保护函数:

注意，如果`isNumber`检查是`false`，TypeScript 可以假设该值将是一个字符串，因为`x`可能是`string`或`number`。

让我们看另一个使用自定义接口的类型保护的例子:

注意`isHunter`函数的返回类型是`x is Hunter`。断言函数将是我们的类型守卫。

类型保护是有作用域的。在`isHunter(x)`代码块中，`x`变量属于`Hunter`类型。这意味着我们可以安全地调用它的`hunt`方法。但是，在这个代码块之外，`x`类型仍然是`unknown`。

# 最后的想法

在本文中，我们只是探索了我们所拥有的最重要的 Typescript 特性。由于这只是一个概述，我们只是触及了它们的表面。

我的目标是让您好奇并展示 Typescript 的能力。现在就看你能不能深入其中任何一个了。

通过尝试逐步采用它们，你会发现你的代码变得更整洁、更容易维护。

你不是中等会员吗？支持我[在这里](http://dioxmio.medium.com/membership)成为其中一员。

# 相关文章

[](/typescript-a-gentle-introduction-to-mapped-types-f65e45fa2598) [## TypeScript:映射类型的简明介绍

### 学习构建自己的一套 TypeScript 工具

better 编程. pub](/typescript-a-gentle-introduction-to-mapped-types-f65e45fa2598) [](/getting-strict-with-typescript-2e906b48c0a) [## 对类型脚本越来越严格

### 使您的类型脚本代码更具可读性和类型安全

better 编程. pub](/getting-strict-with-typescript-2e906b48c0a) [](/master-typescripts-type-guards-1fd5436bc6f2) [## Master TypeScript 的类型保护

### 在 TypeScript 的 type guards 中从初学者到专业人员

better 编程. pub](/master-typescripts-type-guards-1fd5436bc6f2)
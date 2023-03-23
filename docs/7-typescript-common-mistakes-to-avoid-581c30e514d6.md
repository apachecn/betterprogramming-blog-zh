# 要避免的 7 个打字稿常见错误

> 原文：<https://betterprogramming.pub/7-typescript-common-mistakes-to-avoid-581c30e514d6>

## 通过避免这些常见错误来改进您的类型脚本代码库

![](img/d90a3b527b154281b7e0b89580a0144a.png)

来自 [Pexels](https://www.pexels.com/photo/yelling-formal-man-watching-news-on-laptop-3760778/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Andrea Piacquadio](https://www.pexels.com/@olly?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的照片

自 2012 年 10 月首次出现以来，TypeScript 已经获得了广泛的关注，它已经成为 web 开发领域真正的游戏规则改变者。虽然，有些人一直对使用它持怀疑态度。

当将 TypeScript 添加到项目中时，开发人员接受它而不是反对它是很重要的。这可能会导致挫折感，并让人觉得 TypeScript 已经成为一个瓶颈。然而，如果使用得当，拥有一个可读且易于维护的代码库变得至关重要。它有很好的特性，比如映射类型、重载、类型推断、可选类型等等，并且随着不断升级，这些特性越来越好。

为什么有些人觉得 TypeScript 损害了他们的生产力？怎么才能防止这种坏名声呢？有没有一些我们可以采纳的最佳实践？

这里，我们将讨论使用 TypeScript 时最常见的错误。通过不陷入这些通常的怀疑，我们将看到我们的生产力和代码可维护性的提高。

# 1.不启用严格模式

如果没有 TypeScript `strict`模式，打字可能会太松，这将使我们的代码库不那么类型安全。这会给人一种错误的印象，因为有些人认为通过添加 TypeScript，所有的打字问题都会自动得到解决。

以后，我们将成为这些类型的受害者。我们可能最终用补丁来修复它们，而不是修复根本原因。这可能会导致认为该工具没有做好工作。

如何才能启用严格模式？通过将`tsconfig.json`文件中的`strict`参数设置为真来启用，如下图所示:

启用`strict`模式将启用钩下:

*   `noImplicitAny`:这个标志防止我们暴露带有推断`any`的合同。如果我们不指定类型，无法推断，那么将默认为`any`。
*   `noImplicitThis`:防止`this`关键字不必要的不安全使用。防止不必要的行为将使我们免于一些调试难题，如下所示:

*   `alwaysStrict`:这将确保`use strict`在我们所有转换的 JavaScript 文件中发出，除了编译器。这将提示 JavaScript 引擎代码应该在`strict mode`中执行。
*   `strictBindCallApply`:这将确保我们使用正确的参数调用`call`、`bind`和`apply`函数。让我们看一个例子:

*   `strictNullChecks`:如果该标志为`off`，`undefined`，`null`和`false`被编译器忽略。这种松散的类型会在运行时导致意外的错误。让我们看一个例子:

*   `strictFunctionTypes`:有此标志`on`可确保更彻底地检查功能参数。
*   `strictPropertyInitialization`:当设置为`true`时，这将强制我们在构造函数中设置所有的属性值。

正如所见，TypeScript 的`strict`变量是上述所有标志的简写。我们可以通过使用`strict`全部启用它们，或者逐个渐进地启用它们。

更严格的类型将有助于我们在编译时捕获更多的 bug。

# 2.重新声明接口

在对组件接口进行类型化时，通常需要相同类型的不同接口变体。这些可以在一个或两个参数上变化。一个常见的错误是手动重新定义这些变化。这将导致:

*   不必要的样板。
*   需要多方面的改变。如果一个属性在一个地方发生了变化，那么这个变化需要传播到许多文件。

很久以前，TypeScript 提供了一个功能来解决这个问题:映射类型。它们让我们根据我们定义的一些规则，在现有类型的基础上创建新类型。这确实会产生可读性和声明性更强的代码库。

让我们看一个例子:

在上面的代码中，我们保留了一个真实的来源:实体`Book`。它的所有变体都使用映射类型特性来表达，这大大减少了对代码进行类型化和维护的需求。

映射类型也可以应用于联合，如下所示:

TypeScript 附带以下映射类型:`Omit`、`Partial`、`Readonly`、`Exclude`、`Extract`、`NonNullable`、`ReturnType`。

我们可以创建自己的实用程序，并在我们的代码库中重用它们。

# 3.不依赖于类型推断

TypeScript 推理是这种编程语言最强大的工具之一。它为我们做了所有的工作。我们只需要确保所有的部分加在一起，尽可能少的干预。

实现这一目标的关键操作者是`typeof`。它是一个类似于 JavaScript 的操作符。它不是返回 JavaScript 类型，而是返回 TypeScript 类型。使用这个操作数可以避免我们重新声明相同的类型。

让我们看一个例子:

在上面的代码中，注意`result`参数类型。靠`ReturnType<typeof addNumber>`比加`number`型更可取。通过硬编码`number`类型，我们正在做编译器的工作。最好使用适当的语法来表达我们的类型。TypeScript 将为我们做繁重的工作。

让我们看一个虚拟的例子:

请注意，`Book`接口用于特定的场景，甚至不需要创建接口。

通过依赖 TypeScript 的推理，代码变得不那么杂乱，更易于阅读。这里有一个例子:

TypeScript 甚至有`infer`操作符，它可以与映射类型结合使用，从另一个类型中提取一个类型。

在上面的例子中，我们可以看到如何提取数组的类型。

# 4.重载的不正确使用

TypeScript 支持本机重载。这很好，因为它可以提高我们合同的可读性。但是，它不同于其他类型化重载语言。

有些场景可能会使我们的代码更加复杂和冗长。为了防止这种情况，我们需要记住两条规则:

## 1.避免编写仅尾部参数不同的几个重载

您可以看到两个接口是如何相等的，但是第一个接口比第二个接口更冗长。在这种情况下，最好使用可选参数。

## 2.避免编写只有一种参数类型不同的重载

像前面的例子一样，第一个接口变得非常冗长。最好使用联合。

# 5.使用函数类型

TypeScript 附带了`Function`类型。这就像使用关键字`any`一样，但只用于函数。遗憾的是，启用`strict`模式不会阻止我们使用它。

下面介绍一下`Function`型:

*   它接受任意数量和类型的参数。
*   返回类型始终是`any`。

让我们看一个例子:

在上面的代码中，通过使用一个显式的函数定义，我们的回调函数更加易读和类型安全。

# 6.依靠第三方实现不变性

当使用函数式编程范式时，TypeScript 会有很大的帮助。它提供了所有必要的工具来确保我们不会改变我们的对象。我们不需要在我们的代码库中添加沉重的库，比如 ImmutableJS。

让我们通过下面的例子来看看我们可以使用的一些工具:

正如你从上面的例子中看到的，我们有很多工具来保护我们的对象免于变异。

通过使用内置的特性，我们将保持我们的包轻便，类型一致。

# 7.不理解推断/从不关键字

`infer`和`never`关键字很方便，在许多情况下都有帮助，例如:

## 暗示

使用`infer`关键字就像告诉 TypeScript，“我想把你推断在这个位置的任何东西赋值给一个新的类型变量。”

让我们看一个例子:

在上面的代码中，随着`array`扩展`infer U[]`，变量`X`将等于一个`Number`。

## 从不

`never`类型表示从不出现的值的类型。

让我们看一个例子:

在上面的代码中，我们可以使用`never`类型来表达我们不希望某个属性可赋值。

出于同样的目的，我们可以使用`Omit`映射类型:

然而，你可以看到它的缺点。它更冗长。如果你检查`Omit`的内部，它使用`Exclude`，而`Exclude`又使用`never`类型。

依靠`infer`和`never`关键字，我们可以省去复制任何类型的麻烦，更好地表达我们的接口。

# 摘要

这些指南很容易遵循，旨在帮助您接受 TypeScript，而不是与之对抗。TypeScript 旨在帮助您构建更好的代码，而不是妨碍您。

通过应用这些简单的技巧，你将拥有一个更好的、更简洁的、易于维护的代码库。

我们是否遗漏了在你的项目中经常发生的一些常见错误？请在评论中分享。

干杯。

不是中等会员？支持我[成为这里的](http://dioxmio.medium.com/membership)。

# 有关系的

[](/top-5-typescript-features-you-should-master-2358db9ab3d5) [## 您应该掌握的五大打字稿功能

### 使用这些必须知道的特性来提高您的打字稿知识

better 编程. pub](/top-5-typescript-features-you-should-master-2358db9ab3d5) [](/whats-new-in-typescript-4-4-e17d63b84b86) [## TypeScript 4.4 有什么新功能？

### 改进的控制流分析、索引签名等等

better 编程. pub](/whats-new-in-typescript-4-4-e17d63b84b86) [](/mastering-function-overloading-in-typescript-97108369570a) [## 在 TypeScript 中掌握函数重载

### 使用重载功能创建更全面、更易读的函数类型

better 编程. pub](/mastering-function-overloading-in-typescript-97108369570a)
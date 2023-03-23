# 6 种类型脚本代码模式，使您的代码更加健壮

> 原文：<https://betterprogramming.pub/6-typescript-code-patterns-to-make-your-code-more-robust-bbe54354f39f>

## 您可以在日常工作中应用的简单实用的解决方案

![](img/76eb7b7e9aebfa084c5d2724816f81d5.png)

照片由 [Vipul Jha](https://unsplash.com/@lordarcadius?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我发现我在日常工作中重复使用了一些 TypeScript 代码模式。这些代码模式不重也不复杂，而且它们也不需要大量样板代码。它们只是解决一些常见问题的简单而实用的解决方案。

在接下来的几节中，我将分享我最喜欢的六种类型脚本代码模式。

*向前跳转:*

*   [使用 Pick 实现接口分离原则](#2ce1)
*   [从文字表达式中派生类型的常量断言](#eeab)
*   [使用“从不”类型进行彻底检查](#1058)
*   [使用不透明类型模拟名义键入行为](#d807)
*   [从对象类型中查找属性类型](#6021)
*   [用析构赋值重构过多的函数参数](#dd51)

# 使用 Pick 实现接口分离原则

界面分离原理定义为:

> 不应该强迫客户实现他们不使用的接口。

假设我们有一个`Client`类型，它被用在很多地方。有时只使用属性的子集。根据接口分离原则，类型为的函数参数应该是所需的最小类型。

我们可以使用`[Pick](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys)`实用程序类型从`Client` 类型中排除未使用的属性。如下面的代码片段所示，只选择了`name`和`active` 属性。因此，`ClientSummary`类型代表一个更小、更具体的接口，只包含它需要的属性。

选择实用程序类型

另一种方法是使用如下`[Exclude](https://www.typescriptlang.org/docs/handbook/utility-types.html#excludetype-excludedunion)`实用程序类型:

```
type ClientSummary = Exclude<Client, 'dateOfBirth'>;
```

然而，`Pick`更健壮，因为当额外的属性被添加到`Client`类型时，不需要任何改变。

# 从文字表达式派生类型的 Const 断言

`as const`是一个名为 [const assertion](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-4.html#const-assertions) 的文字值的类型化构造。当`as const`被应用时，我们得到一个 const 对象，它的每个属性都是非加宽的文字类型。我们可以用它从带有 const 断言的声明常量中派生类型。

下面的代码片段是我在另一篇文章中讨论过的一个例子。简而言之，我们从`payGrades`常数中推导出`payGradeType`和`payValueType`。这些类型形成了一个约束类型系统。当源常量被修改时，所有相关类型都将自动更新。

通过使用 const 断言从文本表达式派生类型，您可以创建具有更具体和更准确类型的变量，这有助于提高类型的类型安全性和正确性。

# “从不”类型的详尽检查

`never`类型表示从不出现的值的类型。这对于执行详尽的检查非常有用，因为您可以使用`never`类型来确保在 switch 语句或其他控制流结构中处理所有可能的情况。

类型的一个很好的应用是作为不可能类型的类型保护。

下面的示例显示了如何涵盖所有可能的文本类型值。由于`DataTypes`只包括两种文字类型`client`和`order`，所以`assertUnreachable`永远不会到达。

如果另一个开发人员在`DataTypes`中添加了一个新的文字类型，并且忘记更新 switch 语句，将会抛出一个编译时错误。

有了详尽的类型检查，我们可以在编译时而不是运行时检测缺失的情况。

# 使用不透明类型来模拟名义类型行为

TypeScript 是一种结构类型系统。在[结构类型系统](https://www.typescriptlang.org/play#example/structural-typing)中，两种相同形状的类型兼容。这使得 TypeScript 非常灵活，但也可能产生潜在的问题。

在下面这个虚构的例子中，我们有两个兼容的类型`Customer`和`VIPCustomer`。函数`getVIPName`应该只接受`VIPCustomer` 类型的参数。但是，如果`Customer`类型参数被错误地传入，由于结构类型的限制，它不会出错。

上述问题可以通过不透明类型来解决。不透明类型允许您创建名义上是类型化的类型，但仍然与结构类型兼容。

尽管 TypeScript 不支持不透明类型开箱即用，但我们可以使用交集类型实现类似的行为。

以下`OpaqueType`利用[普通型](https://www.typescriptlang.org/docs/handbook/2/generics.html)和交集型。现在`Customer`型和`VIPCustomer`型结构不同。因为内部的`_brand`属性只存在于编译时，所以没有与之相关的运行时开销。

不透明类型实现可以使您的 TypeScript 代码在正确的地方使用时更干净、更强大。

# 从对象类型中查找属性类型

[类型脚本是关于类型的](https://blog.logrocket.com/type-flowing-rethinking-typescript-typing-system/)。通常，我们需要从复杂的对象类型中提取现有的对象属性类型。

我们使用条件类型和`never`来过滤出下面`lookup` 类型定义中所需的数据类型定义。

`lookup` 型乍一看可能会令人困惑。我们来分解一下。

首先，为了访问一个属性类型，我们将创建一个带有递归类型别名的新类型。

当`Path extends keyof T`为真时，表示完整路径匹配。因此，我们返回当前的属性类型。

当`Path extends keyof T`为 falsy 时，我们使用`infer`关键字构建一个模式来匹配`Path`。如果匹配，我们递归调用下一级属性。否则，它将返回一个`never` ，这意味着`Path`与类型不匹配。

如果不匹配，则以当前属性作为第一个参数继续递归。

生成的属性类型可以在其他函数中使用，以加强类型安全。

# 用析构赋值重构过多的函数参数

TypeScript 代码的一个常见问题是函数参数过多。我们可以使用析构赋值来重构过多的函数参数，让你的代码更加简洁易读。

析构赋值允许你将一个对象或数组的值解包到单独的变量中。当您的函数有多个相关的参数时，这可能会很有用，因为您可以使用析构赋值将值解包到单个变量中，并将它们作为单独的参数传递给函数。

下面的例子说明了参数过多的问题。例如，如果一个新的参数类型，比如说`middleName`是必需的。我们需要非常小心地将参数添加到正确的位置。

很明显，很难维持。读取调用函数以将参数与其名称匹配也很困难。

为了重构它，我们使用对象析构和对象文字，如下所示。这是我最喜欢的方法之一，因为它很简单。简洁易读。

TypeScript 是智能的。它不仅将值赋给相应的变量，还能正确地推断出类型。

重构之后，参数的顺序不再重要。您可以添加或删除属性，而不必担心其位置。

TypeScript 打字系统太神奇了。开发人员面临的挑战是充分利用其强大的工具箱来开发干净和强类型的代码。

希望这篇文章对你有用。如果您有自己喜欢的类型脚本代码模式，请告诉我。

如果你喜欢这篇文章，你也可以看看我的另一篇关于 TypeScript 最佳实践的文章。
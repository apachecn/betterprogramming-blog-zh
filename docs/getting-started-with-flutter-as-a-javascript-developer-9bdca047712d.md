# 作为 JavaScript 开发人员开始使用 Flutter

> 原文：<https://betterprogramming.pub/getting-started-with-flutter-as-a-javascript-developer-9bdca047712d>

## 使您从 JavaScript 到 Dart 的过渡更加容易

![](img/a2dc365aa782b3afa11004f8b96869b5.png)

马特·邓肯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

早在 2011 年，Dart 就在丹麦奥胡斯的 GOTO 大会上亮相。它没有一个很好的开始。这被视为网络开发的进一步分裂。谷歌计划将其作为 Chrome 虚拟机的一部分。不确定其他供应商是否会采用它。它的诞生是为了解决许多后来由新的 EMACScript 版本解决的问题。

直到 2018 年 Flutter 的发布，Dart 才开始再次获得牵引力。Flutter 是一款开源的 UI 软件，它的原则是“一次编写，随处运行。”有了一个 Dart 代码库，您的应用程序就可以为 iOS、Android、桌面和 web 应用程序而构建。这和反应原生但做对的原理是一样的。

那么，Dart 的学习曲线是怎样的呢？有 web 开发背景的人更容易学习吗？的确是。在本文中，我们将探讨如何轻松地从 JavaScript 转换到 Dart。

# 基本原则

在直接进入编码之前，让我们看看 JavaScript 和 Dart 的主要区别和相似之处。

## 主要差异

*   Dart 基于经典继承，而 JavaScript 基于原型继承。
*   Dart 是一种比 JavaScript 更严格的语言。它有打字。就像在 TypeScript 中一样，注释是可选的，因为 Dart 可以推断类型。
*   Dart 比 JavaScript 快两倍。
*   Dart 是开源的。JavaScript 是基于 ECMAScript 标准的特定于供应商的。

## 主要相似之处

*   Dart 只是像 JavaScript 一样的单线程语言。它们都通过事件循环来处理代码。这意味着您理解不劫持 UI 和依赖异步调用的重要性。
*   两种语言都有垃圾收集过程。您不必再处理分配和内存问题。您仍然应该对垃圾收集过程有很好的理解。
*   Dart 从 2.12 版本开始支持`sound null safety`。类似于 TypeScript 的`strictNullChecks`。
*   两种语言都支持多种编程范例。
*   函数在两种语言中都是一等公民。
*   两种语言都支持异步编程。

# 基元

在 JavaScript 中，我们有以下原语:`undefined`、`String`、`Number`、 `BigInt`、`Boolean`、`Symbol`。它们都有相应的包装对象。例如:

```
const foo = new Boolean(false);
```

这将对应于达特的`int`、`double`、`String`、`bool`和`Null`。然而，有一个很大的区别:Dart 没有原语。以下类型除了`Null`都是从`Object`接口延伸出来的。

Dart 示例:

JavaScript 等价物:

即使您没有为变量指定任何类型，Dart 仍然会推断出值并输入该变量:

这类似于 TypeScript 的推断机制。但是，您可以使用动态类型。如果你想选择输入你的变量，你可以使用`Object`或者`Object?`类型。由于所有类型都是从`Object`扩展而来，所以你不会得到任何错误。`Object`和`Object?`的区别在于`Object?`接受空值，而`Object`不接受:

上面的代码类似于在 TypeScript 中这样做:

选择进入`sound null safety`时，除非明确指出，否则变量不能是`null`。当我们需要一个类型可以为空时，我们必须添加`?`修饰符:

奇怪的是，Dart 可能会做出错误的假设，如下面的代码所示:

为了解决这个问题，我们可以使用`late`关键字，告诉编译器它将在以后被初始化:

# 条件式

条件是任何开发人员日常工作的基础。Dart 中的语法类似。有一个关键的区别:条件必须有一个静态类型`bool`。

这意味着您不能使用 JavaScript 中最酷的东西之一:断言反对 truthy/falsy 对象值。

让我们看看 Dart 中的一个非工作示例:

为了修正上面的例子，我们应该做:

如前所述，Dart 是一种比 JavaScript 更严格的语言。一个直接的后果是它不支持`===`和`!==`操作数。要比较对象，我们不需要三重操作数。那么我们如何比较实例呢？通过使用`identical`功能:

上面代码的输出将是`same value`。由于两个对象不同，`same instance`不会被打印。

Dart 为每个对象实例生成不同的`hashCode`。

```
// 3 ways to check that 'a' and 'b' objects are from the same object instanceidentical(a,b);identicalHasCode(a) === identicalHasCode(b);a.hashCode === b.hashcode;
```

# 功能

一个共同的特点是 Dart 和 JavaScript 将函数视为一等公民。它们可以作为参数传递，存储在变量中，或者从函数中返回。这意味着两种语言接受不同的编程范式。由开发人员决定哪一个更适合他们的用例。

函数的语法类似于 JavaScript 中的。如果参数类型丢失，它们将在运行时被推断出来。

让我们看一些 Dart 示例:

即使允许，也不是好的做法。最好提示编译器一下。

要使 Dart 中的参数可选，您必须使用特殊的语法来定义它们。可选参数需要放在方括号中。这些参数只允许使用默认值。

让我们来看一个例子:

箭头功能也是 Dart 的特色。它们可以用几种方式来定义:

您不必像在 ES6 中那样将它们存储在变量中。

# 承诺

ES6 版本的 JavaScript 实现了承诺。ES7 引入了`async`和`await`的概念，这将让我们以更线性的方式编写 JavaScript。

让我们回顾一下 JavaScript 的语法:

在 Dart 中，这个概念非常相似。异步功能`async`和`await`以及`Promises`存在。在 Dart 中它们被称为`Futures`。

让我们看看 Dart 中的一个示例:

上面的语法确实非常类似于 JavaScript。

# 最后的想法

我们已经看到 Dart 和 JavaScript 是非常相似的语言。它们共享一些基本原理和语法。我们只是触及了 Dart 的表面。然而，从一个转换到另一个应该很简单。JavaScript 背景有一个小的学习曲线。

如果你有打字稿的知识，这条曲线会更小。这是因为 JavaScript 不是类型安全的，这是一个有意义的变化。然而，一旦你习惯了打字，这将成为公园里的散步。

干杯！

# 相关文章

[](/typescript-a-gentle-introduction-to-mapped-types-f65e45fa2598) [## TypeScript:映射类型的简明介绍

### 学习构建自己的一套 TypeScript 工具

better 编程. pub](/typescript-a-gentle-introduction-to-mapped-types-f65e45fa2598) [](/7-habits-of-productive-developers-bce60d880907) [## 高效开发人员的 7 个习惯

### 建立正确的习惯来保持每天的工作效率

better 编程. pub](/7-habits-of-productive-developers-bce60d880907)
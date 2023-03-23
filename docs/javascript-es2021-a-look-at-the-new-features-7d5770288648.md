# JavaScript ES2021:看看新特性

> 原文：<https://betterprogramming.pub/javascript-es2021-a-look-at-the-new-features-7d5770288648>

## 新 ES12 规格概述

![](img/0af17df8f8520378a84e0e612052b2cc.png)

图片由作者提供。

新的 ES12 规格终于来了。规格？JavaScript 确实不是开源语言。这是一种遵循 ECMAScript 标准规范编写的语言。TC39 委员会负责讨论和批准新特性。他们是谁？

> “ECMA International 的 TC39 是一个由 JavaScript 开发人员、实现人员、学者等组成的团体，他们与社区合作维护和发展 JavaScript 的定义。”— [TC39.es](https://tc39.es/)

他们的释放过程由五个阶段组成。自 2015 年以来，他们一直在进行年度发布。它们通常发生在春天。下一个申请批准的日期是 6 月 25 日.

有两种方法可以引用任何 ECMAScript 版本:

*   按年份:这个新版本将是 ES2021。
*   根据它的迭代编号:这个新版本将是第 12 次迭代，所以它可以被称为 ES12。

那么这个版本中有什么新内容呢？哪些功能可以让我们感到兴奋？

# 全部替换

一个新的功能被添加到`String`原型中。在此之前，如果不使用正则表达式，就不可能替换子字符串的所有实例。

ES12 之前:

现在:

如果搜索参数是空字符串会发生什么？它将返回每个 UCS-2/UTF-16 代码单元之间的替换值。

```
'x'.replace('', '_');
// '_x''xxx'.replace(/(?:)/g, '_');
// '_x_x_x_''xxx'.replaceAll('', '_');
// '_x_x_x_'
```

网上有很多关于这个话题的问题。因此，我们可以推断这将是一个有用的功能。不需要任何正则表达式知识使它更容易访问。

# 承诺。任何

新的`Promise.any`方法是另一个有用的工具。它以一系列承诺作为论据。当满足其中任何一个条件时，它将触发`Promise.any()`参数回调或返回响应。这要看你是不是在用`async/await`了。

如果所有的承诺都失败了，这个方法将抛出一个`AggregateError`,它组合了所有不同的承诺错误。为什么不返回一个普通数组呢？主要是为了兼容性。它将是`Error`的一个实例，您将得到一个堆栈跟踪。有这些信息以备不时之需是件好事。

让我们看一些例子。

*   使用传统的`callback`语法:

*   使用`async/await`语法:

让我们检查一个错误示例场景:

这是`Promise`原型的第四个附加物。简单回顾一下，目前我们有以下资源可供使用:

*   `[ES2020] Promise.allSettled`:这个方法返回一个承诺，当所有给定的承诺都被实现或拒绝时，这个承诺就解决了。返回的对象描述了每个单独的承诺结果。
*   这个方法返回一个只有当所有的目标承诺都实现时才实现的承诺。
*   `[ES2015] Promise.race`:该方法将返回一个承诺，一旦其中一个承诺被拒绝或履行，该承诺将立即履行。

# WeakRefs

JavaScript 有一个自动垃圾收集过程。它只能收集不可及的对象。每次你给一个对象赋值的时候，你都在创建一个`strong reference`。这可以保护它不被垃圾收集。

```
// x is a strong reference to the object
const x = { foo: 'bar' };
```

`WeakRef`服务于一个完全不同的用例:保存一个对象的引用，该对象不会保护它免于垃圾收集。因为垃圾收集是不确定的，所以没有真正的保证对象什么时候会被清除，甚至是否会被清除。`WeakRef`利用了这一点，允许您访问该对象，直到它被收集。

`WeakRef`只能带一个宾语作为论元。

```
function Foo() {}// strong reference to a Foo instance
const x = new Foo();// weak reference to the Foo's instance
const xWeak = new WeakRef(x);
```

我们如何取回值？通过使用`deref()`方法。请记住，该方法将返回对该对象的强引用。

这为什么有用？它允许您根据用户的设备提高应用程序的性能。我们可以使用`WeakRefs`来缓存大对象。这意味着拥有更多内存的机器可以看到应用程序性能的提高。那些内存有限的仍然会工作，不会吃掉用户的内存。

# 终结器

终结器是 ES12 在内存区域的另一个特性。这个特性的作用是让你知道一个对象什么时候被垃圾收集了。它通过 JavaScript 回调来实现。

但是，有几件事要记住:

*   不保证回调会执行。
*   目标对象已被清除，将无法访问。
*   回调将执行多长时间是不确定的。可以是一分钟，也可以是一个小时。

这个特性让您有机会做进一步的清理，以帮助优化您的应用程序。

*注意:这不是你想要运行关键代码的地方。这是一个旨在帮助进一步减少我们的记忆网络应用程序的足迹的地方。*

# 逻辑赋值运算符

最后，这些新的 ES12 规格配备了期待已久的运营商。新运算符将逻辑赋值运算符与逻辑运算`&&`、`||`和`??`结合在一起。

## 运算符&&=

让我们将它在 ES11 中的对等物与新规范进行比较。

之前:

```
x && (x = y)
```

现在:

```
x &&= y;
```

示例:

## 运算符||=

让我们将它在 ES11 中的对等物与新规范进行比较。

之前:

```
x || (x = y)
```

现在:

```
x ||= y;
```

示例:

## 接线员？？=

`??`是 JavaScript 的 nullish 合并操作符。让我们回顾一下它是做什么的，因为它不太常见。

> **无效合并运算符(** `**??**` **)** 是一个逻辑运算符，当其左侧操作数为`null`或`undefined`时，返回其右侧操作数，否则返回其左侧操作数— [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator)

之前:

```
x ?? (x = y);
```

现在:

```
x ??= y;
```

示例:

# 数字分隔符

这种添加可能不那么花哨，但它确实有助于提高代码的可读性。目前，当在`var/let/const`变量中存储一个长数字时，它可能是不可读的。

让我们看一个例子。让我们创建一个 100 万的常数。显而易见，长数字很难阅读。

```
const oneMillion = 1000000;
```

使用新的 ES12 语法，您可以使用`_`字符在数字之间创建分隔符。

```
const oneMillion = 1_000_000;
```

它可以用于`binary`和`Hex`文字:

```
const max8bits = 0b1111_1111;const message = 0xA0_B0_C0;
```

这肯定是我会经常使用的东西。

# 最后的想法

这是一个有趣的版本，提供了许多小而有用的特性，如`logical assigment operators`、`replaceAll`和`numerical separators`。你已经可以开始使用它们，并通过巴别塔传输它们。如果您使用 TypeScript，那么这项工作已经为您完成了。

还增加了一些高级功能，如`WeakRefs`和`Finalizers`。尽管它们很复杂，但在使用之前需要很好地理解它们。否则，他们会带来比他们解决的更多的问题。它们涵盖了一些特定的场景，不应该被滥用。

我希望这篇文章能让你像我一样对新的 ES2021 规格感到兴奋。

不是中等会员？支持我[成为其中一员。](http://dioxmio.medium.com/membership)

# 相关文章

[](/5-common-javascript-memory-mistakes-c8553972e4c2) [## 5 个常见的 JavaScript 内存错误

### 避免应用程序内存泄漏的技巧

better 编程. pub](/5-common-javascript-memory-mistakes-c8553972e4c2) [](/7-tips-to-write-efficient-and-performant-javascript-code-bccbdb9662ae) [## 编写高效高性能 JavaScript 代码的 7 个技巧

### 提升您的 JavaScript 应用程序的性能

better 编程. pub](/7-tips-to-write-efficient-and-performant-javascript-code-bccbdb9662ae)
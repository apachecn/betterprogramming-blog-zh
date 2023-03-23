# 不要在 JavaScript 中使用 If-Else 和 Switch，使用对象文字

> 原文：<https://betterprogramming.pub/dont-use-if-else-and-switch-in-javascript-use-object-literals-c54578566ba0>

## 用对象文字在 JavaScript 中编写更好的条件

![](img/49d326ebcd5f9d80180e9d187ae07da4.png)

[Jac Alexandru](https://unsplash.com/@rolls0ut?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

用 JavaScript 编写复杂的条件总是有可能产生一些相当混乱的代码。长长的`if/else`语句或`switch`案例列表会很快变得臃肿。

当有多个条件时，我发现对象文字是结构化代码最易读的方式。让我们看看它们是如何工作的。

举个例子，假设我们有一个函数获取一个[押韵俚语](https://en.wikipedia.org/wiki/Rhyming_slang)短语并返回其含义。使用`if/else`语句，它会是这样的:

这不太好。不仅可读性不强，而且我们还在为每条语句重复`toLowerCase()`。

我们可以通过在函数开始时将小写字母赋给一个变量来避免这种重复，或者使用一个`switch`语句，如下所示:

我们现在只调用`toLowerCase()`一次，但这仍然感觉不那么可读。`switch`语句也容易出错。在这种情况下，我们只是返回一个值，但是当你有更复杂的功能时，很容易错过一个`break`语句并引入错误。

# 另一种选择

你可以使用一个对象以一种更简洁的方式实现与上面相同的功能。让我们来看一个例子:

我们有一个对象，其中的键是条件，值是响应。然后我们可以使用方括号符号从传入的韵中选择对象的正确值。

第 10 行的最后一部分(`?? “Rhyme not found”`)使用 [nullish 合并](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator)来分配默认响应。这意味着如果`rhymes[rhyme.toLowercase()]`是`null`或`undefined` (而不是`false`或`0`)，那么返回默认字符串“找不到韵脚”。这很重要，因为我们可能会合理地希望从条件语句中返回`false`或`0`。例如:

这是一个非常人为的例子，但是希望它说明了 nullish 合并是如何帮助避免引入 bug 的！

# 更复杂的逻辑

有时候你可能需要在你的条件中做一些更复杂的逻辑。为此，您可以将一个函数作为值传递给对象键，并执行响应:

我们选择想要做的计算并执行响应，传递两个数字。您可以使用[可选链接](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)(最后一行代码中的`?.`)来仅执行已定义的响应。否则，返回默认的返回字符串。

# 结论

编写条件句永远是个人喜好的问题，某些情况下需要不同的方法。然而，我发现当我有几个条件要检查时，对象文字是可读性和可维护性最好的方式。

我很想听听你的想法，或者你是否有不同于以上概述的方法！
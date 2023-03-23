# 在 JavaScript 函数中重构 If/Else 语句的 5 种方法

> 原文：<https://betterprogramming.pub/5-ways-to-refactor-if-else-statements-in-javascript-functions-2865a4bbfe29>

## 让你的代码运行得更快，看起来更整洁

![](img/6cb536f82de8fe2207bb5c209846fbe1.png)

照片由 [Tim Chow](https://unsplash.com/@timchowstudio?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在这篇文章中，我将介绍五种通过去除不必要的`if-else`语句来整理代码的方法。我将讨论默认参数、OR ( `||`)操作符、无效合并、可选链接、`no-else-returns`和保护子句。

# 1.默认参数

你知道当你使用一个不一致的 API 并且你的代码因为一些值是`undefined`而中断时的感觉吗？

```
let sumFunctionThatMayBreak = (a, b, inconsistentParameter) => a+b+inconsistentParametersumFunctionThatMayBreak(1,39,2) // => 42
sumFunctionThatMayBreak(2,40, undefined) // => NaN
```

对于许多人来说，解决这个问题的本能方法是添加一个`if/else`声明:

然而，您可以通过实现[默认参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)来简化上述功能并取消`if/else`逻辑:

```
let simplifiedSumFunction = (a, b, inconsistentParameter = 0) => a+b+inconsistentParametersimplifiedSumFunction(1, 39, 2) // => 42
simplifiedSumFunction(2, 40, undefined) // => 42
```

# 2.OR 运算符

使用默认参数并不总能解决上述问题。有时，您可能会遇到需要使用`if-else`逻辑的情况——尤其是在尝试构建条件呈现特性时。在这种情况下，问题通常以这种方式解决:

或者这样:

然而，您可以使用[或(](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR) `[||](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR)` [)操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR)来简化它。`||`操作器的工作方式如下:

*   当左边是一个`falsey`值时，它返回右边。
*   如果是`truthy`，则返回左侧。

解决方案可能是这样的:

# 3.无效合并

然而，有时你确实想保留`0`或`''`作为有效的参数，但你不能用`||`操作符做到这一点(如上面的例子所示)。幸运的是，从今年开始，JavaScript 允许我们使用`??` ( [无效合并](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator))操作符，该操作符仅在左侧为`null`或`undefined`时返回右侧。这意味着如果你的参数是`0`或`''`，它将被如此对待。让我们来看看实际情况:

# 4.可选链接

最后，在处理不一致的数据结构时，很难相信每个对象都有相同的键。看这里:

发生这种情况是因为`object.name`是`undefined`，所以我们不能在上面调用`firstName`。

许多人以下列方式处理这种情况:

但是，您可以使用新的 JS 特性——可选链接——来简化上述操作。可选链接在每一步检查返回值是否是`undefined`，如果是，它就返回这个值，而不是抛出一个错误:

# 5.无其他退货和保护条款

对于笨拙的`if/else`语句，尤其是那些嵌套的语句，最后的解决方案是`[no-else-return](https://eslint.org/docs/rules/no-else-return)` [语句](https://eslint.org/docs/rules/no-else-return)和[守护子句](https://anthonygharvey.com/ruby/guard_clauses_vs_nested_if_statements)。假设我们有这个函数:

## 无-否则-返回

现在，我们可以用`no-else-return`语句简化这个函数，因为无论如何我们返回的都是`null`:

`no-else-return`语句的好处是，如果条件不满足，函数将结束`if-else`的执行，并跳转到下一行。你甚至可以没有最后一行(`return null`)，然后返回将是`undefined`。

*注:我在前面的例子中实际上使用了一个* `*no-else-return*` *函数。*

## 保护条款

现在我们可以更进一步，设置保护措施，更早地结束代码执行:

# 结论

你用什么技巧来避免笨拙的陈述？请在评论中告诉我。
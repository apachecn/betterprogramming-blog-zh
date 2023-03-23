# 4 使用 typeof()的 JavaScript 技巧问题

> 原文：<https://betterprogramming.pub/javascript-trick-questions-using-typeof-d9500288676e>

## 不要被这些基本但偷偷摸摸的 JavaScript 情况所迷惑

![](img/eb8de494e344506f285f30eb1768b162.png)

照片由 [bruce mars](https://www.pexels.com/@olly?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 从 [Pexels](https://www.pexels.com/photo/photo-of-a-woman-thinking-941555/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

英语起初可能很难学，因为它并不总是遵循自己的规则。类似地，JavaScript 也不总是遵循自己的规则。这可能会导致一些令人惊讶和困惑的场景！

虽然这些情况中的大部分不会出现在你作为开发人员的日常生活中，但是我将会分解一些你可能在面试中遇到的或者来自其他试图胜过你的开发人员的问题。让我们开始吧！

# `null`的类型是什么？

如果您 console.log 以下代码:

```
console.log(typeof(null)); 
```

你觉得会有什么回报？肯定不是`object`吧？不对！

根据 JavaScript，这个`typeof`T3 就是`object`。你不同意吗？我也是，事实证明我们都是对的。

*Null 不是宾语。*

这是语言中一个[古老错误](https://2ality.com/2013/10/typeof-null.html)的结果，这个错误不值得深究，但是你要知道`typeof(null)`的输出永远是`object`。

为什么这很重要？如果您想要构建一个只接受`object`而不接受`null`值的测试，那么按照`if (typeof arg === "object")`的思路编写一些东西可能不会如您所愿。

相反，你需要更加具体地阐述你的`if`陈述。比如:`if ((arg !== null) && (typeof arg === “object”))`。

# 数组的类型是什么？

如果您控制台. log 以下内容:

```
let newArr = [];
console.log(typeof(newArr));
```

你认为输出会是什么？这次你可能没被骗；这里的输出也是`object`。

很奇怪，对吧？在 JavaScript 中，数组是一个对象。

同样，当我们需要测试更具体的东西时，这可能是一个问题。解决问题的方法是使用`Array.isArray()`方法:

```
let newArr = [];
console.log(Array.isArray(newArr)) // true
```

# NaN 是什么类型的？

这个例子特别反直觉。这是我们的代码:

```
console.log(typeof(NaN)) // "number"
```

正如我们所料，在 JavaScript 中，`NaN`代表非数字的类型是..一个号码？嗯。

这里的问题是`NaN`代表一个数值结果，它的值不能用合法的数字来表示。

`NaN`是 JavaScript `Math`的产品，所以在某种程度上，尽管它的定义不是数字，但它的类型是`“number”`是有意义的，因为它是 JavaScript 数学的结果，是使用数字计算的。

在这种情况下，我们的替代测试是使用`isNaN()`方法。

# 函数的类型是什么？

任何不是原语的东西(未定义、null、boolean、number、string、bigInt 和 symbol)都是对象。所以，函数应该是一个对象，对吗？

```
let foo = () => {};
console.log(typeof(foo)); // "function" 
```

而`typeof`混乱再次来袭！

首先，*函数是一个对象。*JavaScript 中没有“函数”类型。

也就是说，`typeof`返回八个不同的值:对象、布尔、函数、数字、字符串、未定义、bigInt 和符号。

返回`“function”`的选项虽然可能令人困惑，但对开发人员来说只是一个有用的规范。

# **感谢阅读**

欢迎在下面的评论中添加任何更棘手的`typeof`情况。

如果关于 JavaScript 中对象的讨论让你困惑，看看我的博客文章，解释为什么 JavaScript 中的所有东西都是(不是)对象。
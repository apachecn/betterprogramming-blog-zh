# 3 个有趣的 JavaScript 代码片段可能会让你大吃一惊

> 原文：<https://betterprogramming.pub/3-fun-javascript-code-snippets-that-might-surprise-you-33be87c7fbd5>

## 虽然这些输出真的很神奇，但我不建议在生产中使用它们

![](img/e69e66a8ab95aee0f04c60f26f723b16.png)

照片由 [Aziz Acharki](https://unsplash.com/@acharki95?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在过去的七年里，我一直是一名 JavaScript 开发人员，但即使在今天，我仍然会遇到一些令我惊讶的代码片段。有时候我想知道 JavaScript 是一种编程语言还是一种魔法。

我知道很多人认为 JavaScript 是一种愚蠢的语言，有许多错误，使这些“神奇的片段”能够工作(可能是因为 [Brendan Eich](https://en.wikipedia.org/wiki/Brendan_Eich) 在十天内创造了这种语言)，但我觉得 JavaScript 是一种多功能和有趣的语言。还有，这是我的饭碗。

在这篇文章中，我将通过几个“神奇”的片段。但是，我不建议在您的生产应用程序中使用这些代码，因为这些代码片段只是为了理解概念和敬畏 JavaScript。另外，如果你是一名 JavaScript 面试官，请不要问这样的问题，因为这不能证明你懂 JavaScript。这些只是您在任何生产应用程序中可能不会遇到的语言怪癖。

对于生产应用程序，我建议使用 TypeScript，正如我在上一篇文章中提到的:

[](/how-an-anti-typescript-javascript-developer-like-me-became-a-typescript-fan-a4e043151ad7) [## 像我这样的反类型脚本“JavaScript 开发人员”是如何成为类型脚本爱好者的

### 我从不喜欢 TypeScript 到喜欢它的旅程

better 编程. pub](/how-an-anti-typescript-javascript-developer-like-me-became-a-typescript-fan-a4e043151ad7) 

# 1.平等

我不确定在其他编程语言中是否可能，但在 JavaScript 中，下面的代码片段将为变量`a`的某个值返回`true`:

要知道它是如何工作的，我推荐阅读[这篇详细的文章](https://codeburst.io/javascript-can-a-1-a-2-a-3-ever-evaluate-to-true-aca13ff4462d)。

如果你读过它，你会知道它的工作原理是因为松散的等式(例如，`==`被用于比较)，这会导致 JavaScript 强制比较其中一个值的类型。

下面的片段呢？你认为它能为`a`的任意值返回`true`吗？

*注意:我这里使用的是严格的类型检查(即* `*===*` *)。*

你一定认为这是完全不可能的，因为没有类型强制，它应该返回`false`。但是等等，这是 JavaScript。

要知道如何让上面的代码工作，我推荐阅读[这篇文章](https://theanubhav.com/2018/11/7/understanding-primitive-and-getter-setters/)。

# 2.查找闰年

要判断一年是否是闰年，需要满足两个条件:

*   年份应该是 400 的倍数。
*   年份是 4 的倍数，而不是 100 的倍数。

基于这些条件，你可以[用任何编程语言写一个简单的程序](https://www.geeksforgeeks.org/program-check-given-year-leap-year/)来找出给定的年份是否是闰年。

您甚至可以用 JavaScript 编写相同的逻辑来实现这一结果，但是 JavaScript 也有一种神奇的方式来实现这一点——实际上是一行程序:

魔法？

它之所以有效，是因为当你将非闰年如`2019`传递给`isLeapYear` 函数时，`getDate`函数会返回下个月的第二天，即 3 月 1 日，而不是 2 月 29 日。你可以说这个片段的工作是由于 JavaScript `Date`实现中的一种错误。

# 3.睡眠排序

有多种排序算法可以用来以任意顺序对数字数组进行排序，甚至可以使用 JavaScript 的内置`sort`函数来完成同样的操作。然而，我最近看到了下面的代码片段，它利用`setTimeout`对一组数字进行升序排序:

惊讶吗？

实际上，这个片段并不总是保证能够工作和伸缩。由于 JavaScript 的异步特性以及 JavaScript 中[事件循环的工作方式，这种方法可能行得通(也可能行不通)。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)

# 结论

这些片段只是为了好玩，所以请不要在您的生产应用程序中使用它们，甚至不要作为技术面试的问题。

如果你有任何这样神奇的 JavaScript 片段分享，那么请在下面评论。

感谢您的阅读！
# 我写第一个 npm 实用模块的那一天

> 原文：<https://betterprogramming.pub/the-day-i-wrote-my-first-npm-utility-module-74b8098df003>

## 一个关于编码的不简单的故事

![](img/50caf91faf864e4be66c688acadc7abc.png)

[保罗·吉尔摩](https://unsplash.com/photos/NZ-K01i0Hmo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

本文原载[法文](https://www.24joursdeweb.fr/2018/le-periple-vers-mon-premier-module-utilitaire-npm/)。由于我是一个很好的拖延者，我决定一年后把它翻译成英文。快乐阅读！

有时候，我会问自己:“这些人是如何为 JavaScript 社区发布这么好的实用程序的？”

是的，怎么做？答案很简单。他们没有在互联网上长长的 npm 模块列表或其他库中找到他们想要的东西。所以，他们决定把手伸进泥土里，自己编码。

# 从一个简单的方法开始

这个故事开始于我测量我的一个网页的性能。我试图列出代码中的瓶颈，以及如何修复导致我的页面出现灾难性性能的模式。

从页面上，我跳到了一个 React 组件。然后，从组件开始，我跳到了实现。而且从实现开始，我就开始写一点函数提取一些逻辑。

然后，一切都失控了…

我的问题是我想检查一个字符串值是否代表一个“数字”。

让我们深入了解一下:

*   字符串值:这意味着传递给函数的参数应该是一个`string`类型。
*   数字:在 JavaScript 中，`number`是一个原语，许多符号可以返回一个`number`——指数符号、十六进制基数，或者使用[一元(+)运算符](https://medium.com/@nikjohn/cast-to-number-in-javascript-using-the-unary-operator-f4ca67c792ce)。我选择关注用固定记数法(熟悉的记数法)和十进制数书写的数字。

可以返回“数字”的符号示例。

我在网上做了些调查。看看过去的`[jQuery.isNumeric](https://api.jquery.com/jQuery.isNumeric/)`。或者伟大的`[is-number](https://www.npmjs.com/package/is-number)`模块。或者其他一些关于堆栈溢出的问题。

最后，我看了看我能用全局顶级`[parseFloat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseFloat)`函数编码什么。

# 跳转到 npm 模块

我写了一个似乎适合我的用例的简短函数:

*   `parseFloat`函数帮助我摆脱了每一个不是`number`或`string`的参数。例如，对于一个数组或函数，它返回`NaN`。我的第一部《提前归来》处理了所有这些案件。
*   第二次“提前归还”处理了环球地产的案子`Infinity`。
*   最终相等检查传递的参数是否严格等于被转换为`number`的参数。这个检查处理了带有`string`输入的科学指数符号的情况:`'42'`将匹配，但是`'42e4'`不匹配。

# 模块的第一步

然后，我就在想这个助手函数放在哪里。我的组件中的某个地方—作为一个实例方法？这样我就可以在另一个组件中使用它了。

如果我如此渴望使用那种助手功能，其他一些前端开发人员也应该有。也可能不是。

我意识到，我自己可以在许多其他项目中使用这个功能。所以，我决定完全提取这个函数并[创建一个 npm 模块](https://docs.npmjs.com/creating-node-js-modules)。天真地，我认为模块的代码几乎准备好了，因为它服务于我的项目的目的。剧透警告:我错了。

我决定调用模块`is-plain-number`，因为它是一个*谓词函数* ，检查一个值是否是一个*“普通的”*、简单的数字，也就是用固定符号和十进制基数写成的“*”。*

所以，我发布了模块的[第一版:](https://www.npmjs.com/package/is-plain-number)

*   没有依赖性，因为模块只是一个函数。
*   一个测试套件，描述了我想要涵盖的所有用例。
*   一个可靠的自述文件，帮助其他开发人员快速有效地理解该模块的内容。

这三点对我来说非常重要。显然，它们对使用我的模块的人很有用。但是，更重要的是，他们帮助我理解我自己的代码，解释它，挑战它。

自述文件中“is-plain-number”模块的一些代码示例。

但是，重读模块后，我被它的名字和它的测试套件所困扰。当你考虑命名一个实用程序时，你必须考虑它的目的，尤其是它应该包含的所有用例。所以，你也要想好考什么。

这个功能的目的是什么？

那一刻，我彻底重新考虑了`[is-plain-number](https://www.npmjs.com/package/is-plain-number)`模块。

# 模块的新版本

正如我之前所说，我个人的用例是检查一个`string`值是否是一个用固定符号和十进制表示的数字。这就是我错过了语言中非常重要的一点:

“用固定记数法和十进制写”是什么意思？我对用 JavaScript 显示数字了解多少？

让我们回到我的函数:

该函数的最后一行是强制转换为`string`的`number`(从`parseFloat`返回)和传递给该函数的也强制转换为`string`的参数之间的严格等式。

通过这个实现，我的意图是抛弃科学符号:

耶！当参数类型是以科学指数表示法编写的字符串时，我的模块返回“false”。

但是，这只适用于`string`参数:

༼ つ ಥ_ಥ ༽つ:哦，不！当参数类型是用科学指数表示法写的数字时，我的模块返回“true”。

起初，我没有抓住我错过的有`number`论据的案例。在 JavaScript 中，`number`值以二进制浮点存储，但其输出主要是十进制和固定记数法。

语言的这一方面让我意识到，如果值是一个`number`，就没有办法知道原始输入是否是以科学指数记数法的十六进制为基础编写的。

这种对语言的误读指出了我的模块的不一致性:它返回了用不同于固定记数法或不同于十进制记数法编写的`number`的`true`。但是，对于参数为`string`的完全相同的情况，它返回`false`。

你知道吗？我不喜欢前后矛盾。

我回到了最初的需求，选择放弃带有参数为`number`的案例，并编写了一个新的模块: [is-string-a-number](https://www.npmjs.com/package/is-string-a-number) 。

我们可以看到“普通数字”模块和“字符串数字”模块之间的区别。

# 边缘案例

有了这些新知识，我没有想到的其他问题就出现了。

最后的`string`严格平等仍在实施`is-string-a-number`:

```
return String(float) === String(value)
```

但是，我发现，`number`的输出主要是固定记数法，但有时也可能是指数记数法。因此，对于我想要处理的所有情况，我的严格等式不返回`true`:

\_(ツ)_/是我的严格平等不起作用的新案例！

是的，我的模块不能处理大于 T5 的 T4 数字和以 T6 开头后面跟五个以上零的数字。

为了理解这个问题，我将让您阅读 Axel Rauschmayer 的非常好的文章中用于在 JavaScript 中显示数字的算法。与此同时，我还在考虑如何处理这个问题。

此外，我对大数字也有异议。ECMAScript 标准只有一种类型的数字: [64 位浮点数](http://2ality.com/2012/04/number-encoding.html)。大整数(现在从 TC39 提案中称为 [*BigInt* )不能全部用这种类型表示。](https://github.com/tc39/proposal-bigint)

目前，我选择不处理它:模块不能处理大整数，仅此而已。

# 你对你的模块还满意吗？

在这个漫长而不完整的旅程之后，你可能会怀疑我是否还能适应我编写的模块。我可以非常自信地回答:

是的。

是的，因为，我告诉你的这几个步骤只是开始。我可以迭代边缘案例。最重要的是，我学到了很多，很多，很多。

# 撰写好的自述文件

这一点我怎么说都不为过:好的读物是至关重要的。他们帮助用户快速理解你的模块的范围，以及它到底能不能满足他们的需求。

我花了很多时间写`is-string-a-number`的自述，尤其是动机部分。这让我明白了为什么我认为我的模块是有用的。

这完全是主观的，也许我错了。但是，至少，我解释了上下文，如果其他开发人员不同意，他们可以用这些信息来帮助或挑战我。

# 编写一整套测试

当然，用我小小的函数，我写了一组测试。这些测试几乎检查了我可以传递给函数的每一种可能的值。

有趣的是，我第一次写它们的时候——在编写我的模块的第一个版本的时候，我已经感觉到有些东西不一致了。

测试是检查`'42e24'`返回`false`而`42e24`返回`true`。向代码气味问好…

如果测试的不是正确的事情，不是你真正想做的事情，那么测试就不是好的。这就是我如何结束重写模块。

# 挑战你自己的代码

> "当有疑问时，就没有疑问了。"索莱娜·马特

因为我觉得我的模块的目的对我来说不够清楚，而且我错过了关于语言的一些非常重要的东西，我停止了编码，开始阅读大量关于 JavaScript 中的数字。

感谢 Axel Rauschmayer 的博客，我了解了很多关于数字的知识:它们是如何显示的，它们是如何编码的，甚至是零的含义。

这些知识帮助我定义了我的模块的边界:

*   它能处理什么。
*   为什么会这样，为什么不会。
*   我能改进的地方。

# 出版

我认为在编码和共享一个库之前做一些研究是很重要的，即使它很小。我们不需要重新发明轮子。有些人在我们之前做得很好。

谦逊的态度对于观察案例和其他人已经解决的问题很重要。

但是，这不应该阻止我们尝试和创造新事物。如果一段代码对我们有用，即使我们是它的唯一用户，它也是值得的。

# 结论

用开源库为 JavaScript 社区做贡献对我来说是一次很棒的经历。如果你有一个以前没有探索过的想法，或者不是以你看到的方式的想法，征求意见，三思而后行，然后尝试发布你自己的模块！

最后，也许像我一样，你会发现你的模块中有很多漏洞，但这正是开源领域如此丰富的原因:它建立在经验和合作的基础上。

*感谢 Sunny Ripert 的再次阅读，感谢 Florent DUVEAU 和 24 jours de web 团队的支持！*
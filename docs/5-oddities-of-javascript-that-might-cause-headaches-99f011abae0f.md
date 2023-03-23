# JavaScript 的 5 个奇怪之处可能会让人头疼

> 原文：<https://betterprogramming.pub/5-oddities-of-javascript-that-might-cause-headaches-99f011abae0f>

## 欢迎来到 JavaScript 的疯狂世界

![](img/e10a1f9648d86f9d963564fa1e34719a.png)

照片由[乌代·米塔尔](https://unsplash.com/@mittaluday?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果您熟悉 JavaScript，那么在使用它时，您很可能会遇到一些意想不到的奇怪行为。其中一些可能是开发人员之间的一个很好的笑话，但是了解它们可以为您节省大量时间和调试代码的麻烦。

在这篇文章中，我想向你介绍其中的一些怪癖。

# 1.平等和相同

在 JavaScript 如何定义相等性和相同性方面存在微妙而重要的差异。对于初学者来说，最令人困惑的可能是抽象的`==`和严格的`===`相等检查之间的区别。

让我们先来看几个例子:

与`null`相比如何？

如果你将`null`转换成布尔值，它就是`false`。
然而，如果你试图比较`null`和`false`，结果仍然是`false`！

如果我们比较其他的伪值，比如`0`或`""`和`false`，会怎么样呢？
你可能会认为那也是`false`。不过这只适用于严格的等式检查，抽象将导致`true`！

在上面的例子中，我们看到抽象的相等检查在某些情况下将两个不同类型的变量分类为相等。然而，如果您将一个空数组(真值)与`true`进行比较，您可能会再次感到惊讶:

了解 JavaScript 中抽象等式比较和严格等式比较的区别很重要。我强烈推荐阅读 MDN 的这篇文章，其中他们详细解释了平等和相同。

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness) [## 平等比较和相同

### ES2015 中有四种等式算法:抽象等式比较(==)严格等式比较(===):已用…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness) 

# 2.数学疯狂

我们继续我们的数学之旅！JavaScript 在这里为我们准备了一些美丽的怪癖:

为什么第一个例子是正确的，而第二个却不是？这样做的原因是，第一个只在这种特定情况下有效。如果您看一下 JavaScript 对这些表达式求值的顺序，就会明白为什么会得到这些结果:

JavaScript 中数学的另一个陷阱是这些“神奇地”增加的数字:

由于 IEEE 754–2008 标准的[，JavaScript 在这个尺度上舍入到下一个最接近的偶数。这对于许多其他编程语言都是一样的，不仅仅是 JavaScript。该标准还对以下问题负责:](https://en.wikipedia.org/wiki/IEEE_754)

这个问题在 StackOverflow 上的[这个回答](https://stackoverflow.com/a/588014)中有很多详细的解释。

理解 JavaScript 如何决定加法和串联也很重要。一般来说，你可以说每次一个字符串被包含在加法中，它在操作中的某一点变成一个连接。

然而，在一些令人惊讶的情况下，加法运算会变成完全不同的类型，如下例所示:

等等，我把最令人困惑的例子留到了最后。让我们比较函数`Math.min()`和`Math.max()`的结果

这绝对不是我第一次使用这个功能时所期望的。查理·哈维在他的博客上解释了这种行为[，所以如果你对细节感兴趣，可以去那里看看。](https://charlieharvey.org.uk/page/why_math_max_is_less_than_math_min#)

# 3.数组的乐趣

在前面的章节中，我们已经看到了一些关于数组的例子，但是关于数组还有更多有趣的事实。

例如，如果您尝试将两个数组相加，会得到以下结果:

乍一看，这似乎令人困惑，但当您理解这种连接的执行顺序时，这是有意义的。

JavaScript 如何处理尾随逗号也很有趣。
在数组中，尾随逗号将被忽略，如下例所示:

但是如果你有多个尾随逗号呢？这就是 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Trailing_commas#trailing_commas_in_literals) 在这个例子中描述 JavaScript 行为的方式:

> 如果使用了一个以上的结尾逗号，就会产生一个省略号(或孔)。一个有孔的数组叫做*稀疏*(一个*密集*数组没有孔)。当用`[Array.prototype.forEach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)`或`[Array.prototype.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)`迭代数组时，数组空洞会被跳过。

您可以看到前两个逗号生成了前面描述的孔。但是，最后一个逗号将被忽略，因为它与普通数组项一样。这就是我们最终得到的最终长度`5`。

# 4.巧妙的箭头功能

在 ES6 中引入的 arrow 函数很快变得流行起来，因为它们的最小语法和更直观的`this`对象行为。

几乎所有使用箭头函数的人都遇到过的一个常见陷阱是，当您在函数中返回值时:

重要的是不要错过这里的括号；否则，你的函数将返回`undefined`，因为你打开了函数却没有返回任何值。

要了解 JavaScript 中箭头函数的更多信息，可以看看 MDN 文档。

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) [## 箭头函数表达式

### arrow 函数表达式是传统函数表达式的一种紧凑替代形式，但它是有限的，不能…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) 

# 5.弦不弦？

是的，你没看错。看一下下面的例子:

你可以看到`typeof`像预期的那样工作，但是`instanceof`为一个`String`返回`false`，尽管我们知道`"str"`确实是一个字符串。

但是，如果您使用`String()`构造函数，您将得到预期的结果:

这是什么原因呢？ [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof) 这样描述`instanceof`:

> `**instanceof**` **操作符**测试一个构造函数的`prototype`属性是否出现在一个对象的原型链中。返回值是一个布尔值。

原因是字符串原语与字符串对象并不完全相同。如果你仍然想要类型检查原语，你应该使用`typeof`而不是`instanceof`。

# 6.额外收获:JavaScript 中的 HTML！

你知道你可以用 JavaScript 写一点 HTML 吗？正如您在下面的例子中看到的，JavaScript 中的 HTML 注释完全有效。

这是很久以前引入的，所以旧的浏览器——还不理解`<script>`标签——不会崩溃。这些浏览器早已寿终正寝，但其功能依然存在。甚至在 NodeJS！

这只是 JavaScript 中许多怪癖的一小部分。如果你想深入了解，我可以推荐这个由 **denysdovhan** 开发的名为 [wtfjs](https://github.com/denysdovhan/wtfjs) 的令人敬畏的 GitHub 库。
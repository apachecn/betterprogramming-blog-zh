# JavaScript 的 6 个小秘密帮助你在求职面试中脱颖而出

> 原文：<https://betterprogramming.pub/6-little-secrets-of-javascript-to-help-you-shine-at-job-interviews-c32bc5e93c8f>

## JavaScript 中的古怪和秘密可能很少有人知道

![](img/b0b2c4391ed36e54fe25e4232405979d.png)

[卡洛斯](https://unsplash.com/@folkcarlos?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

JavaScript 不容易掌握。

通常它会做一些完全不同于你预期的事情。是一个关键词…“我不这么认为，先生！”。这只是当你被问到 JavaScript 时，你可以收集和展示的知识的预览。真正的专家知道细节。

许多小事情值得了解，以克服意外错误，避免长达一小时的调试。

# 1.未定义的可以定义

> 弃用信息 09/2022:在这篇文章出现很长时间的时候，JavaScript devs 将 undefined 变成了一个不可写的标识符。如果你试图创建一个名为“undefined”的变量，你会得到“未捕获的语法错误:标识符“undefined”已经被声明。”

奇怪的是，`undefined`并不是 JavaScript 中的保留字。尽管它有特殊的含义，并且是确定变量是否未定义的唯一方法:

但是如果你把 undefined 作为变量名并给它赋值，你就不能检查变量的状态为

# 2.Replace()接受回调函数

这可能是 JavaScript 迄今为止保守得最好的秘密之一。`replace()`的大多数用法如下:

代码向您展示了一个简单的替换:一个字符串，一美元。

但是，如果你想更好地控制替换的方式和时间呢？

*如果您只想替换 30 以上的数字，该怎么办？*

仅靠正则表达式是无法实现这一点的(毕竟，它们都是关于字符串的，而不是数学)。相反，您需要**跳转到一个回调函数来评估每个数字。**

[如果你不熟悉正则表达式或者想深入研究，让印第安纳琼斯教你来龙去脉&。](https://levelup.gitconnected.com/indiana-jones-and-the-universal-way-to-search-for-text-1901990f53ae?source=your_stories_page----------------------------------------)

JavaScript 为每个匹配调用您的函数，并将数字传递给 number 参数。然后，我们返回美元符号(如果数字大于 30)或数字本身。这意味着条件失败(数字低于 30)。

# 3.创建动态正则表达式模式

大多数 JavaScript 开发人员只能通过正则表达式使用`match`和`replace`。

你知道 JavaScript 定义了比这两个更多的方法吗？

很有意思的是`test()`，工作原理类似于`match`。关键的区别是它不返回匹配:它只是确认你的正则表达式是否匹配，就像 tinder 推送通知一样。

从这个意义上说，它的计算量更小。

三个或更多的字符需要设置一个真正的控制台。使用的字符串符合此标准。我们没有得到实际的匹配，只有结果。

同样值得注意的是`RegExp`对象。你可以创建动态正则表达式，而不是静态的。大多数正则表达式都是用缩写形式`/regexp/`声明的，用正斜杠括起来。

这样，你就不允许使用变量了。所以制作动态图案是不可能的。

使用`RegExp()`，您可以，因为`RegExp`被指定为字符串，而不是通过正斜杠语法:

您只是基于参数`word`的值创建了一个动态模式。

该函数返回`word`在一个字符串中出现的次数，作为一个包含单词本身的数组，只要它不是其他单词的一部分。

由于有了`\b`记号，你也可以在一个有`cart`、`Carl`和`carroussel`的句子中搜索`car`。它通过检查我们正在寻找的单词两边的单词边界(`b`)来强制这样做。

# 4.Nan 是一个数字

JavaScript 中正在进行一个巨大的阴谋:NaN 是一个数字！

处理`NaN`——“不是一个数”——和`NaN`的想法，不被认为等于它本身。搞什么，JavaScript？！

精准`NaN`不等于什么。确认某物是`NaN`的唯一方法是使用函数`isNaN()`。

# 5.Null 是一个对象

坚持阴谋是一个目标。就矛盾而言，它们是最好的矛盾。

"空的定义是完全没有有意义的价值."

你是对的。但事实就是如此。这就是证据:

`null`不被认为是`object`的实例。

> 以防你不知道:JavaScript 中的值是基本对象的实例。因此，每个数字都是 number 对象的一个实例，每个对象都是 object 对象的一个实例，以此类推。

知道这一点可以把你带回现实，因为如果 null 是一个值的缺失，那么它显然不可能是任何东西的实例。上帝保佑，以下评价为`false`:

# 6.数组中没有键等于假

为了理解这里发生的事情，你需要知道真与假的概念。这些都是真/假精简版，如果你对逻辑非常了解，这可能会让你有点生气。

我想你最容易理解的方式是:在 JavaScript 中，每个非布尔值都有一个内置的布尔标志，当要求该值表现得像布尔值时，就会调用这个标志。例如，当您想将它与布尔值进行比较时。

显然，你不能把苹果和梨相提并论。每当 JavaScript 被要求比较不同数据类型的值时，它首先将它们强制转换成一种通用的数据类型。

`False`、`zero`、`null`、`undefined`、空`strings`和`NaN`最后变成`false`——不是永久的，只是为了给定的表达。看一下下面的例子:

您正试图将数字`0`与布尔值`false`进行比较。

因为这些数据类型是不兼容的，JavaScript 秘密地将你的变量转换成它的 truthy 或 falsy 等价物。`0`是福尔西。

你可能已经注意到空数组不在上面的列表中。他们是珍贵的小动物，他们评估 truthy。但是，当你将它们与布尔型相比较时，它们的行为就像一个伪布尔型。

*这太疯狂了！*下面是一个例子:

为了避免强制，可以使用值和类型比较运算符`===`。与`==`相对，只按值比较。仔细看看这个秘密的介绍例子，我用`==`操作符做了比较。

[型强制](https://developer.mozilla.org/en-US/docs/Glossary/Type_coercion)是一个宽泛的话题。我建议在这方面多读点书。虽然不是一个独特的 JavaScript 概念，但它在 JavaScript 中仍然很突出。

# 外卖食品

*   不要使用 undefined 作为变量名
*   `Replace()`可以带回调功能
*   您可以在 JavaScript 中动态创建 RegExp 模式
*   讽刺的是，不是一个数字
*   虽然`null`是值类型，但它是一个`object`
*   用三个等号(`===`)比较空数组。不要与三重空气标志混淆

[*获取 26 份备忘单，只研究你真正需要的东西，以获得你的第一份网络开发工作！*](https://arnoldcodeacademy.ck.page/26-web-dev-cheat-sheets)

![](img/227a060a3bfa55f41fa795d5990e6032.png)

[Arnold Code Academy 26 网站开发者备忘单](https://arnoldcodeacademy.ck.page/26-web-dev-cheat-sheets)
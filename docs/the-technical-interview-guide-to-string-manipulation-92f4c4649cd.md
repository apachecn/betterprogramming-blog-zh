# 字符串操作的技术面试指南

> 原文：<https://betterprogramming.pub/the-technical-interview-guide-to-string-manipulation-92f4c4649cd>

## 用于面试准备和日常编码的 JavaScript 字符串操作

![](img/5c02897428005f4b51dff0ca4a392918.png)

图片来源:作者

字符串是一系列字符。它要么是常数，要么是变量。它是编程语言中的一种基本数据类型。在本文中，我们主要关注 JavaScript 字符串操作。然而，该原理和算法也可以应用于其他语言。

当你面对一次技术性面试时，面试官在寻找一些东西:

*   你的编程技术有多好？
*   你的语言技能有多新？
*   你解决问题的能力有多强？

这个面试系列为你准备了一个成功的技术面试，但它对日常编码也很有用。

在 code gist 格式中，我们列出了 JavaScript 字符串的重要特性，这是编程技能的基础。有已经存在 20 年的属性和方法，也有 ES2021 中的特性。你可能已经知道他们所有人。如果有什么不清楚的地方，你知道你需要在哪里多学习一些。

有了 JavaScript 编程语言，我们可以解决许多文字问题。这些算法，或者它们的变体，经常在真实的面试中被测试。

# 字符串属性和方法

一个[字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)用于表示和操作一个字符序列。有许多字符串属性和方法。以下是供回顾的代码样本，包括 ES2020 中的`matchAll`、ES2021 中的`replaceAll`和 ES2022 中的`at`。

# 地图和布景

对于字符串操作，我们需要在某个地方存储中间值。`Array`、`Map`和`Set`是你需要掌握的流行数据结构。我们将在另一篇文章的[中讨论`Array`。这里复习一下`Set`和`Map`。](https://medium.com/better-programming/the-technical-interview-guide-to-dynamic-programming-3ce755d99849)

## 一组

`[Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)`是一个存储任何类型的唯一值的对象。以下是供审查的代码示例。应该是不言自明的。

## 地图

`[Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)`是保存键值对的对象。任何值都可以用作键或值。A `Map`会记住按键的原始插入顺序。以下是供审查的代码示例。它也应该是不言自明的。

# 文字问题

一次面试有很多英语单词问题。我们探索一些经常被测试的算法。

## 等值线

等值图是字母表中没有一个字母出现超过一次的单词。

以下是长等值线图:

*   皮纹学(15 个字母)
*   液压气动(15 个字母)
*   错误组合(15 个字母)
*   不可复制(15 个字母)
*   不可复制的(16 个字母)
*   皮下注射(17 个字母)

怎么写一个算法来检测一个字符串是不是等值线图？有许多方法可以实现它。我们可以把字符串放在一个`Set`中，然后它会自动拆分成字符。由于`Set`是一个存储唯一值的对象，如果它是一个等值线，那么它的大小应该与字符串长度相同。

这些是验证测试:

## 潘格拉姆

A *pangram* 是一个包含字母表中所有 26 个字母的句子，不区分大小写。理想情况下，句子越短越好。

以下短句是 pangrams:

*   华尔兹，坏仙女，快速夹具烦恼。(28 个字母)
*   Jived 狐狸仙女抓住快速华尔兹。(28 个字母)
*   油嘴滑舌的运动员测试仙女激怒侏儒。(28 个字母)
*   黑色石英的狮身人面像，审判我的誓言。(29 个字母)
*   愚蠢的斑马跳得多快啊！(30 个字母)
*   五个拳击奇才跳得很快。(31 个字母)
*   寒鸦喜欢我的石英大狮身人面像。(31 个字母)
*   在我的箱子里装上五打酒壶。(32 个字母)
*   那只敏捷的棕色狐狸跳过了一只懒狗。(33 个字母)

还有许多方法可以验证给定的字符串是否是一个 pangram。这一次，我们将每个字母(转换成小写)放入一个`Map`。如果`Map`的尺寸是 26，那么它就是一个 pangram。

这些是验证测试:

## 同构字符串

给定两个字符串`s`和`t`，如果`s`中的字符可以替换得到`t`，则它们是*同构的*。`s`中的任何角色转换都必须应用于`s`中所有相同的角色。比如`murmur`与`tartar`同构，如果`m`被`t`替代，`u`被`a`替代，`r`被自身替代。

下面的算法使用一个数组来存储转换字符。它也适用于`Map`。

这些是验证测试:

## 变位词

*变位词*是通过重新排列一个不同单词的字母而形成的单词，通常使用所有原始字母一次。

从一个池中重新排列单词可能有许多可能性。比如*猫*有字谜*猫*、*法*、 *atc* 、 *tca* 、 *atc* 、 *tac* 。我们可以添加一个额外的要求，即新单词必须出现在源字符串中。如果源是`actually` *，*得到的数组是`[“act”]`。

这些是验证测试:

## 回文

一个*回文*是一个单词或句子，前后读起来一样。回文很多，比如*一个*，*鲍勃*，*一个人，一个计划，一条运河——巴拿马。*

所有检查回文的算法分为两种。一种使用循环从两端检查它们是否相同，另一种使用递归从两端检查它们是否相同。下面的代码使用递归方法。

这些是验证测试:

有许多回文面试问题，有些变化。下面是一个在给定字符串中寻找最长回文的算法。

这些是验证测试:

# 结论

还有其他的文字问题。熟能生巧。享受编码吧！

感谢阅读。我希望这有所帮助。你可以在这里看到我的其他媒体出版物。
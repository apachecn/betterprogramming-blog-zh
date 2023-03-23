# 应用于 JavaScript 的干净代码(第 4 部分)

> 原文：<https://betterprogramming.pub/clean-code-applied-to-javascript-part-4-3f6db21ee4e5>

## 关于编写和维护干净代码的惊人好处的系列文章。第四部分:评论。

![](img/b9a1822b721cd36857cacba29610ede9.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com/s/photos/team?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

本文是深入探讨应用于 JavaScript 的干净代码主题的系列文章的第四篇。

在本系列中，我们将讨论每个程序员都应该知道的关于干净代码的经典技巧，并将它们应用于特定的 JavaScript/TypeScript 语言。

*   [第 1 部分:开始之前](https://medium.com/better-programming/clean-code-applied-to-javascript-part-1-9f3badd5715)
*   [第二部分:变量](https://medium.com/better-programming/clean-code-applied-to-javascript-part-ii-variables-8302f01e539c)
*   [第 3 部分:功能](https://medium.com/@ccaballero/clean-code-applied-to-javascript-part-3-functions-f9f26b221736)
*   [第 4 部分:注释](https://medium.com/better-programming/clean-code-applied-to-javascript-part-4-3f6db21ee4e5)
*   [第 5 部分:例外情况](https://medium.com/better-programming/clean-code-applied-to-javascript-part-5-exceptions-d46e07691c19)
*   [第 6 部分:避免条件复杂性](https://medium.com/better-programming/clean-code-applied-to-javascript-part-6-avoid-conditional-complexity-5ee9cbb1b26a)

# 介绍

在本帖中，我们将讨论一个引起开发者争论的话题。许多开发人员认为注释是好的实践，而其他人认为相反——应用注释是不好的实践。

很抱歉地告诉你，没有绝对的规则——一切都要看情况。事实是，在很多情况下，注释*并没有* 对软件开发有所贡献，因为它们已经被其他比应用注释更好的工具所取代。在其他情况下，注释可能会对我们正在开发的源代码或者我们将来将要阅读的源代码造成干扰。在这种情况下，最理想的是没有评论。

另一方面，在有些情况下，注释是很好的实践，例如在公共 API 的文档中，您可以了解一个库的行为，但不能了解它是如何开发的。

在这篇文章中，我将描述注释产生噪音的几种情况，这时注释不应该应用在你的代码中。

# 只评论有业务逻辑复杂性的东西

注释的存在只是为了帮助程序员解释对他们来说很难理解的业务逻辑。评论从来没有描述过算法。我们必须认为好的代码通常是自文档化的，并且源代码在阅读时是可以理解的。注释是额外的，不是必需的。

然而，有时算法是为特定的业务逻辑设计的，而作为开发人员，我们并不知道这一点。比如用信用卡操作，用自己的保险操作等等。

下面的例子显示了其中大部分的冗长和不必要的注释。然而，最后一个注释并非无关紧要，因为操作是在我们正在建模的问题的领域中执行的。这个评论的存在不会有什么坏处。这个注释不是描述在编程层次上正在做什么，而是在业务逻辑层次上正在做什么。

```
function convert(data){
 // The result
 let result = 0;

 // length of string
 const length = data.length;

 // Loop through every character in data
 for (let i = 0; i < lenght; i++){
 // Get character code.
 const char = data.charCodeAt(i);
 // Make the hash
 result = (result << 5) — result + char;
 // Conver to 32-bit integer
 result &= result;
 }
}
```

等效的代码如下所示。在这段代码中，你可以看到注释并没有增加价值，而是在我们的代码中产生了噪音。

```
 function convert(data) {
 let result = 0;
 const length = data.length;

 for (let i = 0; i < length; i++){
 const char = data.charCodeAt(i);
 result = (result << 5) — result + char;
 result &= result; // Convert to 32-bit integer
 }
}
```

# 没有日志评论

注释被用来(我希望这种情况不再发生)记录文件随时间发生的变化。由于缺乏版本控制工具，这在过去可能是有意义的。

今天，这个任务应该委托给版本控制软件(我推荐使用 GIT)。因此，不需要死代码、注释代码，尤其是日志注释。

要获得这些信息，您只需使用 *git log* 来获取历史记录命令。

下面是一段带有日志注释的代码，与之相对的是更简洁的版本。

```
/**
 * 2018–12–20: Removed monads, didn’t understand them (CC)
 * 2018–10–01: Improved using special mondas (JS)
 * 2018–02–03: Removed type-checking (LI)
 * 2017–03–14: Added add with type-checking (CC)
 */
 function add(a, b) {
 return a + b;
 }function add(a, b) {
 return a + b;
}
```

# 避免位置标记

您应该避免位置标记，因为它们通常只会增加干扰。
让函数和变量名，以及适当的标识和格式，给你的代码提供可视化的结构。

下面的代码显示了一个带有位置标记及其干净版本的示例。您应该认为这些使用注释的技术是一种时代错误，因为在早期，开发人员的工具很少。现在，没有必要在源代码中创建这些标记，因为它们只是噪声。

```
///////////////////////////////
// Controller Model Instantiation
///////////////////////////////
controller.model = {
 name: ‘Felipe’,
 age: 34
};///////////////////////////////
// Action Setup
///////////////////////////////
const actions = function() {
 // …
};controller.model = {
 name: ‘Felipe’,
 age: 34
};const actions = function() {
 // …
};
```

# 结论

评论是当今开发人员中争论最激烈的话题之一。许多人认为它们是必要的，而许多人认为它们不是。在生活中，任何决策都不能走极端，软件开发也不例外。

因此，在这篇文章中，我试图总结三种通过包含注释而使代码变脏的做法。然而，如果我们正在创建一个公共 API，写注释可能会很有趣，因为我们正在记录 API。

许多教师采用的一个不好的做法是注释生产代码中的每一行。这种不好的习惯是从初级程序员被教导编码代码时继承下来的，每一行都被注释为学习指南。

然而，在产品开发中，拥有学习指南和评论每一行代码之间的差别是巨大的。

最后，我们讨论的要点如下:

*   只评论有业务逻辑复杂性的东西
*   没有日志评论
*   避免位置标记
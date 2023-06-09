# 为什么编写自己的代码会让你成为更好的开发人员

> 原文：<https://betterprogramming.pub/why-coding-your-own-makes-you-a-better-developer-5c53439c5e4a>

## 为了真正理解轮子，你需要重新发明它

![](img/acfd214aa12d02c2f39677dd0567aabe.png)

乔恩·卡塔赫纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

前几天，我面试了一个 JavaScript 开发人员的高级开发人员职位。我的同事也参加了面试，他要求被面试者编写一个函数，该函数将执行一个 HTTP 调用，如果失败，就重试多次。

因为他是在白板上写的，所以伪代码就足够了。如果他表现出对这件事有很好的理解，我们会很高兴的。但不幸的是，他没能想出一个好的解决办法。

考虑到他可能只是紧张，我们决定让它变得简单一点，并要求他将基于回调的函数转换为基于承诺的函数。

运气不好。

是的，我可以说他以前见过类似的代码。他或多或少知道它是如何工作的。一个伪代码的解决方案，证明他理解这个概念就可以了。

但是他写在白板上的代码完全没有意义。他对 JavaScript 承诺的概念只有模糊的理解，不能很好地解释它。

如果你是一个初级开发人员，你可能会侥幸过关，但如果你申请的是一个高级职位，这是不够的。他会如何调试一个复杂的承诺链，然后向别人解释他做了什么？

# 开发人员认为抽象是理所当然的

作为开发人员，我们从事抽象工作。我们抽象代码，否则我们将不得不重复。因此，当我们关注更重要的部分时，我们把我们处理的抽象视为理所当然，只是*假设*它们有效。

通常是这样，但是当事情变得复杂时，真正了解这些抽象是如何工作的*是值得的。*

高级开发人员职位的候选人认为承诺抽象是理所当然的。如果他在某处的一段代码中发现了它，他可能知道如何使用它，但他并没有真正理解这个概念，所以他无法在求职面试中重现它。

他可能只是记住了密码。其实没那么复杂:

```
return new Promise((resolve, reject) => {
  functionWithCallback((err, result) => {
   return err ? reject(err) : resolve(result);
  });
});
```

我做到了。很可能，我们都这样做。你只要记住一段代码，就可以使用它。你或多或少明白它是如何工作的。

但是如果他真的理解了这个概念，他就不需要记住它。他会知道它，并且毫无困难地复制它。

# 了解你的消息来源

回到 2012 年，在前端框架占主导地位之前，jQuery 统治了世界，我正在阅读 jQuery 的创始人 John Resig 的“[JavaScript Ninja 的秘密](https://www.manning.com/books/secrets-of-the-javascript-ninja)”。

这本书教你如何从头开始创建自己的 jQuery，让你对这个库的创建背后的思想过程有一个独特的见解。虽然 jQuery 在过去的几年里已经淡出了人们的视线，但我还是强烈推荐阅读这本书。

这本书给我印象最深的是，我一直觉得我自己也能想到这一点。所描述的步骤是如此的符合逻辑和简单明了，以至于我真的有一种感觉, *I* 如果我自己去做的话，我本可以构建 jQuery 的。

当然，在现实中，我永远也做不到——我会认为这太复杂了。我会认为我的解决方案太简单、太天真而行不通，我会放弃。我只是认为 jQuery 是理所当然的，并且相信它能够工作。在那之后，我可能不会花时间去弄清楚它是如何工作的。我会把它当成一个黑匣子。

但是读这本书改变了我。我开始阅读源代码，发现许多解决方案的实现非常简单，甚至显而易见。

现在，自己想出这些解决方案当然是完全不同的事情。但是自己阅读源代码并重新实现现有的解决方案正是*帮助你提出自己的解决方案的*。

作为开发人员，您获得的灵感和发现的模式将会改变您。你会发现，这个你使用并认为是魔法的伟大库，其实并不神奇，只是一个简单而聪明的解决方案。

你可能需要花时间一步一步地理解代码，但这也会让你经历作者创建代码时所经历的那些小的、渐进的步骤。这让您对编码过程有了更多的了解，并对自己的解决方案有了更多的信心。

当我开始使用 JavaScript 承诺时，我认为它们很神奇。后来我知道它们只是基于回调，我对编程的看法永远改变了。

这种旨在消除回调的模式是使用……*回调实现的？*

这改变了我。这让我意识到，这些并不是复杂到我无法理解的难以置信的代码。如果我有好奇心和意愿去钻研的话，这些模式我很容易理解。

这才是你真正学习编程的方法。这就是你成为更好的开发者的方法。

# 重新发明轮子

所以继续努力，重新发明轮子。编码[自己的数据绑定](https://medium.com/swlh/https-medium-com-drmoerkerke-data-binding-for-web-components-in-just-a-few-lines-of-code-33f0a46943b3?source=friends_link&sk=09dd590e07b3300bae4b63dbb716cc39)，编码[自己的承诺](https://hackernoon.com/implementing-javascript-promise-in-70-lines-of-code-b3592565af0f)甚至[自己的状态管理解决方案](https://css-tricks.com/build-a-state-management-system-with-vanilla-javascript/)。

没人用也没关系。你会学到的。如果你能在你自己的项目中使用它，那就已经很棒了。你会进一步发展它，学到更多。

重点不是在生产中使用你的解决方案，而是学习。为现有解决方案编写您自己的实现代码是从最佳方案中学习的好方法。

这是你成为更好的开发人员的方法。
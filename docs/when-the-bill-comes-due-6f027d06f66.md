# 当账单到期时

> 原文：<https://betterprogramming.pub/when-the-bill-comes-due-6f027d06f66>

## 技术债务的必要性和危险性

![](img/761d91d38aa5b8473f50f22e9da44174.png)

图片由来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=2789112) 的[Steve buiss NNE](https://pixabay.com/users/stevepb-282134/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=2789112)拍摄

> “快速移动，打破东西。”—马克·扎克伯格

公平地说，就像引用的那样，这个非常好。它简洁、有力、激励人心。它还迫切需要类似这样的第二句话:“一旦成功了，就回去正确地做！”

技术债并不是从扎克伯格大胆的使命宣言开始的。他只是用我们大多数程序员自古以来一直在做的事情做了一个座右铭。我们制造东西，但更重要的是，我们经常不得不在制造的同时发明制造的方法。

那是一个混乱的过程。

通常，我们为之构建解决方案的问题是如此之大或复杂，以至于我们编写代码只是为了看看我们所设想的是否可能。这个概念验证阶段是我最喜欢的编程部分之一。一个想法的原型可能会非常令人满意，尽管很少经过良好的规划，这也是我们大部分技术债务的来源。

# 从原型到 MVP

开发周期很快。出于许多原因，围绕一个可行的概念验证的兴奋会很快变成一个最小可行产品的冲刺。无论你是想在市场上击败竞争对手，还是认为自己正在做下一件大事，开发者很少会在这一点上放慢脚步。

这是有道理的。发展的世界变化无常，转瞬即逝，速度确实很重要。正是在这个过程的这个阶段，技术债务开始真正积累起来。快速修复的代码解决方案旨在使一个特性启动并运行，或者寻找外部数据的可行集成，一旦扩展，很少能保持良好。

# 失信

几乎没有人像程序员那样经常或有效地对自己撒谎。虽然积累了技术债务，但我们本质上知道我们在走捷径，但我们向自己承诺，当我们继续前进到下一个功能时，我们会回来解决这个问题。

这就产生了指数级的连锁反应。当您的项目真正成形时，总会有另一个(或多个)特性出现。这本身并不是一件坏事；事实上，这通常是一个迹象，表明你正在做的东西有价值，但是当你把每一个几乎不起作用的功能留在身后开始下一个功能时，你的技术债务会越来越高。

# 技术债务收取利息

诚然，这有点老生常谈，但也是事实。你越长时间忽视这一堆科技债务，它就会变得越大，但也越难解决。毫无疑问，新的特性和功能已经改变了您的应用程序的前景，并且弄清楚如何将所有东西重新连接在一起变得很困难。

劣质的变量命名实践、不安全的数据访问方法、漏洞百出的第三方库等等。所有人都开始从一个小山丘变成一座大山。在这一点上，应用程序状态出现错误或 UI 问题并不少见。

# 一个不应该被忽视的必要的邪恶

实际上，技术债务是不可避免的。这是一种必要的邪恶力量，它让我们能够迅速更好地理解大局。平衡何时以及如何解决技术债务是一个困难的决定，会随着项目的不同而变化。

但是你必须解决它。

当你的项目进入发布阶段时，被忽视的技术债务会造成严重的破坏。在多个并发用户或意料之外的用户工作流的压力下，裂缝将会显现。在最好的情况下，这可能像 UI 异常一样轻微，但是未解决的技术债务也可能暴露用户数据。如果您的应用程序跟踪敏感的个人用户信息，这可能是灾难性的。

# 为你计划不足的事实做好准备

那么什么是正确的方法呢？明确地理解你可能没有像你希望的那样充分地计划好你的项目是一个好的开始。总之，不要骗自己。

*   在任何功能正常工作后，花点时间记下需要解决的问题。缺点在哪里？什么不能很好地扩展？脆弱点在哪里？甚至像运行日志这样简单的事情也会让你对实际纠正这些问题负责。
*   求代码审查。其他开发人员可以指出您可能没有意识到的脆弱点。他们的经验可以引导你发现你在前进的道路上可能会遇到的意想不到的问题。
*   安排重构时间。在你的工作流程中留出时间来回顾、修改和优化。你总是有新的事情要做，所以如果你不安排时间来解决技术债务，你很可能会忽略它。

最后，不要绝望。如果你正在积累技术债务，这可能意味着你正在构建一些很酷很有用的东西，并且有成长的空间。这是一项成就；这就是为什么我们做我们所做的！

技术债务会引发真正的问题，但它不是一只 800 磅的大猩猩。这是你的准则。你写的。去修复它，确保它在任何情况下都能按照你需要的方式运行。那么就去构建下一个伟大特性吧！
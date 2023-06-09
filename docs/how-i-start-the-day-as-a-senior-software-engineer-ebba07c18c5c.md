# 作为一名高级软件工程师，我如何开始新的一天

> 原文：<https://betterprogramming.pub/how-i-start-the-day-as-a-senior-software-engineer-ebba07c18c5c>

## 从长期 JavaScript 开发人员的角度来看

![](img/3f0d7aa9addc48cbf2edc329c61375f2.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[奎诺阿尔](https://unsplash.com/@quinoal?utm_source=medium&utm_medium=referral)拍摄的照片。

*一些背景:我的日常活动与 JavaScript 环境有关。我的工作包括研究/设计/实现新功能，改进现有功能，或者解决特定的错误。*

我的软件工程思维每天从早上 8:09 开始——就在我跳上开往根特的 50 分钟火车的时候，在那里我是内容营销工作区 [StoryChief.io](http://www.storychief.io) 的软件工程师。

在那段时间里，我只反复做下面三件事情中的一件:

1.  阅读关于 JavaScript、良好编码模式或特定工具更新的中等文章。我倾向于不要超越这三个主要话题，因为专注于一个小的、具体的、精选的话题集能让我获得一种控制感。
2.  通过为我之前工作的相应特性/bug 写几行来开始一天的工作。这样，当我在办公室打开笔记本电脑时，我已经有了正确的心态和测试环境。我把这看作是赛前的热身，就像运动员一样。
3.  最后，偶尔我会睁着一只眼睡觉！我是一个孩子的父亲，偶尔，夜晚会不那么平静。

> “我不怕练过一万次踢腿的人，我怕的是练过一万次踢腿的人。”— [李小龙](https://www.brainyquote.com/quotes/bruce_lee_413509)

好了，让我们更深入地了解第一个活动的细节，因为其他两个活动都是不言自明的。

# JavaScript 内容

我遵循这两种形式的 JavaScript 内容:

1.  新版本。
2.  相同功能但使用不同代码块的效率比较。一个简单的例子是:

```
const users = [...];const { length } = users;
let i;
for (i = 0; i > length; i++ ) {
  // some statement here
}
```

和...相对

```
const users = [...];
users.forEach((user) => {
  // some statement here
});
```

研究表明，前者速度更快，但不足以抵消后者的可读性。

*奖金提示:*`*continue;*`*`*break;*`*报表在* `*forEach*` *中没有，而在* `*for*` *报表中有。**

*你说了算！*

# *编码模式*

*我最近一直在研究面向对象编程和函数式编程，读得越多，我就越倾向于后者。举个例子，我最初是通过关于 OOP 的讲座学习编码的，我相信我有一个很好的基础来进行合理的比较。*

*我更喜欢 FP 的一点是有厚重的数学背景，而另一方面，我们从非常抽象的概念开始。为什么要把一个已经熟悉数学概念的学生的生活复杂化呢？*

*我不会讨论他们之间的性能差异，尽管我很动心！也许这是一篇独立的文章。*

*我喜欢阅读的其他模式是社区如何组织他们的文件结构，它们之间的优缺点，以及管理应用程序状态的最佳方法。*

# *特定工具*

*我使用的工具示例有 React、Apollo 和 AWS 服务。但是我如何决定使用哪些工具呢？首先明确我的目标。我首先回答以下问题:*

1.  *我认为自己是全栈、前端还是后端工程师？*
2.  *如果我选择前端，然后我比较一些工具，并决定哪些最适合我的风格。对我来说，是反应和阿波罗。*
3.  *如果我选择后端，然后我比较一些工具，并决定哪一个最适合我的风格。对我来说，是 AWS 服务。*

*一些考虑因素是工具的市场份额(因为这定义了你被雇用的机会)，以及你是否觉得在这样的环境中编码有意思(因为你不希望你的第二个月很无聊)。*

*然而，在决定转换环境之前，给它一些时间来适应你的选择是很重要的。这可能会因为没有完成项目而导致困惑和不满。*

# *最后的话*

*最后，我想介绍一下我早上打开笔记本电脑时做的事情。*

*我总是写一份简短具体的当天待办事项清单——通常是三个要点。这样，我可以在一天结束时有一种生产力的感觉，几年后，你也可以发展出正确的感觉来衡量你完成特定任务的估计时间。*

*待办事项列表的一个例子是:*

1.  *实现评论侧栏。*
2.  *研究方便用户添加评论的地方。*
3.  *实施 2。*

*感谢阅读！我希望你喜欢这篇文章。*
# 如何更好地编写面试代码

> 原文：<https://betterprogramming.pub/how-to-get-better-at-coding-interviews-781086ace349>

## 摆脱你的紧张，深呼吸(你得到了这个),搞定你的下一次面试！

所以你想提高面试水平？一切都在方法中——这个指南是一个**一步一步的演练**,告诉你如何回答来自脸书、亚马逊、微软、网飞或谷歌等公司的编码面试问题。

这篇文章会涉及很多内容。它将引导您完成一个常见的技术(白板或非白板)面试问题，您将接触到以下内容:

*   征服紧张情绪所需的心态
*   面试中的每一步
*   要复习什么样的模式

还有更多。在处理这些问题的具体步骤之前，让我们从解决**面试的精神层面**开始。

*注:此文最初发表于 https://algodaily.com*[](https://algodaily.com/lessons/how-to-get-better-at-coding-interviews)**。**

# *克服紧张*

*软件工程和技术面试令人伤脑筋。我们都知道这一点，但我们很少问为什么。*

*为什么这类采访会特别让人产生这种恐惧？*

*![](img/edcc094940d33a85784f9f9471dbd041.png)*

*真的，后果很少。*

*最坏的情况是，你无法正确表达他们所寻找的算法。或者你想不出正确的定义。那么你可能得不到这份工作。*

*这不是一个很好的结果，但还有更糟糕的事情。你总是可以回家，重温你错过的东西，并尝试申请其他地方。*

*知道这一点似乎并没有帮助，以下是原因。*

*我们的恐惧通常来源于不确定性。也就是说，相信你可能会遇到一个你不知道如何解决的问题或挑战。*

*但实际情况并非如此！*

*首先，通过正确的方法——本文的其余部分将探讨这一点——您可以解决任何问题。*

*第二，是的，你有可能被问到一些完全不属于你的领域的问题。但是在大多数情况下，面试官真的很想知道你是怎么想的。作为一个多次站在另一端的人，要知道我们希望你做得好。*

*如果紧张仍然碍事，还有一些其他的解决方法。一些可能有帮助的事情是[每日冥想](https://www.amazon.com/gp/product/1439195455/ref=as_li_qf_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1439195455&linkCode=as2&tag=algodaily03-20&linkId=00452c0588f28a516612a0778b749753)，吃一顿健康的食物给你的大脑补充能量，以及[定期有氧运动](https://www.amazon.com/gp/product/0316113514/ref=as_li_qf_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0316113514&linkCode=as2&tag=algodaily03-20&linkId=2344bdbddf2327be22643a8ac562f0ac)。*

# *如果你完全失去记忆该怎么办*

*对于几乎所有的技术挑战，如果你有正确的方法，你就能找到解决问题的方法。但是如果你真的不知道从哪里开始呢？*

*所以让我们快速解决一些问题——如果你真的没有头绪，下面是该做的事情。*

*假设您是一名前端工程师，有几年的 JavaScript 单页面应用程序开发经验。在面试中，你会被问到以下技术性问题。*

> **您何时会在两个后端系统之间应用异步通信？**

*你僵住了，突然你的胸部绷紧了。您从未在任何后端系统上工作过，并且您已经忘记了`asynchronous`是什么意思。有几秒钟，你盯着面试官，你的思想无处可去。*

*这里有两种可能的方法来解决这个问题:*

1.  *把它和你做过的事情联系起来。*
2.  *强调你对学习和从事这些事情是多么兴奋。*

*第一个回答有效，因为它仍然允许你展示你的经历:*

> **我没有直接做过后端系统，但是你能再提醒我一下* `*asynchronous*` *是什么意思吗？啊，一种允许工作从主应用程序线程中独立出来的编程形式？我用 React* — *做过类似的事情，有时通过 REST 端点将数据插入数据库需要一些时间，但是我们希望立即向用户反馈数据正在被持久化。所以* ***我有根据的猜测*** *就是当你想让一个进程做某件事的时候，同时等待另一件事完成。**

*在这种情况下，这可能不是面试官 100%想听到的，但是你已经展示了一些技术上的敏锐。你也可以加入一些关于过去经历的讨论。*

*另一方面，如果你根本无法将问题联系起来呢？谈论你有多兴奋以及你将如何学习这可能是一个不错的选择:*

> **老实说，我不确定，但我听说过术语* `*asynchronous*` *，并且很想了解更多关于后端系统的知识。这次采访后，我一定会好好研究一下。你推荐什么书或文章开始吗？**

# *白板算法面试好吗？*

*本课的其余部分将讨论如何处理标准化数据结构和基于算法的问题。对于小样本的挑战，比如“这是一个基本的设置，用一些脚手架实现一个 REST API”，它仍然是相关的，但不那么相关了。*

*是的，白板算法面试是有争议的。然而，有几个原因，他们仍然坚持。首先，他们给面试官几个强烈的信号，比如:*

*   *候选人能在他人面前清晰地思考吗(这一课的目的是什么)？*
*   *他们听起来像是已经为面试做好准备了吗(职业道德的信号)？*
*   *他们有合理的逻辑能力吗？*
*   *他们能区分好的解决方案和坏的解决方案吗？*
*   *他们对计算机科学基础知识的掌握程度如何？*

*其次，它们*很容易规模化*，如果你是一家需要每年招聘数千人的企业集团，这一点尤为重要。*

*是的，有带回家的作业，构建一个特色类型的面试，以及为期一周的“试用期”。我相信这些都是很棒的方法。*

*然而，标准的“你能解决我面前的这个问题吗”数据结构和算法问题仍然是大多数软件公司的标准。*

*让我们想想如何解决它们。*

# *优秀受访者的方法*

*在我们开始之前，有一点很重要。为了让这些工作，你必须有你的数据结构和算法。*

*你对这些主题的练习越多，就越容易将它们模式化。到了面试时间，从记忆中找回它们也更容易。*

*一个很好的起点当然是 [AlgoDaily 的高级课程](https://algodaily.com/challenges)和[每日简讯问题](https://algodaily.com)。其他建议可以在我们关于如何准备技术面试的文章中找到。*

*综上所述，以下是我们推荐的解决白板问题的典型步骤。我们将花大量时间深入探讨每一个问题。*

1.  *浏览一到三个输入示例，对问题有所了解。*
2.  *通过询问人类将如何做这件事来快速解开强力解决方案。*
3.  *将强力解决方案与模式、数据结构或计算机科学技术联系起来。*
4.  *再次优化并运行从第一步开始的相同测试用例。*
5.  *如果您有时间，请说出问题的边缘案例和改进。*

# *面试过程中的沟通*

*![](img/2c8a5c8826d904dceb678c101780223d.png)*

*面试的很大一部分是对你沟通能力的测试，这不是什么秘密。软件工程是一项团队运动。*

*孤独的天才程序员的神话很简单——一个神话。这尤其适用于需要成千上万工程师的大型、棘手、有影响力的项目。*

*你如何表现出很强的沟通技巧？*

*你必须继续说下去——我怎么强调都不为过。除非你需要完全的安静来思考——这很好——你应该说出你的想法。*

*   *如果你被卡住了，让面试官知道。*
*   *如果你不明白这个问题，多问一些澄清性的问题。*
*   *如果你不知道发生了什么，就说你需要更多的背景知识。*
*   *如果你需要提示，让他们知道！*

*![](img/7622019cbe641c588771e74da90ddeb6.png)*

*如果你害羞，那也没关系。但是关于面试——要知道你可能会和这个人一起工作，或者和具有相似资质和技术能力的人一起工作。不管是好是坏，面试官在面试中对你的看法就是他们认为你加入后他们会得到的。尽你最大的努力友好和直言不讳，哪怕只是为了得到这份工作的几个小时。*

# *如何收集需求*

*让我们继续讨论实用的技术面试技巧。我们可以考察一下[零点到终点](https://algodaily.com/challenges/zeros-to-the-end)的问题。下面是提示:*

> **写一个方法，将数组中的所有零移动到它的末尾。**

*你应该做的第一件事就是**阐明需求。在你确切知道问题是什么之前，你没有义务去想出一个解决方案。原因如下:***

> *候选人:酷，零在后面，非零在前面。就这样对吗？*

*似乎很简单。为什么不直接解决它呢？因为采访可能会说:*

> *面试官:还有，注意你应该保持所有其他元素的顺序。哦，这需要在 O(n)时间内完成。*

*如果你没有考虑到这一点，你可能已经走上了一条非常糟糕的道路。务必**用自己的话重复问题**并重点阐明。了解需求的全部范围，并重复一遍，这样他们就知道你已经完全掌握了问题的全部*

> **考生:所以我们要把所有的零都移到后面，同时把非零保留在前面。我们还希望保持非零值原来的顺序，并且算法应该以线性时间运行。**
> 
> *面试官:没错。*

# *从输入和输出开始*

*下一步要做的事情是要么要求几个样本数组，要么提出自己的数组。开始构建你的测试用例。它帮助您开始处理如何转换输入以获得输出。*

**试着从一个很小的输入开始，随着你做更多的例子，增加它的大小。你可能会这么说:**

> **考生:很有意思的问题。好了，为了确保我理解了我们想要的转换和结果，让我来看几个例子。所以如果给了我* `*[0, 1]*` *，我们会想要回* `*[1, 0]*` *？**
> 
> *面试官:是的，没错。(考生开始思考如何移动第一个例子中的那个零)*
> 
> *候选人:嗯，对于这个，我们只需要把 `*0*` *和* `*1*` *互换。现在，如果给了* `*[1, 0, 2, 0, 4, 0]*` *，我会想要回* `*[1, 2, 4, 0, 0, 0]*` *。**
> 
> *面试官:正确。*
> 
> **候选:而* `*[0, 0, 4]*` *变成了* `*[4, 0, 0]*` *。嗯（表示踌躇等）...**

# *如何想出一个蛮力解决方案*

*既然您已经尝试了一些输入和输出，那么要问的主要问题是:*

> *如果没有机器，人类如何手动解决这个问题？*

*记住，电脑只是工具。在我们拥有它们之前，人类必须手工计算。因此，问问自己如何手动操作是集思广益解决问题的好方法。当循环和条件句不可用时，你可以用简单的英语说你需要做什么。*

> *嗯，对这些例子来说，结果总是返回一个非零数组和一个末尾为零的数组。思考一个非常基本的实现，我一直在做的是:迭代，找到非零值，并把它们放入另一个 `*temp*` *数组中。然后我用* `*0*` *填充结果数组的剩余部分，直到我们得到原始长度。**
> 
> *面试官:有意思。想要写出实现吗？*

# *使用伪代码来阐明你的想法*

*除非一个算法非常简单，否则你会想先写伪代码。*

*对于暴力解决方案来说尤其如此。面试官可能对*只是*第一遍的伪代码没什么意见，可能会要求你把剩下的时间花在求解和编写优化的解决方案上。*

*此外，如果你发现一个有害的错误，用伪代码思考更容易修改。这是它最初的样子:*

```
*temp = []
zero_count = 0
iterate through array:
  if nonzero, push to new temp
  if zero, increment count
for zero_count times:
  push to temp
return temp*
```

*如果面试官把问题变得更复杂了，这是一个好迹象，表明你的思路是对的。他们可能通过添加一个约束(在恒定时间内完成)，或者通过使输入显著变大来完成。根据我的经验，大多数面试官会计划做一个简单的问题和一个较难的问题。*

> *记者:太好了，现在你能在不实例化一个新数组的情况下做到这一点吗？*

*不要在这一点上失去冷静，也不要对通过第一部分过于兴奋。是时候将我们的暴力解决方案与一种技术结合起来进行改进了。我们现在将介绍一些实现这一目标的方法。*

# *如何利用模式和抽象进行优化*

*在你完成大约 50-100 个模拟面试挑战后，你会开始认识到你可以利用的模式。这里有一个例子:`If you want speed, you usually need more space/memory.`这与下一节关于使用数据结构的内容特别相关。*

*查看目前为止你的解决方案中的每一步，并思考任何可能的方法来简化或分解它。有什么方法可以降低它的复杂度？*

*一个窍门是从更高的层面思考你在做什么。这里，我的意思是让你自己从逻辑的杂草中走出来，回到输入到输出。在上面的例子中，是的，我们通过连接数组将 0 移动到末尾，但是我们实际需要做什么呢？该过程可以被认为是:*

*   *识别非零元素。*
*   *将元素放在不同的索引处。*
*   *找出有多少个`0`。*

*拥有如上清晰步骤的好处在于*你现在可以探索完成每个步骤的替代方法*。*

*   *例如，要标识非零元素，可以迭代数组并使用条件。*
*   *或者，你可以使用一个`filter`方法。*
*   *如果这没有帮助，你也可以连续寻找多个`zeros`和`splice`一个新的数组。*

*其他要问自己的:`What am I trying to do in plain English?`*

*另一个取得进展的简单方法是**尝试输入。***

*   *如果是收藏的话*排序*或者*分组*有帮助吗？*
*   *如果是树，我们能把它转换成数组或者链表吗？*

*![](img/63b9b19f5bd92a1888a4132b20ae9c1d.png)*

*如果调整输入没有效果，也许是时候做更大的改变了。*

# *引入数据结构或抽象数据类型*

*这就是研究数据结构(以及实现和使用它们的经验)真正有用的地方。如果您能够识别瓶颈，您可以尝试使用数据结构来解决问题，看看是否有任何性能或空间上的改进。*

*回到我们之前做的[零点到终点](https://algodaily.com/challenges/zeros-to-the-end)的问题，我们的瓶颈很可能是`putting elements at different indexes`的步骤。在这种情况下，我们可能会意识到使用一个`counter`变量是有益的。*

**注意，数据结构不需要花哨。在我们的例子中，我们只是简单地引入了一个* `*int*` *变量* — *，但有时这就是你所需要的。**

*`counter`应该算什么？好吧，一旦我们把数组分成非零(`[1, 2, 3]`)和零(`[0, 0, 0]`)，我们只关心非零的结束位置。*

*因为我们不需要担心非零元素之后的数组是什么，所以我们可以简单地保留一个单独的指针来跟踪最终数组的索引。它会让我们知道零从哪里开始。*

*然后，我们可以编写以下伪代码来利用这种策略:*

```
*insert_position = 0
for i in nums
  if i is not 0
    increase insert_position
    keep it in insert_position
  fill the rest in with 0*
```

*尽管有两个循环，时间复杂度简化为 O(n)。然而，由于我们使用相同的数组，空间复杂度是恒定的，所以我们有所改进！*

# *战术数据结构备忘单*

*需要快速访问集合中的元素吗？**数组在内存中已经有了位置。***

*必须快速插入数据？**添加到哈希表或链表中。***

*O(1)时间内需要最大值还是最小值？**一堆一堆地叫。***

*需要建立连接模型？在那里放一张图表。*

*![](img/45d7ed7796ccaa08e75e69b827b215e3.png)*

*你知道的数据结构越多越好。你可以查看课程设置中的绝对要求清单。*

*尝试更高级的结构也是有用的(但不是必须的)——想想 AVL 树、尝试、优先级队列、后缀数组和 bloom 过滤器。他们不太可能被需要，但他们在行业中很有用，在面试中拿出来会给人留下深刻印象。*

***关于哈希表的特殊说明:***

*非常了解这些家伙！它们可以用在数量惊人的溶液中。*

**许多问题可以归结为在大型数据集合中搜索元素，在所述集合中寻找重复项，或者存储/检索项目。哈希表/哈希表在这些方面做得非常好，所以要时刻把它放在第一位。**

*如果额外的数据结构没有帮助，可能是时候尝试一种老式(但可靠)的技术了。*

# *介绍一种计算机科学算法技术*

*有几个技巧是每个人都应该知道的。通常这些都包含在`Intro to Algorithms`类中来对算法进行分类。*

*它们通常是工具，不仅对面试有益，而且对一般的软件工程工作也有益，所以去了解它们吧！*

***分而治之**:尽量把问题分成更容易想通或解决的子问题。这考虑到了…的可能性*

*递归——看看你是否能利用一个调用自身的函数。对树的递归要特别小心。*

***备忘录化** —您在强力解决方案中生成的部分结果可以用于更大或不同的输入吗？如果是这样，利用某种缓存。您可以将哪些数据存储在内存中(或创建并存储在内存中)来帮助简化算法？*

***贪婪** —思考每一次迭代或每一步的最佳举措是什么。每一步都有明显的吗？这在像 Dijkstra 算法这样的图遍历问题中经常出现。*

# *当以上方法都不起作用时该怎么办*

*因此，上述模式、数据结构或技术都没有揭示这个问题。怎么办？*

*你有两个选择。*

***多提问。***

*运筹学*

***说“我卡住了。我能得到一个提示吗？”***

*继续沟通！面试官通常非常乐意给你一个提示——事实上，这是他们的工作。不幸的是，某些面试问题会有一两个“关键直觉”,你必须在找到解决方案之前找到它们。*

# *有了可行的解决方案后*

*这是一把巨大的钥匙，点击它需要一段时间。在您为优化的解决方案编写了伪代码之后，**通过您的伪代码一步一步地手动运行一到三个示例输入，以确保它能够工作**。警告:神经会在那里，你可能会不时地失去你的位置，但这是极其重要的。*

*优秀的工程师会彻底测试他们的代码，并能逐步通过逻辑。拥有伪代码解决方案和在白板上写代码之间的时间是演示这一点的好时机。*

*在这一点上，您还将能够剔除不正确的假设或做出重要的实现(“哦，等等，我们应该使用`Map`而不是 JS 对象”)。*

> **候选人:先说*`*[1, 0, 3]*`*；我认为这是一个很好的测试候选人。好了，从* `*1*` *开始，我们看到它不是零，所以它可以原地不动。我们要去下一个元素，所以让我们增加最后的数组索引计数器。现在我们有* `*0*` *，让我们跳过。好的，* `*3*` *，不是零，我们的计数器在* `*1*` *所以我们把它放在* `*1*` *之后，我们有* `*[1, 3]*` *。太好了！然后我们在最后加上一个* `*0*` *就得到我们想要的结果了。**

*面试官可能会说“太好了，让我们把它编码起来”，或者他们可能会试着了解你对自己的解决方案有多自信。如果输入-输出检查完毕，您应该对继续前进感觉良好。*

*注意:如果你要在白板上面试，买一个[磁性白板干](https://www.amazon.com/gp/product/B075LLKL9F/ref=as_li_qf_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B075LLKL9F&linkCode=as2&tag=algodaily03-20&linkId=112235175903de2af465b96357b3c5d4)在上面练习手写代码。*

*关于在白板上编码，有很多事情人们没有考虑到:主要是空间管理，但也包括如何使用更短的变量，以及水平书写。*

*无论如何，现在您已经编写了这段代码:*

```
*function zerosToEnd(nums) {
    let insertPos = 0;
    for (let i = 0; i < nums.length; i++) {
        if (nums[i] != 0) {
            nums[insertPos++] = nums[i];
        }
    }

    for (let j = insertPos; j < nums.length; j++) {
        nums[j] = 0;
    }

    return nums;
}*
```

*一旦你写出了你的解决方案代码，技术部分就应该完成了。*

*面试现在将转向面试官的问题。确保你已经准备好了问题，尽量不去想你的表现。*

*在这一点上，无论发生什么都不在你的控制之内，所以向前看，把你的注意力放在与面试官的当下。如果你能保持镇定，你会给人留下更加专业的印象，即使面试并不完全如你所愿。*

*我希望这个指南对你有所帮助。请记住，任何编码挑战都可以用正确的方法和正确的心态来解决。祝你好运！*

**原载于*[*https://algodaily.com*](https://algodaily.com/lessons/how-to-get-better-at-coding-interviews)*。**
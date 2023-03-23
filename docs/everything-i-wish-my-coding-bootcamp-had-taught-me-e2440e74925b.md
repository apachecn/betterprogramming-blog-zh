# 我希望我的编码训练营教会我的一切

> 原文：<https://betterprogramming.pub/everything-i-wish-my-coding-bootcamp-had-taught-me-e2440e74925b>

## 训练营买家请注意

![](img/b4909e084312d7c920e71e1a2df93038.png)

由 [Flickr](https://www.flickr.com/photos/devbootcamp/16029705607) 上的 [Dev Bootcamp](https://www.flickr.com/photos/devbootcamp/) 拍摄的照片

我最近完成了熨斗学校为期 15 周的编码训练营，这是一个基于 Ruby、JavaScript 和 [React](https://reactjs.org/) 框架的实用 web 开发入门。

接下来是我一生中最轻松的求职过程。招聘人员的要求让我应接不暇，我的日程表上塞满了我实际上无法参加的面试。这些也是高质量的工作:像思维机器、奥卡多科技和斯特林银行这样的名字。开始寻找工作不到两周，我就愉快地找到了工作。

那么为什么我对我所学的东西感到如此不满意呢？部分原因是怀疑我得到了很多这样的面试，不是因为我作为一个蹒跚学步的开发人员的能力，而是因为我的名牌大学教育。我是剑桥的毕业生，也是一名科学家——从事打开大门的技术。

可悲的是，顶级职位要求的技能与能否在一周内组装一个网络应用程序几乎没有任何关系。web 开发的实践基础与真正的软件工程师所需的大量理论知识相差甚远。

在熨斗学校呆了三个月，经历了一连串令人印象深刻的超水平工作面试后，以下是我希望我的编码训练营教会我的一切。

# 1.数据结构

![](img/32de156755862f5a8ee5a06066ad0009.png)

照片由[米卡·鲍梅斯特](https://unsplash.com/@mbaumi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

哈希是如何工作的？B 树比数组有什么优势？如何识别 2D 空间中的哪些点落在给定矩形的边界内？

要编写高质量、高性能的代码，必须理解它所依赖的数据结构。这就是当你在嵌套循环中迭代时突突作响的程序和在固定时间内进出散列时歌唱的程序之间的区别。

在技术面试中，你会被要求优化你的解决方案。除非你明白你所写的代码是如何工作的，否则你无法做到这一点。最起码，你应该考虑单向和双向链表、平衡树(尤其是 AVL 树)、散列(或者在 Ruby 之外的语言中称为*地图/字典、*)、队列、堆栈和图形。

您应该了解如何访问它们的元素以及与此相关的计算成本。这没得商量。

渴望开始吗？Ale Miralles [有一个关于 Ruby 数据结构的系列](https://medium.com/amiralles/mastering-data-structures-in-ruby-recap-682a698b90d0)我推荐。在这个 GitHub 回购中，我对它们[进行了自己的诠释。](https://github.com/AngusGMorrison/data-structures)

# 2.算法

![](img/ce7dc680182913ecca27dbf595fcc79a.png)

由[罗马法师](https://unsplash.com/@roman_lazygeek?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/maths?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如何处理数据结构和结构本身一样重要。两者密不可分。如果你不知道运行日志 *n* 相对于 *n* 时间意味着什么，你需要马上开始钻研算法。这是一个调整良好的程序和在某些情况下，一个落后的，令人震惊的混乱之间的区别。

首先，要理解您的首选语言的默认方法是如何执行搜索和排序的。理解这些方法有意义的环境，以及不同的算法何时会做得更好。一旦你理解了类似图表的数据结构，就要学习寻路算法如何在点与点之间导航。

学习的黄金标准是托马斯·h·科尔曼的《算法导论》，但它是一头野兽。要找一个不那么吓人的切入点，只需谷歌“基本排序算法”一旦头痛过去，你就不会后悔投入了时间。

# 3.数据库

![](img/a59aeccca3c2c1d1df4d0d3a5fe9ecdd.png)

照片由 [Kolar.io](https://unsplash.com/@jankolar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Flatiron School 教授 Ruby on Rails，包括其准神奇的对象关系映射器(ORM)，活动记录。Active Record 在 Ruby 中采用简单、用户友好的方法调用，并完成将数据放入所选 DBMS 并再次取回所需的所有繁重工作。

大多数训练营的学生会忽略学习 SQL。真正的开发者需要 SQL。更具体地说，他们需要了解数据库如何处理他们的查询，并使用这些知识来优化他们的请求。数据库访问是一个性能瓶颈，了解如何加快速度将使您遥遥领先。

最重要的是，您应该了解您的首选数据库管理系统(PostgreSQL、MySQL、Oracle 等。)实现索引—以及何时自己使用它们。支撑索引的数据结构是我们在等待查询返回时不会感到自己变老的原因。

使用索引，卢克会告诉你你想知道的一切。

# 4.部署

![](img/4c34720bc30fe627e5d7393fa1f0dad7.png)

由[泰勒维克](https://unsplash.com/@tvick?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/server?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

AWS、Docker、Kubernetes——这些都是雇主需要的技能，但如果你参加训练营，你不太可能学到关于部署的第一件事。

为了我在熨斗学校的最后一个项目，我开始自学一些关于 Docker 和 AWS 的知识，以帮助我在上午的面试中脱颖而出。成功了。这也让我意识到主题部署本身是多么庞大。除了编写代码本身，这是软件开发人员不可缺少的第二个同样重要的技能。这是需要在语境中教授的东西。

你可能会问，除了让新手第一次编码之外，训练营什么时候有时间教部署，但我会问，如果你不能在网上获得它，那么 web 应用有什么用？

开始探索用户友好的 AWS 替代方案 Heroku。一旦你习惯了，Docker、Kubernetes 和 AWS 都有大量高质量的教程供你学习。我建议有条不紊，一步一步来。进展缓慢可能令人沮丧，但坚实的基础是必不可少的。

# 5.静态打字

![](img/5296a596f853147283d20cf76eda3286.png)

照片由[帕特里克·grądys](https://unsplash.com/@patrykgradyscom?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/control-room?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

好吧，这是一个人的个人偏好，可以说不适合真正的初学者。在 Flatiron School 毕业时，我开始自学 Java，这是一种静态类型的编译语言，对并发性，尤其是并行性有很强的支持。

我可以自信地说，这让我成为了一名更好的程序员。这并不是因为我正在做的事情复杂到需要 Java 强大功能的全部范围，而是因为在类型系统中工作，您必须声明输入的类型和函数返回值，这迫使您更仔细地考虑您的代码。

编程变得——最好的方式是——更慢、更深思熟虑和深思熟虑。重点转移到代码如何工作，而不仅仅是它做什么。

在职业生涯的早期阶段，没有人会夸大广泛经验的价值——尽可能多地探索和感受编码的方式。

如果您自己对探索 Java 感兴趣，我推荐凯·s·霍斯特曼的《为没有耐心的人准备的核心 Java SE9》然而，从我最近找工作的经验来看，对 Go 程序员的需求正在迅速上升。它仍然是静态类型的，但可以说学习起来更愉快。

# 结论

这是我希望我的训练营教会我的一切。但这难道不是软件开发的美妙之处吗？不管你有多优秀，探索了多少途径，开发了多少杀手级应用，总有更多的东西需要学习？
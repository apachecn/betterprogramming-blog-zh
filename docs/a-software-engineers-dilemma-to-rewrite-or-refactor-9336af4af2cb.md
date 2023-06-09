# 软件工程师的困境——重写还是重构？

> 原文：<https://betterprogramming.pub/a-software-engineers-dilemma-to-rewrite-or-refactor-9336af4af2cb>

## 当我年轻的时候，我总是想重写，然后我意识到这是不可能的，现在我又开始怀疑了…

![](img/791275bd61e9f5b8e9b83ee1b3efefe5.png)

Jasmin Sessler 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 熟悉的呼救声:

> *“代码库一片混乱。理解做出改变的影响比理解改变本身需要更多的时间。由于间接寻址和接口的数量，性能越来越慢。我们有一个新的重大变化要做，在盯着代码看了几个小时之后..或者几天..我想知道这个代码库是否需要完全重写。”——*每个开发人员都有这样的经历

我相信我们都抱怨过遗留和继承的代码库。我了解到最初想要扔掉它从头开始的冲动通常是一种业余的本能(新代码→新 bug)。但是有时候你真的应该重写——但是我们怎么知道什么时候重写呢？

# 我见过的重写成功和失败的例子

2016 年，优步做了一次全面的重写，这是有据可查的，大多数人认为这是非常成功的，是迫切需要的，并允许低薪司机和挤满我们人行道的送餐自行车的扩大和扩散——[https://blog.pragmaticengineer.com/uber-app-rewrite-yolo/](https://blog.pragmaticengineer.com/uber-app-rewrite-yolo/)

https://queue.acm.org/detail.cfm?id=1531242[KDB 的亚瑟·惠特尼以每 4 年从头开始重写整个数据库内核而闻名，这听起来难以置信，但他是一个奇怪的天才，内核很小](https://queue.acm.org/detail.cfm?id=1531242)

我管理交易系统有 10 年了，有各种各样的经历:

*   FX 团队做了一些重大的重写，比如从 C++到 Java 后端，从 Java 前端到 JS。作为面向消费者的工具可能推动了这一点，但结果是他们的平台获得了最佳奖项
*   债券和期货电子交易系统团队有一个“只重构”的方法，结果是一个老化且难以维护的生态系统，每个人都讨厌它。众所周知，测试和发布非常困难
*   一个本地市场债券交易系统进行了全面的重写/移植，但它悲惨地失败了，因为新系统有太多的缺陷和用户投诉。我们痛苦地结束了从未完全上线的新系统(是的，很遗憾，但我可以忍受)

事实上，我工作过的大多数系统和团队，不管是商业的、内部使用的、成功的还是失败的，都很少被重写。有几次，当我们进行重写时，我经常震惊地发现有人得到了预算和批准的领导权。我相信重写是非常罕见的(我没有统计数据，但我怀疑今天 prod 中只有不到 5%的系统进行过重大重写)—我记得在 Oracle，他们早在 2000 年就在开发 9i，开发人员谈到 Oracle 5 的代码库中仍然有一些完全混乱的部分，所以他们让退休的老开发人员回来帮忙！

根据我 25 年来对软件开发的观察，这让我想到了一些经验和最佳实践。

# 何时重写

无处不在的答案在这里起作用(“视情况而定”)——它需要一个预测和对未来的押注。不是通灵者，这里有一些想法可以作为重写的理由:

*   当每个人都讨厌在框架中编码，并且一直抱怨*(同样如果每个人都抱怨你，去洗澡)*
*   回归测试比做出实际的改变花费更多的时间
*   你的产品在第一次繁荣中存活下来并茁壮成长，你只有很少的时间和资金进行再投资
*   您的系统具有可靠的测试覆盖率，团队对当前的代码库和逻辑很有经验，可以低风险地完成重写
*   您的接口已经很好地建立，因此第三方的迁移将是简单和低风险的

我敢肯定，重写需要大量的时间和巨大的风险是显而易见的。这段时间你的产品会停滞不前。如果你没有顾客动力，那就不要浪费你的时间。如果你的代码库“足够好”,你可能需要等待。如果你没有可靠的测试覆盖和干净的接口，你将永远无法测试和确保安全上线。重写需要实现根本性的飞跃，而不仅仅是清除烦恼。

# 何时重构

如果你曾经处理过车祸，你会知道人们并不经常修理汽车或零件，他们会更换它们。然而，软件工程师应该像老派工匠一样思考，修复、重构和延长他们软件的生命。*(因为软件是定制的，我们通常有廉价的老板和用户，他们不会为重写付费..)*

关于重构(与重写相比)的一些关键想法:

*   您必须有一个可靠的测试套件来进行任何重构(或重写)。遵循肯特·贝克和马丁·福勒的建议
*   对一个模块缺乏全面的理解是不要重写的一个明显标志，并且可能意味着它需要首先被仔细地重构
*   重构也需要时间，所以确保你让管理层明白重构和交付高质量代码的价值——最终你可以说服他们也做一次完整的重写
*   不要放弃重构——只有在多次重构之后，代码库才被认为需要重写。通常情况下，事情只是让我们烦恼，但这并不意味着你应该扔掉代码库

一个好的心态——期待有一天你需要重写代码库。继续用更好的测试、更好的可读性和更干净的界面来重构代码——当神奇的一天到来时，这将允许干净和成功的重写。

> “预计有一天你将需要重写代码库”

# 最后的想法

在我职业生涯的早期，我记得我重写了某人的模块，因为我不理解它，并且草率地宣称它是垃圾(对不起——是的，是你！).这不是废话(不是很好)——只是理解别人的代码真的很有挑战性——事实上，我有时在 3 个月后理解自己的代码有困难。

最后一点建议:

*   在学习代码库时运用同理心，并考虑作者时代的限制
*   抵制重写/替换和重构的冲动——让代码库比你加入时更好

最后的最后一点——等太久再写会让重写变得更加困难，最终变得不可能——所以不要等太久。
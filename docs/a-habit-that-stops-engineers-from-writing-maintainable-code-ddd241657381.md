# 阻止工程师编写可维护代码的习惯

> 原文：<https://betterprogramming.pub/a-habit-that-stops-engineers-from-writing-maintainable-code-ddd241657381>

## 考虑可重用性是没有意义的

![](img/0cc863f4f2aaa6f4bedcab1d64517bd7.png)

[王占山](https://unsplash.com/@jdubs?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

每个软件工程师都有投机的习惯，我也不例外。

当有一种使代码尽可能经得起未来考验的冲动时，这种习惯就会发生。

你开始想象你可以用代码做的所有事情，而不是现在设计一个可靠的、功能性的、易于理解的代码。

# 考虑可重用性是没有意义的

促使工程师考虑代码可重用性的一个根本原因是产品需求不断变化。因此，所有可能的设计都会让你发明一些场景，在这些场景中，你的特殊代码在新的想象中的未来条件下是有用的。

他们沉迷于自己设计的每一个功能和即将编写的每一段代码:

*“我怎样才能使它尽可能地可重用？”*

*“如果六个月后我们还需要 X 能力呢？我可以添加另一个抽象层，使其具有可扩展性。”*

*“我们需要放入一个队列，使其异步，因为如果我们有高负载摄入，服务器不堪重负怎么办？”*

*“我们想把这个功能抽象成一个策略设计模式，因为如果营销人员想实现更多的算法呢？”*

然后他们留下了无限可扩展和可伸缩的代码，这些代码既不容易也不可伸缩，因为没有人能理解它。

如果他们的抽象层和设计模式遇到任何阻力，他们必须证明他们能够预测未来。

他们浪费工程时间和资源，与其他工程师争论可能永远不会发生的未来——这些争论主要是猜测。

快进到未来，公司雇佣了一个新的开发人员，他需要在他们所做的过于复杂的框架上开发特性。

现在，他们不得不在白板会议上花三个小时，沮丧地解释复杂的抽象是如何工作的。

他们为什么要这样对自己？

你花了很多时间试图让代码变得漂亮，一层又一层优雅的抽象，这是巧妙的，值得一篇博士论文。你需要解释这些难以调试的代码层。

所有工程师对这种抽象代码层的唯一期望是，系统中不会有 bug。我不想修复一个非常抽象的 bug，因为我想知道我正在修复的东西是否会破坏系统的其他部分。

你不应该在“现在就写代码”方面走得太远，否则你会留下一堆乱七八糟的东西，每次代码库被触及时都需要重构。

需要有一个平衡的行为。

# 停止投机习惯的三个技巧

我过去喜欢使用设计模式来编写代码结构。员工工程师会在我的公关上指出这一点，我需要证明我的行为是正确的。他们评论道，“为什么你要创建这么多的间接方式，让你自己以后更难改变代码库？”然后把我链接到这个关于抽象的经验法则的黑客新闻。

## 1.三法则

该规则背后的原则是，从重复的代码中提取好的抽象比重构错误的抽象更容易。重用代码时，复制一次，第三次才抽象。

> “如果某个东西被使用过一次，忽略任何抽象。如果用了两次，就抄一遍，比较好。如果它被使用了三次或更多次，看看写一个适合我们今天而不是未来的抽象。如果抽象允许我们在将来容易地扩展，那将是加分的，但是任何事情都不应该用“如果”来证明。”—黑客新闻

假设您创建了一个可重用的功能，并且需要澄清什么是可重用的，什么是已经被使用的。在这种情况下，这通常是一个迹象，表明您正试图过早地使某些东西可重用。

然而，这种说法经常被误解。选择正确的方法来概括和分解代码取决于具体情况。

我经常在我们的系统中发现 2-3 个用例之间有 90%的重叠。例如，在支付系统中，授权和退款流程非常相似，但有 2-3 种不同的功能，如支付提供商流程中的模型和一些异常值。许多工程师抽象出可在所有操作间共享的主要流程，并用回调或配置变量注入某些流程部分。

最初，当主流程执行相同时，这种方法工作得很好。然而，产品需求和外部集成流程会发生变化，使得整个系统非常紧密地耦合在一起，并且更难理解在给定的配置中会发生什么。

不要因为流程的某些部分不同而创建一个抽象的系统或接口，而是尝试创建一个函数或组件，因为每个用例都有自己的高级代码路径，从这些函数中挑选。

例如，授权的支付流需要服务 X 提供支付细节。然后，它将会话写入数据库 Y，并在支付提供商 z 上进行交易。退款流需要调用服务 x。然后，它将其值写入数据库 Y。之后，它调用服务 V 来获取退款信息。最后，它调用支付提供商 Z 进行退款。

![](img/b5fad7dac4002c15ca80e8b756f5458e.png)

由作者提供

大多数工程师会通过调用服务 X、数据库 Y 和支付提供商 z 将支付流抽象到一个接口中。然后，任何其他特定的调用将被放入回调函数或配置变量中。

我们可以将授权和退款流程复制成两个独立的流程，而不是让它成为一个巨大的接口。抽象出特定的调用，比如服务 X，并写入数据库 y。然后，将这些方法组合成两个独立的流。

这创造了可读性，因为高级步骤比低级抽象语法更容易在人脑中推理。

当您的代码在三个不同的地方使用时，请考虑抽象和可扩展性。如果你在一次代码审查中被拒绝，要求你去掉某些抽象的东西，你应该退回去问他们，“*抽象现在会带来什么价值？*”。

如果你注意到在构建一个可重用的部分的过程中，抽象可能需要被修正，停止继续沿着这条路走下去。记录您的想法，并与您的团队分享或作为经验教训分享。忽略构建可重用代码的所有想法，直到你或其他工程师注意到该功能至少被使用了三次，在三个不同的地方被复制。

## 2.专注于使其易于删除，而不是易于更改

如果你创建了一个原型，违反 DRY 和复制你的代码是没问题的。如果你正在为自己或内部团队开发一个应用程序，编写长函数是可以的。把所有的设计模式，如干，YAGNI，和固体作为建议，但不是绝对的。

每一个设计模式和原则都应该根据它们的使用环境来看待。如果系统只是暂时使用，并且会被丢弃，是否需要创建一个健壮的测试用例？如果你正在创建一个脚本来帮助你自己更有效率，你需要实现 DRY 吗？

如果由于您推送的代码而出现中断，有人能够识别您的代码并删除它，而不用担心这个更改会导致进一步的中断吗？

需要学习这门语言的新手也应该能够导航并对代码进行可扩展的特性更改。

工程师在任职期间所能做的最糟糕的事情就是维护糟糕的抽象。它们要么太具体，因为它们是在没有足够的已知用例的情况下编写的，要么太一般，涵盖了太多可能的用例。无论哪种方式，难以更改的代码通常不是重复的代码，而是过度重用的代码。任何需要修复一些 bug 或者在到处都在使用的代码上开发一个新特性的开发人员，总会感到焦虑。

*“如果我修改了代码，却破坏了我还没有考虑到的某些特性，该怎么办？”*

当设计一个系统、界面或代码时，我想到的一个理念是只写必要的代码。只有在设计系统时才考虑可扩展性，因为很多都不会用到。

如果外部模型中的字段是不必要的，不要将该字段放在您创建的模型中。如果需要，让未来的你或其他工程师处理新的领域。

## 3.经常重构你的代码没问题

代码库不是历史书。

它是为了根据客户和产品需求进行维护和更改而创建的。

这就是代码库不断发展的原因。

新的闪亮的设计模式将会过时，一种新的编写代码的方式将会出现。

这曾经是为依赖注入编写 Scala 蛋糕模式的正确方式。然而，工程师们很快意识到蛋糕图案并不像看起来那么灵活。在更大的项目中，工程师们意识到使用蛋糕模式将所有功能紧密地结合在一起。一旦你创建了一个“自我类型”作为依赖，就很难删除这些依赖。他们意识到你不能只在需要的时候才注射这些成分。

您必须将您的基础设施、域服务、持久性和业务逻辑组合成一个巨大的依赖项。当您在 rebase 过程中遇到冲突时，可能会出现一个新的依赖项，而您并不拥有它。Scala 编译器会对你大喊，“*自型 X 不符合 Y* ”对于 50 多个组件，您只能猜测哪个出现故障，或者通过移除组件开始进行二分搜索法，直到错误消失。

随着产品需求的发展和客户群的变化，应该鼓励工程师经常重构他们的代码，以提高他们的代码质量。

如果我们不重构它们，我们的代码就会开始发臭。例如，如果我们不拆分功能，它们会随着时间变得越来越大。参数也是一样——如果我们不重构，我们的方法会做越来越多的事情，这可能会导致它更难阅读。

代码和系统被设计成经常维护。它就像一个不断进化的有机体。

# 结论

构建可维护软件的关键点是只构建它们来适应当前的需求。

过度思考可重用性和抽象只有在你的代码可以被修改或删除时才有用。

当我尝试实现或更改特性时，我总是牢记的三个技巧是:

1.  评估是否有东西需要用三法则进行抽象
2.  以一种将来易于删除的方式编写代码
3.  当你开发一个新的特性时，可以接受重构

你还能做什么其他的方法和技巧来使你的系统可维护？

【https://pathtosenior.substack.com】原载于[](https://pathtosenior.substack.com/p/a-habit-that-stops-engineers-from)
# 你应该用接口编码的 5 个理由

> 原文：<https://betterprogramming.pub/5-reasons-why-you-should-be-coding-with-interfaces-c6a92560afa1>

## 期望值、可测试性等等

![](img/eb96b94de0aee54c589f3f8b8143db50.png)

[timJ](https://unsplash.com/@the_roaming_platypus?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

[在我的上一篇文章](https://medium.com/better-programming/code-against-interfaces-not-implementations-37b30e7ab992)中，我们讨论了什么是接口以及如何使用它们。我再重复一遍:你应该一直使用接口。嗯，反正差不多一直都是。几乎所有的事情。尽可能多。我之前说过，它们可能是你武器库中唯一最有效的工具。这是一个相当重要的声明，我不会轻率地使用它。

做什么和如何做是容易的部分。不难理解这一切是如何运作的。这似乎是许多人的症结所在。我知道对我来说很久了。你到底为什么要用这些疯狂的东西？

嗯，原因是这样的。

# 1.针对抽象进行编码

接口允许你做一些对编写好代码至关重要的事情:它们将使你能够抽象地编程。纯粹的抽象概念。为什么要编程抽象而不是实现？简单的答案是这样的:接口是你可以耦合代码的最小、最薄、最不复杂的东西。我希望您现在已经知道松耦合代码是好的。

现在，如果你的代码是完全解耦的，那么它根本不能做任何事情。代码必须与某些东西耦合才能提供有用的功能，所以“完美解耦”的代码有点过了。你想要的是松散耦合的代码。非常松散耦合的代码。您希望代码的耦合尽可能地松散。如果除了接口之外，你从来不耦合任何东西，那么这就是你所能得到的最松散的耦合。

最终，这是您应该使用接口的根本原因:它们为您的代码提供了一个非常薄但非常强大的抽象。把你的代码想象成一边有针，另一边有洞的乐高积木。有了这个松散耦合的接口，您可以将任何块连接到其他块，并创建您想要的任何东西。如果红色方块和绿色方块的引脚有不同的孔尺寸，这将是非常有限的，不是吗？正如我以前说过的，一个好的开发人员是针对抽象而不是实现来编码的。接口是创建抽象的好方法。如果你想彻底讨论为什么这是一个好主意，我建议你读读 Erich Gamma 关于这个话题的观点。

# 2.可插拔实现

如果你用抽象来编程，你就不能把自己和一个特定的实现联系起来。接口允许你使类之间的耦合非常松散。类应该在很少或没有外部依赖的情况下独立开发和测试。但是他们几乎肯定要依靠一些东西。当然，一旦你创建了一个设计良好的类库，你需要把它拼凑起来创建你需要构建的系统——就像一个孩子可以用乐高积木构建几乎任何他们想要的东西一样。最终，接口是一个类可以依赖的最轻最薄的东西。我把耦合到接口比作“抓烟”所以，如果你主要用接口编程，你不能不创建非常松散耦合的代码。而且(我有充分的理由重复我自己)我们都知道松耦合代码是好的。所以接口有助于产生好的代码。

但是还有更多:接口还允许你改变实现，甚至在运行时。因为您处理的是一个接口而不是一个实现，所以当您需要时，您可以非常容易地挑选您想要的实现。例如，您可以编写如下代码:

这是一个愚蠢的例子，但是你可以看到这是多么有用。您可以有一个单一的接口，并根据需要在运行时选择适当的实现。我会说，如果你不能在运行时选择你的实现，那么你的代码就太紧耦合了。

一个更实际的例子可能是一个`ICreditCardProcessor`接口，您可以根据客户的选择实例化不同的信用卡实现。因为您不依赖于特定的实现，所以您可以使用单一的`ICreditCard`接口，同时很容易改变使用哪个实现。如果您想添加一个新的信用卡处理器，就像实现一个新的类并将其添加到您用来选择信用卡处理器的方法中一样简单。不要改变处理信用卡的主要代码——不管你用什么实现。

当然，您可能认为您的接口实现非常好，但是更好的可能会出现。您希望能够轻松地插入新的实现，而不必彻底更改您的代码。界面使这变得非常可能和容易。一个好的经验法则是:

这听起来太好了，不是我的原话。我找不到它的来源，我也确实试过。如果引用是你的，请让我知道，我很乐意提供适当的归属。

# 3.模块间通信

但是等等，还有呢！接口有利于模块间的通信。假设您有一个大型系统，不同的团队在不同的重要模块上工作。这些团队负责在他们的模块中提供功能，因此他们负责模块中代码的完整性和质量。假设你在 widget 团队，你需要链轮团队为你做一些事情。你去找链轮的人说，“嘿，我需要在链轮代码中添加一些东西，这样我们就可以在我们的小部件中做一些链轮类型的东西。”链轮的家伙会嘲笑你——就像他们会让你在他们精心制作的系统里瞎逛一样！

不，相反，他们可能会问你需要什么，构建功能，并给你一些代码，包括一个接口和一个实例化该接口实现的工厂。他们不会让你接近他们的代码，但是他们很乐意让你有一个进入代码的接口。你得到了你想要的——一些链轮功能——他们不需要向你展示任何东西，只需要一个接口。

后来，如果他们完全重新架构他们的代码，你关心什么？即使它们完全改变了内部实现，您的接口仍然可以工作。这是一种很好的开发方式，而这一切都因为接口而成为可能。

# 4.可测试代码

这还没有结束。接口使你的代码可测试。如上所述，因为您正在使用接口，所以您可以很容易地替换您想要的任何实现。如果您正在测试并且不想连接到生产数据库，该怎么办？您可以为您的数据库连接接口提供一个假的实现——一个只假装是数据库并返回罐装数据的实现——现在您可以在不实际连接到数据库的情况下单独测试您的代码。你可以在我的文章[单元测试和为什么你应该做单元测试](https://medium.com/better-programming/unit-testing-and-why-you-should-be-doing-it-ab61407c53ce)中读到更多关于为什么你应该做单元测试的内容。

# 5.模式

最后，接口使得实现设计模式和做像[依赖注入](https://medium.com/better-programming/what-is-dependency-injection-b2671b1ea90a)这样的事情变得容易。大多数新的模式和实践——包括依赖注入框架——都是因为接口的强大和灵活性而得以实现的。开发模式和架构，如模型-视图-控制器(MVC)和模型-视图-视图模型(MVVM ),在设计界面时更容易实现和使用。

# 结论

如果你选择不拥抱接口，那么你就把自己锁在了新的有效的编程框架和技术之外。还不服气？我换个说法:所有酷小孩都在做界面，你想成为酷小孩群体的一员，对吧？

感谢阅读！
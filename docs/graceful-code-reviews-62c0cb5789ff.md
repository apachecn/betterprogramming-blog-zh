# 优雅代码评审的 33 个最佳实践

> 原文：<https://betterprogramming.pub/graceful-code-reviews-62c0cb5789ff>

## 对于代码作者和审阅者

![](img/436c59c897eb4d9c86b94b50503cb4c1.png)

克里斯蒂娜@ wocintechchat.com 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

每个软件开发人员都曾经编写或评审过代码，目的是获得或给出评审。最终将编写良好、测试良好的源代码提交到主开发分支，并享受看到他们的代码在最终产品中工作的快乐。无论是学校项目中的 GitHub 库，还是您最喜欢的开源项目的 pull 请求，或者是您自由职业项目的 bug 修复、功能代码，或者是您公司的项目/产品。

每周，我们开发人员都要编写和阅读代码。这就是为什么我们应该讨论一些最佳实践来使这个过程对读者、评审者和作者来说更容易。诸如此类的最佳实践不仅可以确保您能够顺利快速地审查和提交代码，还可以让其他开发人员和作者容易地阅读和理解拉请求的意图、它的上下文以及所做决策背后的历史和原因。

一般来说，编程是一种社会活动。我们不希望在与人交谈或解释某事时语无伦次或难以理解。类似地，对于我们的代码，我们将我们的想法和意图传达给其他开发人员以及 x 个月后的我们自己，那时我们已经完全忘记了“那个 bug/特性/修正”。我们的提交和我们写的代码通常会在将来被多次阅读。在许多情况下，花在阅读(多人阅读)上的时间比花在写作上的时间还要多。这就是为什么我们不仅有责任编写干净、简单、经过良好测试、易于理解的代码，而且有责任遵循一些实践来快速有效地评审代码。

通过整合其他工程师的反馈，代码评审有助于保持我们项目的质量。在这种情况下，除了作者之外，评审人员也有责任通过他们的评审反馈来帮助确保和鼓励主分支中更好的代码。此外，作者和评审者都在这个过程中学到了很多关于编写代码的有用的东西。这就是为什么我会建议花一些时间定期审查你的同事的拉请求以及你通常的编码。代码评审对于作者和评审者来说都是一个互相学习的机会，并利用这个机会来改进提交给代码库的代码。

代码评审员是忙碌的人，他们有一大堆其他的事情要做。因此，如果你能让代码评审对他们来说是一个简单快捷的过程，他们会很高兴的。因此，获得快速审查将大大缩短您的反馈周期，并帮助您更快地提交代码，并在此过程中获得大量有价值的反馈。

现在让我们来看看对双方——代码作者和评审者——来说的一些最佳实践。

# 对于代码作者

## **1。拉动请求应该容易理解**

这是显而易见的。如果编写的代码不够容易理解，那么它会让评审人员感到沮丧，大大增加您的反馈周期和提交时间，因为评审人员需要从他们的一天中抽出大量时间来评审它。

这就是为什么，要尽可能地添加更多的信息、背景、历史和背景。尝试降低复杂性，并使用编码最佳实践来确保评审者已经熟悉所使用的概念。添加拉请求后，使用您的审阅工具的 UI 来检查拉请求在审阅者看来是什么样子，并进行改进以使其更简单、更清晰。

*阅读更多:*阅读关于[干净代码](https://www.oreilly.com/library/view/clean-code-a/9780136083238/)的内容，了解一些可以实现的方法。

## **2。所有可移动的部分都应该很容易修改**

很有可能，你会在你代码的核心移动部分得到很多反馈。

因此，在编写代码时，尽量保持代码的模块化和抽象性，这样，如果需要的话，您可以很快地整合反馈，而不需要花费太多时间来重构整个拉取请求。

## **3。应该有很大的测试覆盖面**

始终确保您所更改或添加的代码具有非常高的测试覆盖率(接近 100%)。这将确保您和其他开发人员能够非常自信地构建或修改这些代码(这在大多数项目中经常发生)。

这有助于防止无意的代码中断。测试还增加了可靠性，并有助于暴露可能的漏洞。它们也有助于增加对被测代码的理解，有时甚至比文档和注释更好，因为它们展示了代码做了什么以及它可以用来做什么。

## **4。测试突变**

如果可能的话，使用一些工具，这样你的代码可以在提交前进行变异测试。

变异测试有助于测试你的测试质量。突变测试以小的方式修改代码，以检查这些修改是否没有通过某些测试。这连同测试覆盖将帮助您维护一个健壮的测试套件。

## **5。它应该使用语言和框架最佳实践**

当你阅读一些你经常使用的编程语言的代码时，你可以很容易地理解它的结构和习惯用法。这种熟悉程度有助于减少阅读代码时的认知超载，这样你的时间就可以更好地用于理解概念，而不是停留在语法上。

为了更好地理解这一点，举一个用你的母语和第二或第三语言阅读一篇文章的例子。大多数开源项目都定义了自己的风格指南和最佳实践。行业中更成熟的项目也是如此。语言也有形成该语言最佳实践的惯例和习惯用法。

您的评审人员和您可能已经看到了遵循这些风格指南的该编程语言的代码。这种一致性将注意力从代码块的*如何*的微小细节上移开，而将注意力放在代码块的*为什么*上。

它还有助于提高代码的可读性，从而使代码评审过程更快，反馈周期更短。使用通用词汇有助于人们阅读代码时将注意力集中在你所说的内容上，而不是你所说的方式。它帮助你获得更多有用的反馈，而不仅仅是语法上的反馈。

您可能不需要手动修复代码中的所有内容，您可以利用语言和 IDE 的 lint 检查器和修复程序来避免可以由工具完成的额外工作。Kotlin 的 KtLint 就是一个例子。无论您想使用哪种语言，都可以很容易地找到 lint 工具。

阅读更多: [Google 以开源项目的形式为大多数编程语言维护了一套风格指南。](https://google.github.io/styleguide/)

## **6。它应该回答问题“*为什么”*和“如何”**

我早些时候已经暗示过这一点。你的 pull 请求应该能够告诉评审者你在用你的代码做什么，你为什么要这么做，以及你是怎么做的。

这种上下文和背景是很重要的，这样评审者就可以知道从哪里开始，从而有效地评审你的代码。通常，*如何*包含在代码本身和注释中。

应该将 *what* 和 *why* 添加到拉请求描述中，这样，在查看代码之前，评审者可以查看描述，将他们的思想放在正确的轨道上，然后开始评审。更多的上下文意味着更快的审阅和更快的提交。

## **7。更好的描述**

什么是好的拉式请求描述？在我看来，你应该从一行摘要开始，以便第一眼参考。接下来应该更详细地描述你在做什么，为什么要这么做，任何历史工件，如错误链接，功能请求链接，相关电子邮件/文档，相关代码/讨论链接。

任何有助于提供更多背景信息的东西都应该添加进去。可选地，你也可以添加你的变更本身的工件，例如，UI 模拟，原型，UI 截图/视频，等等。不要让你的评论者理解你为什么要发出这篇评论。无论你的代码写得多好，从代码中理解目的和方法都是非常困难的。

更好的评审描述将帮助你的评审者查看功能，并提供有价值的快速反馈，这反过来帮助你更快地改进、迭代和提交。总是解释上下文，背景，并在你的评论描述中添加有用的链接。

描述性的描述也有助于减少审核的周转时间，因为您的审核者不需要在深入研究您的更改之前询问背景问题。上下文不仅对评审人员和其他开发人员有帮助，当作者在调试某些东西时，通过开发分支中的代码历史回顾评审时，它也是有用的。

## **8。更小的尺寸=更快的回顾**

正如我们已经注意到的，评审员审查你的代码的时间是有限的。因此，如果你发出一个巨大的请求，他们需要花大量的时间来检查所有的东西并提供反馈。

这可能需要几天到几周的时间，具体取决于审核者的日程安排。如果你把你的拉式请求分解成更小的部分，这不仅能让评审者在会议间隙快速浏览，还能提供快速的反馈。

他们会更容易理解一小块代码，并根据它在整个计划中的位置来构建上下文。这种更快的反馈有助于缩短提交时间。较小的拉动请求，不仅更容易看出零件在整个移动零件机器中的重要性，也更容易发现可能的错误。

评审者会感谢你努力分解拉取请求并为他们节省大量时间。这并不妨碍您在发送一个小的评审后，您可以根据评审中的拉取请求继续工作。

更小的 PRs 也意味着需要解决的合并冲突更少，需要重定基础的东西更少。这使得确保一切都经过测试和变异测试变得更加容易。较小的 PRs 也鼓励更多的模块化和抽象，这总是有益的。

这条规则也有例外，无法避免大的拉取请求，但对于其他情况，最好尝试并遵循这条经验法则。

## **9。提交审查前的自我审查**

IDE 中的代码看起来与基于 web 的审查 UI 不同，例如用于拉请求的 GitHub UI，并且有助于获得令人惊讶的见解。它有时也帮助你重新评估你的设计。

自我检讨有助于给你一个完全不同于 IDEs 的全新图景。它有助于您注意到任何明显的错误和需要改进的地方，从而为审阅者节省一些时间，缩短反馈周期并提供更多的审阅建议，从而增加您在此过程中的学习。

## 10。倾向于在拉式请求中讨论，而不是在直接消息中讨论

任何关于拉请求的讨论都是未来参考和开发人员的重要背景。如果它存在于直接信息中，将来很难找到这样的上下文。

此外，直接讨论“拉”请求，可以让其他人参与进来。它还鼓励思想和观点的自由流动，并在拉请求中产生更好的代码，同时每个人在这个过程中都学到更多。

## **11。简化，简化，简化**

模块化你的方法和类，这样每个方法只做一件事，每个类只封装一个职责。通过模块化重构嵌套。

所有这些都有助于促进重用和重构，这在产品和代码库增长时经常发生。它还有助于编写大量测试每一个功能块的测试，从而提高代码的测试覆盖率，提高产品的可靠性。

另外，*阅读*关于[单一责任原则](https://en.wikipedia.org/wiki/Single-responsibility_principle)。

## **12。代码的干燥度**

这可能看起来更多的是关于代码健康而不是代码评审请求本身，但是我想在这里提到它，因为它是相关的。不要复制粘贴预先存在的代码，不管你复制的逻辑有多小，最好尝试模块化以重用或使用现有的库。

这减少了维护工作，节省了宝贵的开发时间。如果您正在重用代码库中的另一个代码片段，请尝试消除重复并找出重构，以便这段逻辑可以在您的任务以及其他开发人员将来的任务中重用。

模块化有助于促进重用。如果您正在复制现有的逻辑并对其进行修改，请尝试对新的更改进行分支，这样评审人员就可以减少要评审的代码。这有助于他们将注意力集中在如何修改现有代码上，而不是回顾已经存在的代码行，检查内容是否被正确复制。

## **13。留下痕迹**

在可能存在信息缺口的地方添加有用的待办事项、日志和跟踪。这不仅有助于在发生异常的地方作为文本提示，还有助于加快调试速度。

在不寻常的事情发生的地方缺乏痕迹和线索会导致错误。

## **14。使用更好、更具描述性的名称**

名字很重要，它们传达了一个方法做什么，以及一个类的责任是什么。这是应该由名字而不是评论来传达的信息。

应在注释中添加额外的注释和细节。但是这个名字有时可以确保不需要查看评论，这就是它应该的样子。

## 15。离开复合清洁剂

每次你在做一段代码的时候，友好地提供帮助，尽可能地改进代码。可以通过删除死代码、陈旧的注释、使代码模块化、纠正任何 lint 警告等等。

代码从来都不是完美的，即使是世界上最好的程序员写的。我们需要不断改进它，这样它才不会变得不可维护、不可测试，最终被淘汰。

即使是世界上最美丽的公园也需要支持和维护。当作者付出额外的努力并通过添加测试或改进模块化等方式改进现有代码时，这总是一个令人愉快的惊喜。

我们编写的代码需要随着每一次迭代而改进，这样我们就不会到达当前系统变得不可用的地步，我们会觉得需要从头开始重写一切。

## **16。文档越多越好**

具有模块化、单一职责和描述性名称的代码应该足够容易理解。但是文档是添加更多上下文、细节和注释的好机会。

有些语言还支持将文档转换到公共文档网站。

比如 Javadoc。每当添加新 API、新方法或新类时，请添加文档。

## **17。运行现有的测试，以确保你的代码在提交审查之前不会破坏任何东西**

这里没有什么要解释的，总是要改进代码，但要确保不要破坏现有的功能。

良好的测试覆盖率有助于确保你对重构充满信心。

## 18。讨论并协调评审之外的重大变更和重大功能设计解决方案

在编写代码之前，花点时间检查一下代码的设计。在 Pull request 描述中提及代码的设计，并通过代码和注释展示出来。

使用你的软件架构知识，在编写代码之前讨论并找出解决问题的方法，不要浪费每个人大量的时间在毫无意义的代码讨论上。

缺乏预调整也可能导致某些东西被忽略，并进入代码库，而这本来可以通过事先讨论有问题的系统、做一些研究、提出替代解决方案并将其提交给同行审查和讨论来防止。

使用抽象和强制屏障来保持现有代码的可维护性，并进一步改进它。非正式讨论，结对编程，或者只是通过视频会议快速同步也可以很好地工作。

如果说你的拉请求的逻辑应该像一个故事或一首诗一样，解释你在做什么，为什么要做，并逐渐平稳地从上一个提交到下一个提交，这是不是太极端了？应该很容易理解单个提交占据了拼图的哪一部分，以及该提交如何适合作为拉请求的整个画面。

没有完美的代码，但有更好的代码，我们都应该努力达到更好的代码。

# **对于审核者**

1.  **在你的评论中要清晰:**试着解释你为什么要建议某事，而不是仅仅说，做这个。提供一些文档或最佳实践的链接会更有帮助。解释为什么而不是什么，因为从代码中显而易见的是为什么，而为什么是人们在查看代码时要寻找的。解释你的推理。
2.  不要要求解释，要求作者进行改进，更新描述或添加评论。
3.  **提出问题而不是假设无能:**在假设一种方法无用之前，试着提出问题和/或阅读文档来理解意图和上下文。尝试提供作者可以改进的可操作的反馈。总是试图证明你的评论，而不只是指挥。解释并讨论某种方法的利弊，以达成更有利的解决方案。
4.  礼貌并乐于助人:在你的反馈中不要严厉或消极，试着理解作者为什么使用了一种特殊的方法。根据需要提出尽可能多的问题，以提供有用的反馈，并了解特定方法背后的历史或背景。
5.  支持讨论和理解，而不是争论和负面的批评。
6.  不要阻止你的开发伙伴，及时反馈，帮助更快地获得代码。
7.  请通知并让拉式请求作者知道，如果您不能参加评审，您希望跳过评审。
8.  最好坚持事实，而不是开玩笑，使用俚语，缩写等。请注意，你的同事可能不理解特定地区或团体的术语。相似的短语有不同的解释方法。
9.  注意功能，并确保开发人员的意图被拉请求所解析。如果有需要，总是要求作者以特定的方式降低复杂度，简化代码。为他们提供一个可行的方法来帮助他们。找测试。为将来请求更多的信息、评论和文档。确保一致性，并确保遵循语言风格指南和框架最佳实践。
10.  不要花太多时间去挖掘难以理解的代码，如果代码太复杂，就要求作者通过去除嵌套、使用描述性名称、模块化等方式来降低复杂性。您的可操作反馈因情况而异。我们的目的是改进代码并取得进步。
11.  个人观点不重要，但事实、最佳实践、底层原则、软件设计的数据原则才重要。当存在模糊不清的地方，一条道路没有被很好地照亮，那么诚实的讨论来理解方法的利弊会有所帮助。在拉请求本身中使用文档或注释线程来记录对话和讨论是很有帮助的。当遇到困难时，征求另一个人的意见，他可能对此事有很好的见解。
12.  这可能是我的方法，你做的不同的事情可能对你更好。但是我在第一遍中查看整体变化，以理解上下文和整体设计。这些注释需要先放进去，因为它们需要方法上的重大改变，但是一般来说，最好在编写代码之前做出决定。评审描述有助于提供上下文，并且通常应该尽可能地描述性，这样评审者就不需要仅仅为了理解作者为什么发送拉请求而去挖掘。如果我需要询问高层设计，我在这里添加任何评论。如果没有，那么我会再检查一遍每一行，开始在其他内容上添加评论。首先，我会查看有重大更改的文件，然后进行测试，以了解正在测试的内容以及我们应该关注的边缘案例。
13.  总是称赞好的设计决策和改善代码健康的努力。代码评审不应该只关注错误，还应该关注赞赏和鼓励。
14.  如果拉取请求太大，请作者将变更分成较小的变更。
15.  **推回并请求稍后进行更改:**要么让他们在并行的拉请求中处理更改，要么创建一个 bug 来跟踪此事。即使每个人都有改进的意图，我们都忙于更重要的优先事项来完成重构，技术债务就会从裂缝中消失。在一个季度中安排一些时间关注科技股债务是一个好主意。理想情况下，重构应该更频繁、更有机地发生。

# 对所有人

因为我们都是远程工作，所以请注意时区，不要在工作时间之外 ping 你的同事。

我很想知道什么对你有效，我们作为开发人员可以改进什么。
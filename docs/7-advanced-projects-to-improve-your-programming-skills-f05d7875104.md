# 提高编程技能的 7 个高级项目

> 原文：<https://betterprogramming.pub/7-advanced-projects-to-improve-your-programming-skills-f05d7875104>

## 走出你的舒适区，提升你的技能

![](img/47954467237a56b05601951bd0660b80.png)

蒂姆·莫斯霍尔德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这些年来，软件开发世界发生了巨大的变化。它最初只是一个工程师专用的领域，但是现在每个人都可以相对容易地进入编程领域。这都要归功于大量的抽象，它们让人们专注于他们必须完成的任务，而不是从头开始设计一种方法。

当涉及到加速开发时间时，抽象是一个救命稻草，但是对于学习者来说也有一系列的缺点。从我的经验来看，真正理解一项复杂技术如何工作的最好方法是自己重建它。

这种方法极大地提高了你的基础知识，并能更好地使用你所使用的工具；你会更彻底地了解他们，包括他们的优点和缺点。

请注意，其中一些项目可能需要很长时间才能完成。这很正常，因为他们可能需要对这个话题做一些研究。它们没有特定的顺序。

# 给计算器编程

这似乎是一个简单的任务——只需对给定的表达式求值——但涉及的内容远不止这些。你如何用多个运算符或嵌套函数来求解一个表达式？计算器软件实际上非常类似于解释器或编译器，因为它实际上是一个数学解释器。该项目将带来一个旅程，包括:

*   学习[的标记化](https://en.wikipedia.org/wiki/Lexical_analysis#Tokenization)，也称为[词法分析](https://en.wikipedia.org/wiki/Lexical_analysis)。这也是编译的主要阶段之一，因此学习它可以为您提供一个更复杂项目(如编译器)的基础。
*   令牌求值是计算表达式结果的另一个关键方面，您将了解到这一点。它是以正确的顺序执行一连串操作并将一个操作的结果传递给下一个操作的过程。

如果你想继续这个项目，我建议你阅读我的指南，一步一步地编写一个计算器软件。

[](/programming-a-calculator-to-solve-complex-numerical-expressions-75d228f37ebf) [## 编写计算器程序来解决复杂的数字表达式

### 计算器与编译器和解释器有许多共同之处。通过代码示例和算法探索细节…

better 编程. pub](/programming-a-calculator-to-solve-complex-numerical-expressions-75d228f37ebf) 

# 云日志服务

近年来，有一种趋势是将一切都转变为云服务；想想[、SaaS](https://en.wikipedia.org/wiki/Software_as_a_service) 、 [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) 等等。日志即服务( [LaaS](https://en.wikipedia.org/wiki/Logging_as_a_service) )也不例外。它为开发人员提供了一个可管理的、可靠的日志系统，这对于大型复杂的应用程序来说是非常有益的。

构建云日志服务将涉及:

*   通过云服务提供商或使用自己的机器来设置和管理服务器。如果你要决定选择哪家云提供商，那么我建议你看看我的文章“ [7 家面向软件开发者的免费云托管服务提供商](/7-free-cloud-hosting-service-providers-for-software-developers-99ed433ccdc6)”。
*   处理 web 服务器和最终相关的框架。您可能还需要建立一个网站，为您的服务提供良好的日志可视化。
*   API 密钥和用户会话管理。您可能需要实现一个身份验证系统，或者学习使用现有的系统，如 Auth0 或 Firebase。
*   管理数据库以存储用户及其日志。
*   您可能需要考虑服务器的安全性。
*   这样的应用可以通过[微服务架构](https://en.wikipedia.org/wiki/Microservices)来实现，所以这是一个了解它们的好机会。

# 构建一个虚拟机

从头开始编写一个虚拟机可能看起来有点让人不知所措，但这是完全可行的。最简单的形式是，虚拟机是一种执行指令的处理器软件，就像物理 CPU 一样。

这个项目将提高你的低级技能，并帮助你度过:

*   使用原始字节。如今，由于库和框架，编程很少涉及原始二进制数据。
*   学习汇编语言。每个处理器都有自己的指令集，这取决于其内部架构。您可以为现有的汇编语言创建虚拟机，也可以创建自己的虚拟机。
*   语言分析和翻译成一个[可执行文件](https://en.wikipedia.org/wiki/Executable)，如果你计划实现你自己的汇编语言，因为你必须为它写一个特定的[汇编器](https://en.wikipedia.org/wiki/Assembly_language#Assembler)。
*   低级优化。学习计算机如何在引擎盖下工作会让你知道哪种方法在特定的情况下会表现得更好。

# 图像转换器和操作工具

从照片滤镜到图像识别软件，它们无处不在。这种工具可以简单到您喜欢的程度，也可以复杂到您喜欢的程度，但它们都有以下几个共同点:

*   你可以处理原始图像数据，通常是 RGB 字节。
*   你可以学习图像文件是如何工作的。此外，它还可以作为学习操作视频的起点。

这个领域非常广泛，包括各种工具。我以前写过一篇关于图像到 ASCII 转换器的文章；你可以在下面看看。

[](https://towardsdatascience.com/convert-pictures-to-ascii-art-ece89582d65b) [## 将图片转换为 ASCII 图片

### 关于图像如何工作以及如何将像素表示为 ASCII 字符的简要指南——包括代码示例。

towardsdatascience.com](https://towardsdatascience.com/convert-pictures-to-ascii-art-ece89582d65b) 

# 物理引擎

物理引擎是每个游戏或模拟的基础。虽然有许多很好的选择，如 [Unity](https://en.wikipedia.org/wiki/Unity_(game_engine)) 和[虚幻引擎](https://en.wikipedia.org/wiki/Unreal_Engine)，但这是一个很好的学习项目。它涵盖了大量的概念，其中一些与编程没有直接关系。

你可以把它变得简单或复杂。根据精度的不同，它可能包括物理学的不同方面。总之，在这次旅程中，您将大致了解:

*   当然是物理。你将不得不修改[经典力学](https://en.wikipedia.org/wiki/Classical_mechanics)并最终做一些关于该主题的研究，以便模拟空间中物理物体的运动和相互作用。你也可以实现其他更精确的理论和特性，比如温度、能量、磁力...
*   [解析几何](https://en.wikipedia.org/wiki/Analytic_geometry)，例如检测物体之间的碰撞。
*   [向量](https://en.wikipedia.org/wiki/Vector_(mathematics_and_physics))及其相关运算，因为它们是所有计算的基础。
*   例如，诸如[搜索技术](https://en.wikipedia.org/wiki/Search_algorithm)的算法可以有效地检测碰撞。
*   在屏幕上渲染模拟。很明显，您需要来自引擎的一些图形反馈。你还必须区分物理框架和图形框架(更多信息[在这里](https://docs.unity3d.com/Manual/ExecutionOrder.html))。

# 剪贴板同步服务

我们周围的每个软件产品都以这样或那样的方式依赖于同步；时钟需要同时显示；聊天应用程序需要与消息保持同步；数据备份服务需要保存最新的更改。

虽然有数不清的同步应用程序，但我建议构建一个服务，让本地网络上的多个设备上的[剪贴板](https://en.wikipedia.org/wiki/Clipboard_(computing))保持最新。此类软件不仅有用，而且是了解以下内容的绝佳机会:

*   套接字编程，因为您必须为同步设置一个套接字服务器，并让客户端连接到它。
*   设置服务器以运行同步服务。它可能是一台专用机器，也可能只是一个运行在您知道会一直运行的设备上的进程。
*   同步技术跟踪剪贴板历史，确定最后复制了哪些数据，以及[复制](https://en.wikipedia.org/wiki/Cut,_copy,_and_paste)的顺序。
*   您可以创建一个应用程序来可视化、编辑和清除剪贴板历史，并选择要粘贴的剪辑。
*   剪贴板通常由操作系统提供，并且可由程序通过专用 API 访问。你将不得不处理你的操作系统的剪贴板 API。对于 Windows，请查看[本指南](https://docs.microsoft.com/en-us/windows/win32/dataxchg/clipboard)；对于 Linux，[这个 Reddit 线程](https://www.reddit.com/r/cpp_questions/comments/7utqeu/copy_to_clipboard_on_linux/)包含了一些方法；对于 macOS 来说，[这个](https://developer.apple.com/documentation/appkit/nspasteboard)应该就行了。

# 笔记保存 Web 应用程序

如今，网络应用因为其多功能性而无处不在。其实不用安装也可以用；您可以从多个设备访问您的帐户，而不必担心同步数据。

笔记应用是一个经典的有用工具，它不需要太多的工程来构建。然而，这是一个很好的学习项目，因为它涵盖了许多技术方面，例如:

*   服务器设置，要么通过云提供商，要么通过管理自己的机器。正如我之前提到的云日志服务，[在这里](/7-free-cloud-hosting-service-providers-for-software-developers-99ed433ccdc6)你可以找到一些免费的云服务提供商，他们各有特色。
*   Web 技术，如框架和 web 服务器。
*   用户认证、会话管理和相关的安全性。
*   通过 JSON Web 令牌( [JWT](https://en.wikipedia.org/wiki/JSON_Web_Token) )或其他方式管理用户设置。
*   数据库管理是任何 web 应用程序的另一个基本方面。在这种情况下，您必须存储用户帐户及其注释。
*   通过会话进行实时数据同步。应用程序的前端必须不断地发出请求，以使界面与可能发生的变化保持同步。例如，共享笔记可以随时编辑，您需要不断刷新它们。

# 结论

为了在各方面都取得进步，让你不断接受新问题的挑战至关重要。做得更好的唯一方法，包括编程，是深入你目前不知道的领域，从头开始学习。

学习时最糟糕的方法之一是停留在你熟悉的东西上，而没有通过尝试新事物来提高。

> 没有人会因为做他一直在做的事情而变得更好。推动自己超越舒适区，实现你从未想过你能做到的。

承担这种项目需要在他们身上花费时间和精力。如果你决定开始一个，不要放弃，如果你一开始不知道如何进行:你会通过你的旅程了解一切。

如果您想添加任何东西或分享任何帮助您提高的项目，请在下面留下评论；我会很感激的。我希望你喜欢这篇文章。

感谢阅读！在我的文章《 [7 大项目加深你对计算机的理解](https://towardsdatascience.com/7-great-projects-to-deepen-your-understanding-of-computers-e20f7524277c)》中，我更深入地谈到了构建复杂项目的好处。建议你去看看。
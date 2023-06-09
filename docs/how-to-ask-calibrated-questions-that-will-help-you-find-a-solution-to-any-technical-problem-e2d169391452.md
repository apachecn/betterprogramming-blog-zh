# 如何提出有针对性的问题，帮助你找到任何技术问题的解决方案

> 原文：<https://betterprogramming.pub/how-to-ask-calibrated-questions-that-will-help-you-find-a-solution-to-any-technical-problem-e2d169391452>

## 为什么学习如何问正确的问题是开发人员最好的职业技能之一

![](img/16fef08f4bc38a8933b6a29597f2dd3a.png)

[亚历山大·波波夫](https://unsplash.com/@5tep5?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/@zackminott/likes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照。

编程很难。

不幸的是，在可预见的未来，我看不到一个开发人员可以自动变出复杂需求的解决方案，而无需咨询 Google 或花费大量时间不断搜索正确的方法。

关于任何给定编程语言及其潜在用途的知识库太大了，以至于无法立即记忆和适应。如果你能做到，那就恭喜你了。你是一个传奇和专家，我称赞你。

但对于我们这些普通人来说，我发现为了找到任何问题的解决方案，你必须首先知道要问什么问题——无论是为了反转链表的简单目的，还是为了运行与特定 API 的大规模实时集成，这需要对检索到的数据进行进一步的算法处理。

阿尔伯特·爱因斯坦甚至有[这样说](https://www.goodreads.com/quotes/8040091-if-i-had-an-hour-to-solve-a-problem-and)关于处理问题:

> “如果我有一个小时来解决一个问题，而我的生命取决于这个问题的解决方案，我会用前 55 分钟来确定要问的恰当问题，因为一旦我知道了恰当的问题，我就可以在不到 5 分钟的时间内解决这个问题。”

不用说，知道正确的问题是发展的必要条件。

这个概念极大地改变了我接近编程的方式，我知道我永远不会用这个理念去交换其他理念。

正是这一点让我能够在几天之内实现如此大规模的集成，完成我以前没有经历过的任务，并相对轻松有效地实现和学习比以前更多的东西。

真正的问题是:你如何提出正确的问题来弥合你和解决方案之间的差距？

# 花时间思考和分析你到底想要完成什么

你不想盲目地完成任何任务。这对你来说似乎是显而易见的，但更多的时候，我们往往在没有真正理解我们试图完成的任务的情况下就直接投入到事情中。

这可能会导致您做得更多、更少，或者完全偏离预期的实现。

有什么比询问你的经理或技术主管你想要完成的具体细节更好的方式来定义目标呢？

这都是为了试图理解你收到的需求。

我知道我经常觉得我接到的任务缺少细节——我感觉我并不是唯一有这种感觉的人。不要试图直接进入事情试图拼凑丢失的细节，只需要问并让任务分配器来填补这些空白。

一些示例问题可能看起来像:

*   您希望它位于用户界面的什么位置？
*   您打算如何将这一特殊功能融入我们当前的实现中？
*   有没有我应该努力实现的特定功能？
*   你如何想象用户与这个特定的组件进行交互？

同样，这只不过是收集需求。一旦你理解了需求，你就可以充分地为你打算如何完成所说的需求形成一个清晰的路径。

这样，你就有了一个明确的努力目标。

# 当需求显得太大时，把它分成几部分

这是整个软件开发中最重要的技能。

不过，不要相信我的话。

在谷歌的一次关于软件设计哲学的演讲中，斯坦福大学计算机科学教授约翰·奥特指出，问题分解是所有计算机科学中最重要的事情。

我完全同意。

当你被分配一项任务时，它往往是规模相当大的事情，使你很难准确地理解你应该从哪里开始，你应该朝什么方向前进。

这就是为什么你需要问自己如何将一个问题分解成更小、更容易实现的部分。当一项任务被分成更小的微任务时，你就可以以里程碑式的方式接近大任务的目标——每当你完成微任务时，就会获得令人愉快的多巴胺。

所以问这个问题:你怎么能把这个问题变小呢？你怎么能解剖这只野兽呢？

这是为了让你的生活更轻松，压力更小。

例如，当我着手构建我的第一个 Salesforce 和另一个 CRM 之间的实时集成时，我必须首先了解这个大项目中存在的每个不同的小项目。我需要采取什么步骤来使这变得更容易？这让我提出了以下一些问题:

*   API 和 JSON 响应是如何构造的，我如何发现这一点？*查看其他 CRM 的 API 文档，编写模拟 rest 标注，通过电子邮件将其他系统的响应发送给我。*
*   Salesforce 将如何接收在另一个 CRM 中所做的更改并相应地实时更新？ *Rest Web-Service 用 HTTP Post，公开暴露 Web 服务，将服务作为 webhook 添加到其他系统中。*
*   我将如何在 Salesforce 中显示和处理该信息，并使用 Salesforce 中的更改更新其他系统？创建一个类，作为解析 JSON 响应的对象，并在类中存储变量，利用对其他 CRM 的 GET、post 和 PATCH 请求，了解 POST 请求在 JSON 中的结构。
*   这样的例子不胜枚举。

当你看到我提出的问题和我在处理这个问题时得到的答案时，为了最大化我完成这个需求的效率，我需要关注的每一步就变得很明显了。

这就是为什么你需要通过问非常具体的问题来提取完成任务所需遵循的确切路径和计划。

# 持续进步的头号工具:“如何”问题

咄。

问“如何做”的问题是程序员在谷歌搜索引擎中提出的任何问题的基础，以便找到他们迫切的技术需求的答案。正常情况下，你可以发现自己登陆了一些类似 Stack Overflow 的编程论坛。

正如您从我上面的例子中看到的，很明显,“如何做”的问题不仅仅是用来回答技术问题，而是作为一种工具，从最初的需求深入到预期的解决方案。

但老实说，这并不像*总是*问一个非常直接的问题那么简单——尽管当你马上找到答案时会有一种奇妙的感觉。

如果问题太宽泛，你可能找不到你要找的东西。或者，如果你问的问题对于一个独特的问题来说过于具体，那么你可能也找不到答案。

不过，我们都知道这一点。我假设您知道如何正确地要求 Google 反转一个链表，实现一个特定的设计模式，或者如何在 JavaScript 中正确地创建一个调用服务器端代码的功能按钮。

这些是我们在编程中每天都会问的简单明了的问题，但是当问题变得太复杂，事情突然变得不明显时，该怎么办呢？

“如何”问题通过特定的面向目标的行动来推动发展，但是当事情变得困难时，您必须理解围绕这些复杂性的微妙艺术，因为直截了当的问题可能不总是输出直截了当的答案。

# 当“如何”这个问题无法回答时，运用元问题的力量

我相信你已经经历过这种情况很多次了:你不知道如何做某事，你去谷歌上查询。第一次没有运气，所以你一次又一次地尝试。你继续下去，直到你用尽了所有的选择，并以如此多的方式重组了你的问题，以至于你最终只是用手紧紧抓住你的头发毛囊，把你的头撞在键盘上，准备把它全部撕掉。你已经说服自己你被困住了。

我去过那里很多次，我确信我个人永远不会停止体验这种感觉，只要我保持我作为一个开发者的席位。

但也许你经历这些只是因为你问了错误的问题。

由于编程是一门需要相当多批判性思维的手艺，你必须明白，你不可能通过简单的搜索找到你想要的一切。

你需要问正确的问题。

围绕最明显和具体的“如何做”问题的问题。我喜欢把这些*元题叫做*。表现为围绕最初明显问题的战略批判性思维练习的产物的问题——问题中的问题。

这可能很难理解，但是请想一想:这与我之前提到的问题分解的思想非常一致。

假设您有一个在线表单的需求，该表单必须在提交到模板化的 PDF 上时自动生成信息，并将其作为文件存储到云数据库中。

您的第一反应可能是直接询问如何从在线表单中存储的信息生成 PDF。您很可能会得到关于如何渲染 PDF 的信息，但没有关于如何完成您的任务的独特性的具体信息。

在这种情况下，最好考虑一下围绕你所处的困境你需要哪些信息。

所以你应该问:

*   PDF 首先是如何生成和格式化的？这将告诉你应该如何构建 PDF 的模板并传入正确的标题。
*   我如何将表单中的特定信息传递到模板化的 PDF 中？这可能会让您想到让 PDF 成为一个从 URL 中的参数获取信息的网页，这样就可以对表单信息进行适当的查询。
*   如何将 PDF 页面保存为文件，而不重定向到当前表单页面？这可能会使您需要将从 PDF 生成的二进制数据存储到 blob(二进制大型对象)数据类型中，然后可以在表单保存时作为文件插入。

这是我在围绕 Salesforce 云开发的限制进行构建时实际上不得不面对的问题，正是我提出的这些问题最终让我找到了解决方案。

你需要知道的是，大多数编程问题不会提供一条直线来完成。你需要思考围绕特定问题的结构，并致力于回答那些基础问题，这些问题将允许你构建材料，以形成你需要实现的更大的解决方案。

这绝不是一个简单的任务，但它是有效的，并且让你更多地了解你正在使用的语言和系统。

# 总结的步骤(TL；博士)

每当你着手一项新任务时，记住问一些有针对性的问题的重要性。

正是这些类型的问题让你能够解构一个问题，使它更容易实现和理解。一旦你以这种方式思考，几乎任何给你的任务都不会对前进造成任何重大障碍。

*   通过收集交给您的需求的更多细节来定义任务的目标和目的。
*   通过询问你如何想象组成这个任务的微小结构的内在问题，将任务分解成微小的任务。
*   首先问一些直截了当的问题，以找到其他人以前面临的问题的答案。
*   当直截了当的问题不起作用时，思考围绕任务的一切可能的事情。这些是问题中的问题，它们与理解构成微任务的结构密切相关。
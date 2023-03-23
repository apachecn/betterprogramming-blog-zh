# 为什么 Java 如此完美

> 原文：<https://betterprogramming.pub/why-java-is-perfectly-alive-e3f25a576f95>

## 对“Java 为什么会消亡”的回应

![](img/f06927d91e7143615c40540d034cf787.png)

乔纳斯·雅各布森在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我写了这篇长文作为对文章“[为什么 Java 正在消亡](https://medium.com/better-programming/why-java-is-dying-b02b5fd44db9)”的回应我对这篇文章的[评论](https://ivankhodyrev.medium.com/im-sorry-komal-but-the-word-silly-is-used-in-the-wrong-context-to-the-wrong-object-2afc3855d72d)是最受欢迎的，我觉得我必须做一个完整的反对声明，这应该会恢复平衡。

对于作者的尝试，最好的简短回答是 Coder:76 的回答，它获得了数百次掌声:

> “过去 15 年来，Java 对于那些希望得到一些关注的编程博客来说已经奄奄一息了。”

很难不同意。

# “Java 快死了”这种说法有什么问题？

![](img/011097545078cdcf573cd81563cb69c9.png)

照片由 [Jonas Jacobsson](https://unsplash.com/@jonasjacobsson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

这篇题为“为什么 Java 正在消亡”的文章收到了 70 多条评论，其中大部分是批评性的，每条评论都有几十到几百次掌声。为什么那么多人反应那么消极？原因看起来很简单:这篇文章是以一种挑衅的方式写的，并且包含许多有争议的陈述，这些陈述对于使用 Java 的人来说是远离现实的。让我们来看看其中的一些。

> 例如，Spring 在幕后设置自动连接(bean 注入)是可以理解的，但是 Lombok 在应用程序上下文中处于什么位置，两者之间的消息传递是如何协调的

对于使用上述技术的人来说，这种说法似乎是错误的。Lombok 是编译时库，Spring 是运行时库。它们在应用程序生命周期的不同时间工作在不同的层次上，并不直接交互。作者问题“Lombok 在应用程序上下文中处于什么位置”的正确答案是“无处可去”

> “Java 的焦点似乎仍然是愚蠢的规则，这些规则规定类名应该是什么，它们应该在什么包中，以及变量应该是私有的还是受保护的。说真的，谁在乎呢？”

从事大型长期项目的人会在意。那些规则对他们来说并不愚蠢。

> “相反，‘我们都是成年人’是 Python 对语言中缺少访问说明符的正式回应。”

团队中的某个人假设其他人不是成年人是没有问题的——这是一个肤浅的想法。问题是，持续时间长、由大团队创建的大项目需要规则；否则，他们就会失败。一个大项目就像一个大城市:它需要一个架构基础，规划，关注点的分离，私人和公共区域。如果一个熟练的程序员将语言结构分为公共和私有，他们很可能会创建“街道”,以正确的方式引导他人，节省他们的时间，并将辅助基础设施隐藏在“地下”,以便没有人会在那里迷路。

在“为什么 Java 正在消亡”这篇文章中，有很多有争议的说法，但是我在这里的目标不是详细的分析。我想利用这个机会谈谈 Java 本身的现代状态。

多年来，Java 是编程语言中的首选，同时也是批评家们的首选。不是因为它不好，而是因为它是一件大事，如果你想看起来比你实际上更大，你必须说反对某件大事的话，并祈祷有人会注意到。从这个意义上说，Java 是一个很好的目标。但是现在呢？Java 还是一个大东西还是像有些人说的那样“奄奄一息”？让我们讨论最重要和最有争议的话题，以便弄清楚。

# 句法

![](img/dda9c34d0003800620809a74077da657.png)

图片由[哈利·法贝尔](https://pixabay.com/users/harryfabel-3680844/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1797917)从[皮克斯拜](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1797917)拍摄

Java 的语法通常受到最多的批评:“不简洁”、“不适合现代任务”、“样板文件太多”等等。对这些“论点”的唯一正确答案是展示代码。我不会在这里讨论特殊的句法特征；有许多详细的指南涵盖了 Java 语法的所有细微差别。相反，我选择了五个代码片段，只是为了让你们了解现代 Java 在不同实际任务中的表现。

您可能听说过“美好”的旧时光，那时一个简单的 Java 服务器需要几百行代码和配置才能运行。现在忘掉他们吧。

这段代码使用端口 80 上的 [Spark Java](http://sparkjava.com/) 启动一个简单的 web 服务器，该服务器带有 HTTP GET 方法和`/hello`上下文路径，该路径在请求时返回一个常量字符串。非常简单明了，不是吗？

“Java 不是为数据库操作而生的”，有人说。他们绝对是在开玩笑。

这段代码结合使用 [Querydsl](https://github.com/querydsl/querydsl) 和 [Spring Data](https://spring.io/projects/spring-data-jpa) 从 SQL 数据库中获取信息。一切看起来都很简单:方法`getShopOrdersByDate`返回特定商店在特定日期的订单，并带有额外的日期格式。有趣的部分是:这里没有 SQL，只有 Java 构造，这些构造后来在库中被翻译成安全的 SQL。这意味着查询本身是类型安全的，并且在编译时被可靠地检查，而不是在运行时被随机检查。此外，ide 可以像处理其他 Java 代码一样，帮助您完成自动补全，让您的生活更加轻松。许多数据库都支持开箱即用，比如 PostgreSQL、MySQL、Oracle，甚至 MongoDB。此外，如果您想交换它们，也不必更改查询代码。

没想过用 Java 做一个简单的数据分析？建议你试试。

这里没有额外的库，只有纯 Java。从庞大的文件中读取所有行，将它们拆分成单独的单词，过滤掉哑词以保持一切整洁，按首字母将单词分组，计算每组中有多少单词，创建结果组和计数的字符串表示，打印结果。可读性和可维护性都很好。当然，一切都是在并行线程中完成的，以获得开箱即用的适当加速，从而让您的新 16 核怪物证明其成本是合理的。在我装有 Java 15 的 4 核笔记本电脑上，对于一个 1.2 Gb 的充满随机单词的文本文件，这段代码平均执行时间为 6 秒。对于这样的文件大小和直接的非优化代码来说还不错。

“你不能用 Java 做深度学习”，他们说。不，你可以。

这个简单的例子显示了在学习阶段之前准备神经网络结构的阶段，使用了 [Dl4j](https://deeplearning4j.org/) 。那些在深度学习领域的人会很容易认出许多熟悉的单词。没什么特别的。是的，你可以用 Java 做深度学习和一般的机器学习。

龙目岛是爪哇旁边的一个岛屿。非常接近，你甚至可以把它们混淆。

你讨厌样板文件？然后用[龙目](https://projectlombok.org/)。这段代码展示了一个全功能的数据类，所有字段都有 getters、setters、equals、hashcode、toString，最后是`AllArgsConstructor`。当然，如果你愿意，你可以忽略其中的任何一个。

来自 Java 生态系统之外的人通常认为“Lombok 使 Java 不是 Java。”我不得不反对:Lombok 是 Java。它是现代 Java 生态系统的一部分，使用合法的 Java 机制来添加它的功能，帮助您编写几乎没有缺点的 Java 代码，并且被许多 Java 开发人员使用和喜爱。多少？最受欢迎的 Java IDEs 之一统计 Lombok 插件下载量为 1100 多万。很多。它是让 Java 变得更好的工具，所以从实际意义上来说，它是数百万使用它的人的 Java。尽管如此，Java 社区中仍有一部分人反对 Lombok，他们完全有权利这样做——如果你不喜欢 Lombok，没有人强迫你。

现在想象一下这些例子在其他语言中会是什么样子。你可能会发现用更少的字符来达到同样目的的语言。但是这些代码会像 Java 语言一样可靠、可读、可维护和快速吗？我不这么认为。

与语法相关的另一件重要事情是 IDE 支持。这不是一个抽象的理论问题，就像语言结构有多强大，或者开发人员需要编写多少符号来做一些事情。IDE 增加了一个实用层，将所有抽象的问题转换成这个问题:一个特定的任务需要开发人员花费多少时间。将设计良好的语言与现代 IDE 结合使用，开发人员可以比使用更强大或更简洁但 IDE 不太友好的语言更快地达到他们的目标。Java 是 IDE 支持最好的语言之一，这要归功于它的语法:它不太复杂，同时也没有太多的自由选择，所以 IDE 可以理解你当前工作的环境，并非常精确地预测你下一步要做什么。

最后要说的是，Java 的语法允许使用不同风格的编程。您的代码可以用面向对象的范例编写，其中对象相互交互；在过程范式中，命令性过程调用的序列改变全局状态；或者在函数式范例中，您可以组合并应用函数来实现您的目标。有时人们会区分更多的范例——例如，Java 非常适合面向方面或基于角色的范例。Java 在考虑任务的方式上给了你很大的灵活性，所以它已经做好了充分的准备来适应周期性发生的编程范式转变。

综上所述，Java 的语法没有问题。它相对简单、灵活且富于表现力。此外，它允许 ide 以多种方式有效地帮助开发人员，极大地提高他们的生产力。如果你很了解 Java，编写清晰的代码，并为你的任务使用正确的工具集，你的程序将会很漂亮、可维护、简洁。不要让人在这个话题上欺骗你。

# 担保

![](img/3fada18dc2a2db1f5f9c6eac4e85afb6.png)

Joshua Hoehne 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

与许多其他技术不同，Java 作为一种语言和平台，为您提供了许多值得信赖的保证。

Java 语言有 [Java 语言规范](https://docs.oracle.com/javase/specs/jls/se15/html/index.html)，它是 Java 代码构造应该如何工作以及应该如何使用的主要评判标准。为什么重要？因为您可以验证您正在做的事情，并以严格、可预测的方式解决问题或争议。对于没有规范的语言，你不能十分确定发生了什么:你可能会在手册、博客或语言创建者的推文中找到一些信息，但是在一种语言得到规范之前，所有这些信息都没有强大的基础来保证任何事情。

当然，规格可能有不同的质量或不同的详细程度，因此可能会遗漏一些东西。尽管如此，一门有规范的语言比一门没有规范的语言能给你更高的信心，让你相信你正在做正确的事情。Java 的规范非常深入和详细，几乎没有留下歧义的地方。

Java 版本对规范和公共 Java API 中的东西有很强的向后兼容性。这意味着，如果你使用 20 年前编写的 1.3 版公共 Java API 的代码，今天在 Java 15 上运行它，它就能正常工作。同时，如果你使用私有的 Java API，它由不应该被开发者直接使用的类和方法/字段组成，你可能会有麻烦。我认为这是一个相当公平的交易:如果你使用保证向后兼容的东西，你可以依赖它，但是如果你使用不打算使用的东西，请不要期望它们永远工作。

Java 是安全的——不是绝对安全的；相当实际。与许多其他语言相比，开发人员使用 Java 代码不太可能出错，从这个意义上说，Java 代码是安全的。它是安全的，因为 Java 代码不能直接访问操作系统或硬件，因此 Java 运行时可以安全地限制 Java 程序能做什么和不能做什么。

Java 代码是可移植的。这意味着您可以在一个平台上编译 Java 代码，并在任何实现了 Java 虚拟机的平台上运行它，而无需重新编译。“一次编写，随处运行”——这是古老的 Java 口号，25 年后的今天仍然适用。伴随着向后兼容性的广泛可移植性是一个非常强大的保证组合，这使得开发人员相信他们的努力和知识不会很快过时。当然，也有极限情况，由于硬件限制等不同原因，一些虚拟机只允许 Java 语言规范的子集。例如，您可以在 [8kB RAM 微控制器](https://en.wikipedia.org/wiki/PreonVM)上运行 Java 代码，但是您必须考虑一些限制。

Java 代码是可维护的。与 C++这样的语言相比，Java 的语法是一个很大的简化；它缺少 C++拥有的许多特性、定制和强大的构造。同时，与脚本语言相比，Java 有许多乍一看是多余的“仪式”。从这个意义上说，Java 试图在复杂性、功能和可读性之间保持平衡，以最大化长期代码可维护性。什么是可维护性？我把它描述为一个普通熟练的开发人员对一个现有的代码库(可能是一个旧的)进行修改所需要的时间，这导致了开发人员的目标，并且没有破坏其他任何东西。需要的时间越少，可维护性越强。

从这个意义上说，Java 是好的。一方面，它足够强大，可以表达开发人员需要的许多东西，但同时不像语言那样复杂，人们可以使用只有其创造者才能理解的极其强大的语言结构来创建美丽的、无法解决的迷宫。另一方面，与开发人员通常使用脚本语言相比，Java 迫使开发人员编写更详细的代码，使用更明确的内容，以增加可读性，便于理解时间流逝后发生的事情。

Java 很快。如果你试着研究这个主题，你可能会发现几十篇类似“Java 比 X 快”和“X 比 Java 快”的文章，它们包含了相互矛盾的陈述和结论。如果您尝试自己进行实验，您将很容易构建 Java 速度慢和 Java 速度快的例子——顺便说一下，您可以对其他语言使用同样的技巧。有一个[很好的评论](https://stackoverflow.com/a/2163570/3231055)关于为什么 Java 在过去被认为很慢。现在已经有点过时了。例如，最新 Java 运行时中的字符串处理比七年前好得多，而且我也不同意其他一些说法。但是总的结论是，随着每个版本的发布，Java 都得到了提高其性能的优化。还记得本文语法部分的第三个例子吗，这个巨大的 1.2 Gb 文件就是在这里处理的？在我的笔记本上，使用 Java 8 平均需要 10 秒钟，而使用 Java 15，相同配置只需要 6 秒钟。这是这种语言的开发者给我们的重要保证之一:Java 对于今天的许多任务来说已经足够快了，将来会更快。

关于担保，最后要说的重要一点是:它们已经实现了 25 年以上，没有理由在未来的岁月里不会实现。

# Java 实现现代语言特性的速度很慢

![](img/50dbd4118804f2c1fc958a262ce4f185.png)

[马卡里奥斯唐](https://unsplash.com/@makariostang?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

是的。总的来说慢是坏事吗？不。问问你自己。如果你正在骑自行车，偶尔看到前面有一堵墙，你更喜欢哪一种:加速还是减速？我打赌你和我选择的一样。

那么为什么 Java 采用特性的速度比其他一些语言慢呢？因为我们在上一节中讨论过担保。这些保证就是那堵墙，它迫使特性被仔细地讨论、过滤或以某种方式转换，在这里慢是比急更好的朋友。有一个如何对 Java 语言应用更改的正式流程，称为 [Java 社区流程](https://en.wikipedia.org/wiki/Java_Community_Process)。该过程的主要目的是验证提议的功能不会破坏保证。有时这并不是一个明显而快速的任务。

Java 语言的开发人员试图在创新和保证之间保持平衡，这是一个比“让我们现在就把这个很酷的特性添加到我们的语言中”更难的策略但从长远来看，它会带来更多的利润，因为担保意味着信任，这对于未来来说比一个很酷的功能集更有价值。Brian Goetz 有一个关于这个策略和 Java 整体哲学的精彩演讲；请看一下**。**

另外，值得一提的是当前的 Java 发布时间表。每六个月的九月和三月，会发布一个完全可以使用的 Java 新版本，并在接下来的六个月中逐步更新。每三年，就会有一个这样的版本成为长期支持(LTS)版本，并在接下来的三年中逐步更新。现在 Java 15 是最新的版本，Java 11 是当前的 LTS。下一个 LTS 版本将是 Java 17，计划在 2021 年 9 月发布。每个版本都可能包含许多“预览功能”，但不能保证在未来版本中的兼容性。他们的目标是让开发者有可能尝试有争议的创新，并留下反馈。如果一个特性没有被标记为预览版，这意味着它完全包含在平台中，并且拥有 Java 提供的所有保证。

# 生态系统

![](img/73084fd2f3ab34d3d0a01ed0ad15d302.png)

照片由[格雷格·拉科齐](https://unsplash.com/@grakozy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

有些人狭隘地理解*语言生态系统*，将其概念局限于程序员可以使用的一组库。我更愿意从更广的角度来思考生态系统，将其作为处理问题的工具。开发者用这种语言能解决的问题越多，它的生态系统就越广阔。你更喜欢哪种含义并不重要:Java 有一个巨大的生态系统。

以下是我之前为了解决问题而问自己的一系列问题:

*   我能在对 HTML、JS 和 CSS 一无所知的情况下用纯 Java 编写 web 应用程序吗？[是](https://vaadin.com/)。
*   我可以使用百万兆字节的堆和毫秒级的停止世界吗？[是](https://wiki.openjdk.java.net/display/zgc/Main)，[轻松](https://wiki.openjdk.java.net/display/shenandoah/Main)。
*   为了可移植性，我可以用纯 Java 处理图像和视频吗？[是](https://boofcv.org)。
*   我能用 Java 做深度学习吗？[是](https://djl.ai)，[请](https://deeplearning4j.org/)。
*   我可以用 Java 给我在黑色星期五买的机器人编程吗？[是](https://ev3dev-lang-java.github.io/#/)。
*   我能找到关于 Java 的随机愚蠢问题的答案吗？[是](https://stackoverflow.com/tags)。

这个列表绝对是个人的，但是我很确定在大多数情况下，当问题在编程环境中并且涉及到 Java 时，“是”的例子会压倒“否”的例子。

在 Java 生态系统的帮助下，您可以解决许多领域的许多问题，此外，通常您还有多种选择。如果你想了解 Java 生态系统有多大，看看这个[资源](https://github.com/akullpp/awesome-java)。

# 启动时间和内存占用

![](img/1a2214a4d23f5e5142ccf1df86c12d51.png)

比尔·牛津在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Java 代码被编译成称为 Java 字节码的中间形式，然后在称为 JVM 的运行时平台上执行。除了抱怨 Java 语法，批评者通常还会抱怨 JVM 的内存占用和启动时间。让我们更详细地讨论一下。

JVM 代表 Java virtual machine，意思是你的应用程序在一个沙箱中由一台虚拟计算机运行。这种方法有很多好处。开发人员不需要考虑运行应用程序的操作系统和硬件的细微差别。虚拟沙箱提供了更强的安全能力，因为它不允许应用程序直接与底层事物进行交互。虚拟环境位于您的应用程序之外，可以动态优化它，以提高不同情况下的性能，等等。

缺点是 JVM 需要额外的资源来运行，包括内存和处理器时间，而且它还有启动和预热时间。

在通常的用例中，标准的 Oracle HotSpot JVM 引入了数十或数百兆字节的额外内存，平均需要几秒钟的启动时间，这取决于应用程序。(JVM 本身通常启动不到一秒钟，但其他一些库由于其内部例程可以增加这个时间。)此外，在启动后的最初几秒钟，JVM 会消耗比平均水平更多的 CPU，因为它会识别和编译字节码的“热”部分，以优化它们未来的使用。

在大多数情况下，这些缺点对于许多类型的应用程序来说是合理的。但是有些情况下不是这样，你想以某种方式权衡利弊。那你该怎么办？你应该放弃 Java 而选择其他东西吗？通常不需要——只需要另一个适合您特定任务的运行时。

例如，考虑微服务领域。现代微服务应用通常需要最小的内存占用和启动时间，以便有效地填充像 Kubernetes 这样的容器编排器。为了满足这种需求，Java 的开发者已经创建了 [GraalVM](https://www.graalvm.org/) 。它允许开发人员从 Java 代码中创建本机映像，这将在几十毫秒的启动时间和仅兆字节的额外内存占用下运行。很多 Java web 框架针对微服务领域改编了 GraalVM:[quar kus](https://quarkus.io/)、 [Micronaut](https://micronaut.io/) 、 [Spring](https://spring.io/blog/2020/04/16/spring-tips-the-graalvm-native-image-builder-feature) 、 [Helidon](https://helidon.io/) 。

交易弊端？您失去了可移植性，构建的映像只能在 GraalVM 为其编译的平台上运行。但是对于微服务来说，这并不重要，因为你的应用很可能运行在预定义环境的容器中。你也可能面临一些其他的限制。反正当你听到 Java 不适合现代微服务需求的时候，只要记住:那个说法是假的。

Java 并不像许多批评家说的那样意味着过度使用内存和启动时间慢。内存使用和启动时间主要取决于最终运行应用程序所用的运行时以及它所使用的附加库。从这个意义上说，Java 生态系统根据您的需求为您提供了选择。

# Java 到底是干什么的？

![](img/e7103e8e4f909cd5bde673a107b1f276.png)

哈维·卡夫雷拉在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Java 可以用于你能想象到的一切:API 和 web 服务器、游戏和多媒体软件、UI 和 web 应用、物联网和机器人、AR 和 VR、机器学习和数据流、数据库和云原生微服务、大型企业系统和小型原型。
有排除条款吗？技术上没有，实际上有。Java 并不打算成为一种低级系统语言，所以用 Java 创建操作系统或硬件驱动程序的核心并不是一个好主意。从技术上讲，这是可能的，但是对于这些情况，有比 Java 更好的工具。

# 但是我们要为 Java 付费，对吧？

![](img/7be9e91a5db7b4e30722dc869fa39b97.png)

照片由 [Jp Valery](https://unsplash.com/@jpvalery?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

不，如果你使用免费的 Java 发行版，你就不必为 Java 付费。Java 是开源的，因此任何人都可以构建现成的 Java 发行版，并且有许多免费的预构建发行版，如 [OpenJDK](https://jdk.java.net/archive/) 、 [AdoptOpenJDK](https://adoptopenjdk.net/) 、 [AWS Coretto](https://aws.amazon.com/corretto/) 、 [Azul Zulu](https://www.azul.com/downloads/zulu-community/?package=jdk) 等。使用这些发行版是完全免费的，其中任何一个都很可能满足您的需求。如果你想了解更多这方面的内容，请参考[这篇文章](https://medium.com/@javachampions/java-is-still-free-2-0-0-6b9aa8d6d244)。

# Java 的未来

![](img/ad15a2e4feda91e4059f888a5b5dcb19.png)

[卡斯滕·沃思](https://unsplash.com/@karsten_wuerth?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

总结一切: **Java 还是个大东西**。

它的作用是成为许多领域的核心技术，平衡创新、能力和可维护性，以可持续地支持它所应用的项目。如果你喜欢尝试很酷的新语言想法，请选择另一种技术。但是，如果您看到某个特定的特性最终出现在了 Java 规范中，那么您可以肯定，它不是由随机事件或小的流行波动添加的，而是深入研究和大量设计工作的结果，以实现其他 Java 特性所具有的相同水平的保证。从这个意义上说，你可以信任 Java，不像市场上的许多其他技术。

如果你在问你应该学习哪种计算机语言作为你的第一或第二语言，试试 Java 吧。我相信 Java 会伴随我们很长一段时间。

我敢打赌，这篇文章遗漏了许多其他关于 Java 为什么会长寿的好论点，欢迎你在评论中分享。
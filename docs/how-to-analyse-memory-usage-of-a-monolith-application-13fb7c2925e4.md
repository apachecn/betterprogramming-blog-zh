# 分析单片应用程序的内存使用情况

> 原文：<https://betterprogramming.pub/how-to-analyse-memory-usage-of-a-monolith-application-13fb7c2925e4>

## 在没有任何内存管理知识的情况下，我将内存使用率提高了 45%

![](img/91e2f2664cab4bee65461edb4ce0d734.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Carlos Irineu da Costa](https://unsplash.com/@carlosirineu?utm_source=medium&utm_medium=referral) 拍摄的照片

在承担调查单片应用程序不断增长的内存使用量的任务之前，我从未真正深入研究过内存使用量。

我现在绝不是专家，但我在两周内学到了很多，在这篇文章中，我将分享这些知识。希望在本文结束时，您将有一个坚实的知识基础来研究未来应用程序中的内存问题，并分享我实现的减少内存使用的解决方案！

# 问题是

我工作的一个单片应用程序每天都会耗尽内存，导致进程在执行中结束，从而使底层数据处于不一致的状态。

此外，容器当前处理的任何内存不足的请求都将失败——向客户返回一个错误页面。在最糟糕的情况下，已经付款的客户不会自动收到他们所支付的款项，并且需要进行手动更正，这会占用宝贵的工程时间。

在一个月的时间里，**我们看到超过 175 个容器因内存不足而崩溃。**

![](img/cf7f3c5fd957662942b35d698b7cf014.png)

x 轴:时间，Y 轴:内存，粉色线:平均内存使用量，橙色线:峰值内存使用量。图片来源:作者

显而易见的短期解决方案是给容器更多的内存，但是如上图所示，这些内存很快就被消耗掉了。额外的 1GB 内存添加在该图的中间，因此它确实解决了几周的问题。但是，您可以看到橙色线，它显示内存使用的峰值，跳至使用所有额外的内存。

虽然平均内存使用量(粉色线)没有快速增加，但您可以看到它随着时间的推移逐渐增加。考虑到峰值和平均内存使用，我们可以清楚地看到应用程序的内存使用和管理存在问题。

# 如何调查内存使用情况

考虑到我们所看到的应用程序是一个整体，有一个几乎无穷无尽的可能发生内存问题的地方的列表。因此，我的第一步是列出我认为可能有问题的五大领域。不一定是五个，可能只有两三个，但我最初最多会定五个。如果您不确定从哪里开始，我会简单地从应用程序中用户使用最多的地方开始。

然后，我需要分析这五个区域的内存使用情况。

## 内存分析

您可以对应用程序进行多种形式的分析，另一种常见的形式是堆栈分析，它允许您查看被分析代码的完整调用堆栈，包括每个方法花费的时间，以及该方法被调用的次数。

然而，对于我们的问题，我们想使用内存分析。问题中的 monolith 是一个 Ruby on Rails 应用程序，所以我将使用 [Sam Saffron 的内存分析器](https://github.com/SamSaffron/memory_profiler)。然而，每种语言都会有分析器，比如 Java 的 [JProfiler](https://www.ej-technologies.com/products/jprofiler/overview.html) 。

分析的一个关键方面是您将分析器放在哪里，因为我们目前并不知道问题存在于哪里，我们希望从调用栈的尽可能高的位置开始分析。一旦我们对问题存在的地方有了更好的了解，我们就可以更有针对性地进行分析。例如，从分析一个控制器开始很有意义，当我们识别内存密集型类时，将它移到调用堆栈的下方。

我选择了五个最常用的控制器作为切入点。在 Ruby 的例子中，代码看起来像这样:

```
**class** *FooController* **def** handle
    MemoryProfiler.start

    *# the controller code goes here* report = MemoryProfiler.stop

    report.pretty_print(scale_bytes: **true**)
  **end
end**
```

在调用 start 之后和调用 stop 之前放置的任何代码都将进行内存分析。`pretty_print`方法只是将分析输出到控制台，或者您可以将一个文件作为参数传入，它将被写入控制台。

一旦建立了概要分析器，您现在就可以像用户一样浏览您的应用程序，以捕获概要分析器的输出。每个分析器的输出会有所不同，但是它们都会给你*大致*相同的信息。

## 剖析内存配置文件

当我分析 Rails 中的一个主库 ActiveSupport 时，可以在这里看到完整的输出。它太长了，无法包含在本文中，所以我将提取帮助我拼凑应用程序内存使用情况的关键部分。

```
Total allocated: 5.87 MB (55328 objects)
Total retained:  552.41 kB (4588 objects)
```

首先，我们有总结。这告诉我们到底分配了多少内存，保留了多少内存。我很清楚这两个术语的意思，但是为了确定，我做了一些研究。下面是每个单词的含义:

*   **已分配的**是已使用的内存总量，但只保留一小段时间。例如，如果您在一个方法中创建一个类的实例，这将占用一些内存，但是一旦处理了该请求，就可以从内存中删除该对象，从而释放内存。释放内存的过程被称为[垃圾收集](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science))，在大多数编程语言中通常会自动为您处理。如果你不熟悉垃圾收集是如何工作的，这是非常有趣的，所以我建议你在这之后仔细阅读。
*   **Retained** 是在被分析的代码被执行后保留的内存量，所以你可以把它看作长期内存。例如，类变量将被保存在内存中，直到它们被手动删除或应用程序重新启动。在像 Java 这样的语言中，这些是任何被定义为静态的字段。本质上，任何比单个请求存在时间更长的东西都可能保留在内存中。

每个分析器的措辞可能略有不同，但它们通常都有这个区别。在摘要下面，我们可以看到分配和保留内存的概述，分为几个类别。这些是重要的:

*   通过 gem (Ruby 的包管理机制)
*   按文件
*   按位置

这些细分对于突出显示要关注的区域特别有用，当我们浏览每一部分时，分析器的精确度会增加，当我们按位置查看时，会突出显示显式代码行。

## 缩小嫌疑人范围

作为我进行分析的结果，我发现了两个需要重点关注的区域，一个是纯粹使用摘要，另一个是寻找具有高分配内存或高保留内存的区域。输出可能是压倒性的，但是不要推迟，最初只关注概要分析器的高级摘要。

就像刑事调查一样，我将五个重点领域——这些领域中有 70 多个独特的请求——减少到两个看起来很麻烦的特定领域。以下是我发现的第一个问题领域的总结:

```
Memory report for POST
Total allocated: 470.05 MB
Total retained: 3.18 MBMemory report for GET
Total allocated: 320.89 MB
Total retained: 8.30 MB
```

上面的总结显示了当客户在应用程序(POST)上提交特定表单，然后是下一个页面时的内存配置文件。虽然保留的内存*相对*低，但应用程序不得不**分配近 800MB 来处理提交的表单。**

这个表单处于用户旅程的中间，所有之前的页面都有合理的内存使用，但是旅程中所有后续页面都有类似的行为——后续页面中最糟糕的 POST 请求分配了将近 700MB！总的来说，对于完成用户旅程所需的 17 个请求，分配了超过 4GB 的内存。

其次，在申请的另一部分，还有一个看起来更糟糕的表单提交:

```
Total allocated: 732.58 MB
Total retained:  247.87 MB
```

这只是显示了帖子，因为呈现下一页的 GET 在内存使用方面看起来是合理的，所以问题肯定出在表单提交的处理上。对于这个表单提交，**保留了将近 250MB 的内存。**

# 修复高内存使用率

我对我的发现非常兴奋，现在准备尝试解决问题。我们会一个一个来看。

为了解决这些问题，我回顾了针对所有内存密集型请求的分析器的更详细的方面——特别是，使用了突出显示使用大量内存的特定代码行的部分。

## 解决高分配内存问题

在我们的第一个问题中，我们看到高分配内存。进一步深入细节，我可以看到大量的内存使用是在序列化&反序列化 JSON。这让*对我来说有些*意义，因为当提交表单时，会生成大量数据，然后以 JSON 格式存储在 S3。

这些 JSON 文档的大小可能有几 MB，但是这并不能解释为什么分配的内存这么大。最初，我尝试了不同的 JSON 库，看看它们是否能产生更好的结果，但是它们的性能都差不多。

我决定停止包装 JSON 解析的类中的代码执行，并使用调试器进行调试。似乎没有什么不寻常的，老实说，我不太确定我在课堂上寻找什么。我有点沮丧，键入命令退出调试器，下一页没有呈现，而是再次命中调试器。再一次。再一次。总之，它在 POST 中 5 次命中调试器，在 GET 中 6 次命中调试器。

就像有时一样，我偶然发现了这个问题。在分配了大量内存的用户旅程页面中，JSON 文档被从 S3**检索，每个请求被解析 5 到 11 次。**

我最初的想法是，我们可以简单地缓存每个请求的数据，这样，当请求第一次检索数据时，数据就会被缓存，当请求执行完毕时，数据就会被清除。缓存更长时间会导致保留的内存显著增加，这只会使问题恶化。事实证明，已经有了一个基于请求的缓存，但是在某个时候它已经坏了——缓存键没有被正确设置，所以它永远不会命中缓存。不幸的是，没有充分覆盖缓存的测试，所以一些代码的重构在不知不觉中破坏了它。

![](img/d6f4249cacb30beb734a5287d2b2fa17.png)

图片来源:作者

修复很简单，这里比较了 master 上和我进行修复的分支上为这些页面分配的内存。图中的每个点都是一个请求，我必须省略 X 轴标签，因为它包含关于应用程序的敏感信息。但是，如您所见，所有请求的分配内存都要低得多——对于图中的倒数第二个请求，分配内存下降了 400MB 多一点。如果我合计所有这些请求的总分配内存，修复前后，**分配的内存减少了 1.6GB。**

我几乎被我的发现冲昏了头脑，所以一旦我的 PR 被批准，更改就被部署了，令我惊讶的是，峰值内存使用几乎没有变化。

这就是我对记忆的天真发挥作用的地方。虽然分配的内存很高，但事实证明，由于垃圾收集的工作方式，它会快速清除短期对象。

例如，在这种情况下，即使我们加载了一个很大的 JSON 字符串并在一个方法中反序列化它，该方法每次调用仍然只分配了大约 30MB。在请求检索数据 11 次的情况下，这将超过 330MB，但是每次调用仅使用 30MB，并且在下一次调用该方法时，在大多数情况下，30MB 将被释放回来。

尽管这些请求分配了大量内存，但它们实际上并没有一次性使用大量内存。实际上，对于一个分配了 100MB 内存的请求来说，它只在 1MB 内存使用量时达到峰值，这看起来要好得多。

这并不意味着高分配内存总是好的，减少分配内存总是一件好事。一次分配大量内存的简单操作示例是将一个 5GB 的文件完全读入内存。如果您的应用程序只有 2 GB 可用空间，操作每次都会失败。处理这个特殊例子的方法是一行一行地传输文件，因此在任何给定的时间点，内存中只能保存文件的一行——可能只有几 KB。

虽然修复缓存对我们减少峰值内存使用的目标没有帮助，但它确实很好地改善了响应时间(大约 300-500 毫秒，取决于页面)，因为从 S3 检索数据和解析 JSON 都是相对昂贵的操作。

## 解决高度记忆问题

我们强调的第二个问题是:当提交不同的表单时，高记忆。一条非常有用的额外信息是，在后续的表单提交中，保留的内存只有 50KB 左右，而不是几乎 250MB。

在诊断内存使用情况时，请确保至少分析两次请求，因为第一次请求缓存一些对象，而在后续请求中使用缓存并不罕见。但是，请确保不要在两次请求之间重启应用程序，否则它将在第二次请求时重新生成内存中的内容。

我又一次深入剖析了档案报告中更详细的部分，这次我看到大量的内存被 [CSV 库](https://ruby-doc.org/stdlib-2.6.1/libdoc/csv/rdoc/CSV.html)保留了。在搜索 CSV 库的用法时，我看到了这段代码:

```
@csv_cache = Hash.new **do** |*cache*, *path*|
  *cache*[*path*.to_s] = CSV.read(*path*, headers: :first_row)
**end

def self**.csv_lookup(*path*, *lookup*)
  @csv_cache[*path*.to_s].find { |*row*| lookup_match?(*row*, *lookup*) }
**end**
```

对于不熟悉 Ruby 的人来说，第一行创建了一个类变量，因此它将在请求之间保留。缓存本质上是内存中的键值存储，如果为 CSV 提供的路径已经存在于缓存中，将返回一个行数组，否则将从磁盘加载文件并插入缓存中。

这解释了为什么后续请求不会保留尽可能多的内存:第一个请求生成缓存，然后未来的请求只是从缓存中查找，可能是为了加速第一个请求之后的所有请求。

我的假设是删除这个缓存将节省大量内存——特别是考虑到我们在每个容器上的 web 服务器中运行 4 个 worker，并且缓存将在每个 worker 上生成。此外，这是在单个请求之后发生的——还有其他请求组合可以缓存更多文件，从而获得更多保留的内存。

为了测试这个假设，我稍微修改了一下代码:

```
**def self**.csv_lookup(*path*, *lookup*)
  CSV.read(*path*, headers: :first_row).find { |*row*| lookup_match?(*row*, *lookup*) }
**end**
```

不再将 CSV 文件的内容存储在内存中，而是在每次请求时读取它们。这导致了保留内存的以下改进:

```
Total allocated: 971.44 MB
Total retained:  26.83 MB
```

**通过一行代码的修改，我能够将保留的内存减少 90%左右。**然而，就像编程中的大多数事情一样，这是一种权衡，以牺牲更多分配的内存为代价。幸运的是，正如我们上面所讨论的，我们可以通过简单地逐行流式传输文件内容(在 Ruby 中为`CSV.foreach`)来缓解分配内存的增加，我们最终得到了以下内存配置文件:

```
Total allocated: 737.40 MB
Total retained:  26.97 MB
```

现在，我们保留的内存减少了 90%,分配的内存与以前几乎相同。以下是生产中内存使用的结果:

![](img/9533ea98aa319ec18fda5a748108ef02.png)

x 轴:时间，Y 轴:内存，粉色线:平均内存使用量，橙色线:峰值内存使用量。图片来源:作者

在内存使用方面，我们看到峰值使用从 6.4GB 下降到 3.4GB。这个简单的变化，使用相对简单的分析技术，一旦您习惯了它们，**导致整个应用程序的峰值内存使用减少了 45%。**

还有一个权衡需要考虑:速度。因为我们不再从缓存中检索，所以请求处理稍微慢了一些。在投入生产之前，我们在试运行阶段测试了这一变化，结果发现这一变化并没有*慢得多——平均来说，它增加了大约 200 毫秒，这是一个完全可以接受的折衷。时间的增加会有所不同，这取决于在找到它所寻找的内容之前它必须读取多少文件，但是即使在推向生产时，我们也没有看到处理时间的显著增加。*

实际上有一个更好的解决方案值得一提，它既能降低应用程序内存，又能产生快速查找，那就是将数据加载到类似 [Redis](https://redis.io/) 的东西中。然而，由于一些架构的复杂性，这不是一个快速实现的变化。因此，上面的解决方案暂时被接受，我认为这是一个非常合理的权衡。

关于内存管理或分析，您有什么问题或建议吗？

```
**Want to Connect?**I run a free newsletter providing fortnightly technical book recommendations, including my key takeaways from the books. Interested? [Sign up here!](https://subscribe.technicalbookclub.com/?utm_source=medium&utm_medium=article&utm_campaign=memoryprofilingbeginner)
```
# 使用 AWS 和 Hyperscan 匹配 100GB 文本上的正则表达式

> 原文：<https://betterprogramming.pub/using-aws-and-hyperscan-to-match-regular-expressions-on-100gb-of-text-4d87a62141ee>

## 受到对冲基金如何处理信用卡交易数据的启发

![](img/1d9f90b3e22f67f127e36cddeca8a4ae.png)

帕特里克·托马索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这是一个系列的第二篇文章，介绍如何使用 [Meadowrun](https://meadowrun.io/) 在大型文本数据集上快速运行正则表达式，使用英语维基百科作为我们的示例数据集。如果您想继续阅读第二篇文章，您需要我们在第一篇文章中提供的简化的文章摘录。或者，翻译本文中的代码来处理任何数据格式的数据集应该很简单。

## 背景和动机

这个系列的灵感来自对冲基金过去解析信用卡交易数据的项目。为了简化起见，我们将查看每笔交易的描述字段，看它是否与一家可交易的上市公司匹配，将每家公司所有交易的金额加起来，并使用它来尝试预测每家公司的收入。

要使这些收入预测准确，有很多挑战。我们在这篇文章中关注的问题是，由于某种原因(这一点 [patio11](https://bam.kalzumeus.com/archive/) 可能会深入解释)，信用卡交易的描述字段对我们来说完全是乱码。对于像麦当劳这样的公司，我们会看到像`mcdonalds`、`mcdonald's`、`mcdonalds`、`mcdonald s`、`mcd`这样的变体，甚至还有像`mcdnalds`这样的拼写错误和错别字。我们的解决方案是创建正则表达式，涵盖我们感兴趣的每家公司的所有品牌的所有常见变化。

这个数据集大约有 1tb 未压缩，我们有数百个正则表达式，这意味着我们需要两个主要的基础设施:一个真正快速的正则表达式库和一个分布式计算引擎。对于正则表达式，我们使用 Hyperscan，我们将在这里介绍。我们的分布式计算引擎还没有公开，但是我们将引入 Meadowrun，它以类似的方式工作。

显然，我们使用的信用卡数据集也不能公开获得，所以我们将使用英语维基百科作为替身(大约 67 GB 未压缩)，因为本文的目标是浏览该分析的工程方面。

## 跟上速度

如果您没有遵循本系列的第一篇文章中的[，那么只要您安装了](https://medium.com/@meadowrun/use-aws-to-unzip-all-of-wikipedia-in-10-minutes-c419f1b6972f) [smart_open](https://github.com/RaRe-Technologies/smart_open) 和 Meadowrun，您应该能够遵循这篇文章并使用您自己的数据集。smart_open 是一个令人惊叹的库，它允许您打开 S3(和其他云对象存储)中的对象，就像它们是您的文件系统上的文件一样，Meadowrun 使您可以轻松地在云上运行 Python 代码。

```
poetry add smart_open[s3]poetry add meadowrun
poetry run meadowrun-manage-ec2 install
```

我们假设您在这里使用的是诗歌，但是也可以随意使用任何环境管理器。我们还假设您已经配置了 AWS CLI。([参见文档](https://docs.meadowrun.io/en/stable/)了解更多上下文，以及如何将 Meadowrun 与 Azure、pip 和/或 conda 一起使用。)

## 一些现实的数据

我们将使用由本系列第一篇文章中的代码生成的英文维基百科的简化摘录。作为一个快速概览，它产生了 222 个文件，如 S3://Wikipedia-meadow run-demo/extracted-200000 . tar . gz，这是一个 tar.gz 文件，包含第 200，000 到第 299，999 篇维基百科文章。文件名是文章的标题，每个文件的内容是相应文章的文本。我们需要一个小函数来读取这些 tar 文件中的一个， [iterate_extract](https://gist.github.com/hrichardlee/4be2881a66faaee24f122eeaccf0b2c0#file-read_articles_extract-py) 。

对于我们的正则表达式，我们将做一个我上面描述的简化版本，只取 S&P500 的公司名称，当然，这是从维基百科得到的。(如果你很好奇，这是 2022-06-20 索引中的第 1379418 篇文章，所以你也可以在 S3://Wikipedia-meadow run-demo/extracted-1300000 . tar . gz 中找到它。)我用了一点力气将它放入一个名为 [companies.txt](https://gist.github.com/hrichardlee/4be2881a66faaee24f122eeaccf0b2c0?file=companies.txt) 的文件中，前几行看起来像是:

```
3M
A. O. Smith
Abbott
```

我们将用`aws s3 cp companies.txt s3://wikipedia-meadowrun-demo/companies.txt`将它上传到 S3，并用这段代码将它转换成一个简单的正则表达式:

这将为我们提供一个类似于`3M|A.O. Smith|Abbott|...`的正则表达式，让我们查找这些公司名称的任何出现。

## 正则表达式引擎:re vs re2 vs Hyperscan

> 有些人，当遇到问题时，会想“我知道，我会用正则表达式。”现在他们有两个问题。([杰米·扎温斯基](http://regex.info/blog/2006-09-15/247)

如果你没有在正则表达式领域花太多时间，你可能会从 [re 标准库](https://docs.python.org/3/library/re.html)开始——毕竟 Python 自带电池。让我们看看这是怎么做到的。我们将使用 Meadowrun 的`run_function`直接在 EC2 上运行一些探索性代码:

`scan_re`将从指定的. tar.gz 文件中提取前 100 篇文章，并对这些文章运行我们公司名称的 regex，告诉我们它每秒能够处理多少字节。

`scan_re_ec2`使用 Meadowrun 在 EC2 实例上运行`scan_re`——这里我们要求它启动一个至少有 1 个 CPU 和 2 GB 内存的 EC2 实例，并且我们可以接受高达 80%的逐出机会(即中断)。我们可以只在本地运行`scan_re`，但这实际上会更快，因为从 S3 下载数据从 EC2 比通过互联网要快得多。换句话说，我们将代码发送给数据，而不是相反。

运行这个程序会给我们带来:

```
Scanned 1,781,196 bytes in 10.38 seconds (171,554 B/s)
```

因此，对于这个正则表达式，我们得到了大约 170 KB/s。我们有大约 67 GB，所以在许多 EC2 实例上分布它仍然是缓慢和昂贵的。

让我们试试 [re2](https://github.com/google/re2) ，这是一个由 Google [构建的正则表达式引擎，主要目标是](https://github.com/google/re2/wiki/WhyRE2)用线性时间搜索字符串中的任何正则表达式。对于上下文，python 的内置 re 库使用回溯方法，这可能需要[的指数时间来搜索一个字符串](https://www.regular-expressions.info/catastrophic.html)。re2 使用了一种[汤普森 NFA 方法](https://swtch.com/~rsc/regexp/regexp1.html)，这种方法可以保证线性时间搜索，但是提供的功能较少。

PyPI 上的官方 python 包是 google-re2，但是 pyre2 也为 Windows 和 Mac 提供了很好的预编译版本(除了 Linux 之外):

```
poetry add pyre2
```

pyre2 被设计为一个直接替换，所以我们可以在最后一个代码片段中用`import re2 as re`替换`import re`，然后重新运行，看看 re2 的性能如何。请注意，Meadowrun 会自动同步对远程机器上的代码和环境的更改，我们不必担心手动重建虚拟环境或包含新 re2 库的容器映像。

重新运行 re2 和 10，000 篇文章，我们得到:

```
Scanned 190,766,485 bytes in 6.94 seconds (27,479,020 B/s)
```

大幅加速至 27 MB/s！

我们的最后一站是 [Hyperscan](https://github.com/intel/hyperscan) ，这是一个正则表达式引擎，最初由一家名为 Sensory Networks 的初创公司创建，旨在进行深度数据包检查，该公司于 2013 年被英特尔收购。Hyperscan 有很多非常酷的部分——有一个维护者 Geoff Langdale 的很好的概述，这篇文章会更深入。我只强调我最喜欢的一个，它广泛使用了 [SIMD 指令来搜索字符串](https://branchfree.org/2018/05/30/smh-the-swiss-army-chainsaw-of-shuffle-based-matching-sequences/)。

多亏了 [python-hyperscan](https://python-hyperscan.readthedocs.io/en/latest/) ，pypi 中有一个带有 python 绑定的编译版 Hyperscan。python-hyperscan 只有为 Linux 预先构建的轮子，但这没关系，因为 Meadowrun 为我们创建的 EC2 实例正在运行 Linux。如果我们在 Linux 上，我们甚至可以告诉 poem 只安装 Hyperscan，因为在 Windows 或 Mac 上安装它可能会失败:

```
poetry add hyperscan --platform linux
```

(Pip requirements.txt 文件也支持“[环境标记](https://stackoverflow.com/questions/16011379/operating-system-specific-requirements-with-pip)”，这允许您完成同样的事情。)

Hyperscan 的 API 不是 re2 的直接替代品，所以我们需要调整我们的代码:

`scan_hyperscan`展示了 python-hyperscan API 的一个非常基本的用法，而`scan_hyperscan_ec2`正在做同样的事情，使用 Meadowrun 在 EC2 上运行它。运行这个程序会给我们带来:

```
Scanned 190,766,485 bytes in 2.74 seconds (69,679,969 B/s)
```

这是在 re2 基础上的又一次坚实的改进。

## 把所有的放在一起

现在我们可以用 Meadowrun 的`run_map`在整个维基百科上运行这个:

`run_map`和 python 内置的 [map](https://docs.python.org/3/library/functions.html#map) 函数语义相似。如果你以前没看过，`map(f, xs)`大致相当于`[f(x) for x in xs]`。`run_map`和`map`做着同样的事情，但是在云上并行进行。因此，我们像以前一样为每个任务请求相同的 CPU/内存，并且我们要求 Meadowrun 启动足够的 EC2 实例，以便我们可以并行运行 64 个任务。每个任务将在不同的提取文件上运行`scan_hyperscan`,尽管我们实际上检查了数据集两次，以综合地使数据集变得更大。

Meadowrun 选择的确切实例类型将因现货实例可用性和实时定价而异，但在一个示例中，run Meadowrun 打印出:

```
Launched 1 new instance(s) (total $0.6221/hr) for the remaining 64 workers:
    ec2-18-117-89-205.us-east-2.compute.amazonaws.com: c5a.16xlarge (64 CPU/128.0 GB), spot ($0.6221/hr, 2.5% chance of interruption), will run 64 job/worker
```

加倍后的维基百科数据集大约有 135GB，在标准普尔 500 搜索一个公司名称大约需要 5 分钟，只需花费我们 5 美分！

## 结束语

*   Hyperscan 使用起来可能有点烦人，因为它有一个不同于 re 的 API，并且没有任何针对 Windows 或 Mac 的预编译版本(可以针对这些平台进行编译，只是需要一点工作)。在这些非常不科学的基准测试中，它“只”比 re2 快 2.5 倍，但根据我的经验，这是值得的，因为随着正则表达式变得越来越大和复杂，与 re2 相比，性能差距也越来越大。这真是计算机科学理论和工程学的奇迹。
*   Meadowrun 使得在 EC2(或 Azure)中使用真正强大的机器来处理数据变得很容易。显然，对于这种精确的工作负载，使用 Google 或 Wikipedia 自己的搜索功能会更快，但是我们在这里展示的方法可以用于任何具有任意复杂正则表达式的大型文本数据集。对于任何尚未被谷歌或其他科技巨头索引的东西，我认为没有比这更好的工具组合了。
*   这个系列的完整代码是[这里是](https://gist.github.com/hrichardlee/4be2881a66faaee24f122eeaccf0b2c0)，以防你想把它用作模板。

```
*To stay updated on Meadowrun, star us on* [*Github*](https://github.com/meadowdata/meadowrun) *or follow us on* [*Twitter*](https://twitter.com/kurt2001?s=21&t=66yV7Xy4agKRFj4dOaLLew)*!*
```
# 通过 Swift 任务组管理并发性

> 原文：<https://betterprogramming.pub/managing-concurrency-with-swift-task-groups-703ef993eb16>

## 管理多线程 iOS 应用的机制

![](img/bf9e28769f8c595401b9d8b10d42fe59.png)

资料来源:undraw.co

上周我[发表了一篇文章](/handling-core-image-filter-processing-with-concurrency-in-swift-a016396c3a07)关于在现实世界中使用并发。这是我想在本文中继续讨论的一个主题，希望了解您在开发需要管理多个 Swift 任务组的应用程序时的选择。

# 案情摘要

我的客户希望使用图像实现一些基本的运动检测。他们想在安全环境下从静态摄像机拍摄的两张照片中寻找变化。

理论上，如果我们使用前面开发的代码，我们可以比较这些图像，并返回它们有多大不同的百分比。假设基线是一个静态图像，任何重大变化都可能被解释为运动，所以入侵者。

但是等等——事情比这复杂得多。考虑一下，静态图像可以是内部位置，也可以是外部位置。假设是在室外随着天的变化，光线的变化，你会得到不同的结果。

随着季节的变化，光线也在变化，不同的结果。有些场景会比其他场景更有效。一扇黑色的门，装在黑色的框架里，被一个穿黑色衣服的入侵者偶然发现——这是最糟糕的情况。最后，整个过程需要很快——每个周期不到一秒。理想情况下，处理多个图像对需要一秒钟。

# 密码

好，这就是我要开始的。我得到了两段代码，一段将我的图像转换成一些我可以处理的数据，另一段处理这些数据。看起来像这样的方法:

我在这里使用了一个`Actor`,因为在我之前的文章中，我运行了多个任务来处理同一个图像——我在这里不会这样做，以保持事情的重点，但你可以稍后尝试进行修改。我用这些在[这篇论文](/handling-core-image-filter-processing-with-concurrency-in-swift-a016396c3a07)——最好的时间是 1.98 秒。等等，那只是一张照片，现在我有两张了。这太慢了。

我需要使用更少的数据来得出答案，所以我引入了图像过滤器，这是一个低级别的 API，可以快速对我的图像进行像素化，用它来预处理图像，并大大减少我需要采样的像素数量。然后，我用一个步幅跳过呈现的数据。

# 巴别塔中的任务组

bon——我想，我已经有了三种主要成分——我现在需要做的就是想出如何混合它们。我整理的初稿是这样的。

第一次尝试还可以，尽管我确信我可以做得更好——主要是，我不太喜欢巴别塔的结构。我不喜欢它，因为它看起来不可扩展。从时间上看，大约需要 0.82 秒。

方法`phase1`调用例程过滤数据，阶段 2 将图像转换为数据&阶段 3 进行分析。这些方法将调用已经列出的`t4`、`t6`和`t8`例程。结构`phase1`、`phase2`和`phase3`几乎完全相同。

这里是`phase1`。它们都是指任务组。

# 具有单一订阅的任务组

我想到，也许我可以使用基于订阅的解决方案来获得更好的结构，所以我尝试了一下。如果你忽略了这样一个事实，它读起来会更好，那就是开始的时候有点扭扭捏捏。

从时间上来说，它花了 0.79 秒，这很好，因为它表明构建代码并没有对速度产生太大的影响，但却使它更具可读性/可伸缩性。

# 具有多个订阅的任务组

在第一稿中，我建立了一个订阅，并根据我收到的数据决定下一步发送到哪里。但是我有点担心这种安排，同样是可伸缩性。

所以我设置了多个订阅，每个任务组一个。本次修订使用了两组`subject1`和`subject2`。时间方面，我仍然是 0.88 秒。

# 任务、任务组和多个订阅

现在，虽然这有点悬而未决，但在这种情况下，我不需要等待`image1`和`image2`完成两幅图像的像素化过滤器，就可以建立数据集。

我可以独立完成。同时，我也可以独立运行像素化图像的分析。理论上，我也不需要一起做分析系综，尽管我需要在最后将两个数字放在一起——所以这里有一个草稿，最初只使用任务，最后一步有一组用于分析。

与此同时，我们的计时越来越好，0.56 秒，尽管我必须承认我修改了 do filter 方法，将`CIContext`的创建移到了函数之外，我认为这是一个非常慢的过程。

# 使用通知的任务和任务组

当然，有一种机制比订阅历史更悠久，也更久经考验。

这是使用通知构建的最后一个订阅解决方案—它花费了 0.90 秒，这是可以的。我没有试图加快它，阅读警告。

**一个警告**:通知似乎有一个与队列相关的元素——又名`DispatchQueues`——如果你打算使用 async/await 模式，你被告知要像 plage 一样避免它。所以我不确定这个解决方案最终是否有意义，只有当你讨厌联合订阅时才小心使用。

## 使用属性观察器

现在我尝试用这些来实现一些东西。你知道，`willSet` / `didSet`人群。但是我没有成功，所以很明显，没有代码可以发布。

# 最后的话

Bon，使用订阅的最终草案最有意义，它是最快的，最灵活的，并且最好地利用了任务/任务组的混合。

所有这些使我想到了本文的结尾。我希望你喜欢读它，就像我喜欢写它一样。
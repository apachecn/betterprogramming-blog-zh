# Jetpack 撰写中的双标题 LazyColumn

> 原文：<https://betterprogramming.pub/double-header-lazycolumn-in-jetpack-compose-5cbbcece75ec>

## 创建 API 的缺失部分

![](img/fe23c5ac11ed0432d46dc2b795ab1082.png)

汉斯·雷尼尔斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当你听到“双头什么的”时，你可能会认为它是古希腊神话中的怪物。

你几乎是正确的，它可能不是来自神话，但肯定它是一个怪物！每个人都想要双头列表，但不幸的是，它们总是 API 中缺失的一部分。所以，你得搜索第三方库。

有很多用于遗留 Android 代码的库。在我写这篇文章的时候，这还不是一份遗产，但最终会是！

快速的解决方案将是找到一个第三方库，使用它并移植它来使用`AndroidView`类编写代码。这将完成工作，但感觉不像作曲，不是吗？！

Compose 应该易于使用和快速学习，我愿意走这条路。科特林·兰姆达斯会来救援的。说够了，让我们编码吧。

唯一需要遵循的要求是我们必须扩展由`DoubleHeaderLazyColumn`库提供的`Item`类。属性`type`和`subType`将定义外部和内部头，所以我们必须覆盖它们。

然后，我们将创建一些模拟数据来继续我们的示例:

现在我们已经有了所有的数据，我们必须实现类似于`LazyColumn`的东西。

其实，这个方案是一个`LazyColumn`兴奋剂！您必须定义标题内容(`headerContent`)、副标题内容(`subHeaderContent`)、条目内容(`itemContent`)，就像您在常规`LazyColumn`中定义`itemContent`一样。

瞧，最后的结果。

## **图书馆代码**

如果您想进一步了解该功能，请查看代码中的注释，否则您可以将该代码复制粘贴到您的项目中。

这是我发现的让这篇文章简短的最好方法，对于那些想创建一个双重粘性标题列表而又不想让这篇文章读起来很长的人来说。

# 我只需要密码

如果你想把这个库添加到你的模块依赖关系中，或者你需要一些如何使用它的例子，请随意浏览整个项目。

**另请参见** : [您对具有索引功能的](https://medium.com/@tsapalos.v/lazycolumn-with-indexing-for-jetpack-compose-3456023d19d5) `[LazyColumn](https://medium.com/@tsapalos.v/lazycolumn-with-indexing-for-jetpack-compose-3456023d19d5)` [感兴趣吗？](https://medium.com/@tsapalos.v/lazycolumn-with-indexing-for-jetpack-compose-3456023d19d5)
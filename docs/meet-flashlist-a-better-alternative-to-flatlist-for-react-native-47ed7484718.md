# meet flash list:React Native 的 FlatList 的更好替代方案

> 原文：<https://betterprogramming.pub/meet-flashlist-a-better-alternative-to-flatlist-for-react-native-47ed7484718>

## 交换优势的简要指南

![](img/5ea10f169f96513e22c0367eaaefa9ca.png)

照片由[格伦·卡斯滕斯-彼得斯](https://unsplash.com/@glenncarstenspeters?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

自从我开始使用 React Native 进行开发以来，就像其他人一样，我至少遇到过十几次这样的警告:

> VirtualizedList:您有一个更新缓慢的大型列表——确保您的 renderItem 函数呈现遵循 React 性能最佳实践的组件，如 PureComponent、shouldComponentUpdate 等。{"dt":13861，" prevDt":1498372326027，" contentLength":6624}

似乎很熟悉，对吧？这通常发生在我们用许多条目填充平面列表时，这些条目不仅仅是静态图像或文本，而是包含一些动画或其他交互的组件。

这里的 FlatList 简单说就是不能处理列表平滑渲染。你可以一直忽略它作为警告，但这并不意味着它会消失。我希望我们能生活在这样的世界里。

![](img/a39009dfccbfe28aba5a90ce28505bff.png)

照片由[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在开始寻找解决方案之前，让我们深入了解这条信息，找出这些数字是什么:

基于`ScrollView`的`FlatList`，使用了一个叫做`VirtualizedList`的列表接口。在幕后，`VirtualizedList`利用了一个渲染 API，负责以滚动的形式显示一个可枚举的条目列表。可能我们没有注意到是因为我们有最新的 iPhone，我们通过 Instagram 帖子一整天都很顺利。对于移动应用程序来说，滚动操作是一项巨大的进程密集型任务，因为它不仅需要从上到下对下一个即将出现的内容进行预渲染，而且还必须在视口中以滑动效果显示这些内容。

这些密集的计算称为“批处理”，任何超过前一个的批处理大小称为“增量”所以数字是`dt`(增量)、`prevDt`(之前的增量)，用时间戳表示，意思是批次之间经过的时间。最后一个数字是`contentLength`，是 React Native 的无单位(！)单位。每当任何增量值超过 500 毫秒并且`contentLength`超过视窗大小的五倍时，就会触发该错误。这是`VirtualizedList`正常工作的极限。

那么你能做些什么呢？你可以去网上搜一下，或者参考 React Native 官网的`FlatList`，想出一些优化渲染过程的技巧。这些包括使用`initialNumToRender`或`removeClippedSubviews`道具，也许是记忆物品成分或使其纯净。大多数时候，不管你用什么方法，都会让这种信息消失，你就没事了。

但是…

即使看起来没问题，如果您的项目运行在低端设备上，尤其是 Android 设备上，您可能仍然会得到这个错误。因为仅仅是这些技术，也需要做一些计算来解决。也就是说，我们需要做的是将这些进程的负担重定向到其他线程。也许吧，为什么不呢，用户界面，对吗？

认识 FlashList！一个运行在 UI 线程上的 FlatList 替代品，正如他们在他们的网站上声称的那样，它在 JS 上的运行速度快 10 倍，在 JS 线程上快 5 倍。即使只考虑到一半的改进，这些确实是很大的性能提升。

![](img/9bce5f9814200da89c3d6ccd965e31f7.png)

要使用它，您只需使用以下命令安装 FlashList:

```
yarn add @shopify/flash-list
```

如果您需要用<flashlist>替换现有的<flatlist>，请安装 pod。就是这样！</flatlist></flashlist>

是的，FlashList 非常容易使用，因为它有与 FlatList 相同的道具。也就是说`data`、`renderItem`、`ListEmptyScrollComponent`等等。唯一不同的是它使用了`RecyclerView`而不是`VirtualizedList`作为底层组件，我们上面说的表演道具都是多余的。相反，你可以在[https://shopify.github.io/flash-list/docs/usage](https://shopify.github.io/flash-list/docs/usage)查看其他性能和诊断道具。

最值得注意的是，`estimatedItemSize`你提供你的单个组件的高度。如果列表中的每一项大小相等，速度会非常快，因为 FlashList 不需要单独重新计算项目的大小。如果它是灵活的，你可以把你的元素的高度总和提供给这个道具。

另一方面，如果你的目标是低端设备，你可能需要额外调整其他道具，如`getItemType`或`overrideItemLayout`。“轻型组件”逻辑仍然适用于 FlashList，因此您可以查看[https://shopify . github . io/flash-list/docs/fundamentals/performant-components](https://shopify.github.io/flash-list/docs/fundamentals/performant-components)来优化您的组件。

到目前为止，我个人没有在生产中使用这个库，但我在我的旧三星 S5 手机上做了一些测试，我可以告诉我当我使用 FlashList 时，滚动性能变得更好。以前，当我快速滚动时，有明显的空白区域，当我切换到它时，它们都消失了。

你可以去 https://shopify.github.io/flash-list/[的图书馆](https://shopify.github.io/flash-list/)查看，并轻松地用这个替换你现有的平面图。我相信你会注意到性能的提高，特别是如果你在你的项目中使用动画或复杂的布局。

*感谢阅读！*
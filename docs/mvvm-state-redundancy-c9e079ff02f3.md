# 在 iOS 的模型-视图-视图-模型中对抗状态冗余

> 原文：<https://betterprogramming.pub/mvvm-state-redundancy-c9e079ff02f3>

## 使用 MVVM 和 RxSwift 加载并显示播客列表

![](img/42befaf160b03ba2f77d2f1cd36c5e10.png)

照片由 [Esther Jiao](https://unsplash.com/@estherrj?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

移动应用程序中最常见的实际问题之一是从服务器加载可显示的数据，这些数据可以是任何东西，从用户的提要或播客列表到个人资料图片或流媒体视频。

应用程序显示各种微调器和滚动条来指示加载过程，这一切都是为了诱导用户的耐心并改善他们的体验。

然而，这个看似微不足道的问题对程序员来说隐藏着隐患，天真的实现可能导致编写过多的代码和修复突发的错误。

让我们举一个简单的例子，我们需要使用 [Model-View-ViewModel](https://en.wikipedia.org/wiki/Model-view-viewmodel) 模式为屏幕结构加载并显示播客列表，使用 [RxSwift](https://github.com/ReactiveX/RxSwift) 进行 UI 绑定。

# 模型

用于保存播客记录基本信息的不可变结构:

网络层用这个微小的协议来表示；为了简化示例，我将省略实际的网络代码:

# 视图模型

对于播客列表，我们需要一个可观察的`Podcast`结构数组。为此，我们将`[Podcast]`包装在来自`RxSwift`的`BehaviorRelay`类中，这基本上是一个**可观察属性**，它总是保存一个值:

如您所见，我们通过引用`PodcastsService`为 ViewModel 提供了对网络层的访问。

`Podcast`记录的数组最初是空的，但是`loadPodcasts()`函数允许 ViewModel 的用户在适当的时候查询播客，并且当请求完成时，它更新播客的列表。

# 视角

用于显示播客信息的简单 TableViewCell:

最后是显示播客列表的 ViewController:

考虑到`viewModel`是从外部实例化并注入到`ViewController`中的，现在我们有了一个全功能的`MVVM`模块，它可以自动加载并显示播客列表。

唯一的问题是该应用程序目前不是用户友好的。它没有装载过程的指示器；它也没有显示网络层可能出现的错误。

让我们更新视图模型来应对挑战:

我们添加了用于跟踪加载状态的`isLoading`布尔属性，以及用于报告错误的`onError` PublishRelay。

现在我们需要更新 UI 并将其与状态更新绑定在一起:

好了，现在我们显示`viewModel.isLoading`报告播客正在加载时的`UIActivityIndicatorView`，还有一个`UILabel`用于显示`viewModel.onError`发出的错误

请注意，在第 16–17 行中，当`podcasts`有任何记录时，我们还必须显式隐藏`errorMessageLabel`，因为否则，即使播客请求在最初出错后成功，错误消息也会留在屏幕上。

你已经能感觉到代码的味道了。对于这样一个简单的用例，我们已经开始对抗状态不一致性。

让我们深吸一口气，然后思考一分钟。我们的屏幕实际上可能有哪些状态？

1.  播客尚未被查询
2.  正在加载播客
3.  播客加载失败
4.  播客已成功载入

没错，就四箱！我们现在拥有的状态值的叠加(`podcasts`是否为空，`isLoading`和`onError`)给了我们`2*2*2 = 8`种可能的情况。这种状态冗余是我们必须对`podcasts`和`onError`的冲突值进行修复的原因。事实上，这意味着还有其他我们没有考虑到的组合，它们会导致不必要的影响，例如同时显示*错误信息*和*装载指示器*。

当我们发现问题时，我们可以重构当前的实现，通过引入带有这四种情况的`enum`来清除状态冗余:

这个整洁的`enum`允许我们以下面的方式返工`ViewModel`:

只用一个变量，我们就能代表所有的状态，但是现在——没有了状态冗余。

重构 ViewController:

为了方便映射上面代码中的`Loadable<[Podcast]>`，我们可以对`Loadable`使用扩展:

太好了！现在我们的状态-UI 绑定更加清晰，并且不需要过多的代码来修复视觉缺陷。

如果我们想在执行列表刷新时显示先前加载的列表，我们可以用一个保存值的参数来扩展`isLoading`案例。

在 Github 上的[要点中可以找到`Loadable`的最终实现，以及它的一些额外好处；然而，这只是一个如何通过消除视图模型中的状态冗余来提高反应式代码的清晰性和稳定性的例子。](https://gist.github.com/nalexn/df65131de76301130c8e179d02cec939)

在[推特](https://twitter.com/nallexn)上关注我，关注即将发布的帖子！

```
**Want to Stay Updated With My Latest Articles?** Here’s the [RSS](https://nalexn.github.io/feed.xml) link of my blog.
```
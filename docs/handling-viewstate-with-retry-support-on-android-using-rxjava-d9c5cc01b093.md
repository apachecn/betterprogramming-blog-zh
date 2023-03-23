# 使用 RxJava 在 Android 上处理支持重试的视图状态

> 原文：<https://betterprogramming.pub/handling-viewstate-with-retry-support-on-android-using-rxjava-d9c5cc01b093>

## 在重试操作的支持下实现加载、内容和错误状态

![](img/7653801fc8fffddce69eb4ecb9407e48.png)

照片由[马蒂亚斯·米洛斯](https://unsplash.com/@mattiasmilos?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

让我们考虑这个用例:

我们的行动可能需要一些时间，可能成功也可能失败。当失败时，我们希望允许用户通过单击重试按钮来重试操作。

所以我们这样设计我们的`ViewState`:

处于`Error`状态的`onRetryClicked()`将向我们的刷新事件流发布一个事件。这个函数的映射在这篇文章中是不可见的，因为它是不相关的，但是你可以认为它是将`Unit`发布给了`PublishSubject`。

我们这样写我们的`ViewState`处理:

这里发生了什么:

1.  我们的`upstream`是一个无限的流，发出我们的领域实体
2.  将域实体映射到`ViewEntity`。
3.  将`ViewEntity`映射到`ViewState.Content`。
4.  发布`ViewState.Loading`当我们订阅这个蒸汽。
5.  当我们收到一个错误时，我们创建一个`ViewState.Error`并将其发布到我们的 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata) 。
6.  `retryWhen()`当它收到一个项目时不做任何事情。但是当它接收到错误时，它不允许错误向下游传播。取而代之的是，它开始监听`refreshSignalStream`，当有一个项目时，它重新向上游订阅。
7.  我们正在订阅并向我们的 LiveData 发布项目。我们在订阅中也有错误处理，因为`*retryWhen()*`会向下游发送`refreshSignalStream`的错误。

尽管这种处理`ViewStates`的方式有效，但是我们的流并没有发出完整的`ViewStates`。它只散发出`ViewState.Content`。`Loading`和`Error ViewState`被当作副作用处理(使用`doOnError()`和`doOnSubscribe()` *)，*但实际上，它们不是任何副作用。如果你有另一个依赖于这个`ViewState`流的流，你就不能使用它。

让我们通过在同一个流中发出完整的`ViewStates`来改进我们的流，而不是把其中的一些当作副作用。

## 正在修复*视图状态。正在加载*

我们可以很容易地用`startWith()`代替`doOnSubscribe()`。当我们重新订阅该蒸汽时，它将具有相同的效果。

```
upstream
    .map(viewEntityMapper)
    .map<ViewState> { ViewState.Content(it) }
    .startWith(ViewState.Loading)
    ...
```

## 正在修复*视图状态。错误*

当我们想在出现错误时发出另一个项目时，我们可以使用`onErrorResumeNext(aNewStream)`。这不会让错误向下游传播，但它会用我们提供的新流替换该流。

我们将使用等效的`onErrorReturnItem(anotherItem)`，因为我们只有一个项目(也就是`ViewState.Error`)，而且更简单。

此外，如果您想要记录错误，您需要在`onErrorReturnItem()`之前完成，因为它不允许错误向下游传播。

我们增加了用于记录的`doOnError()`和用于发射的`onErrorReturnItem()``ViewState.Error`:

```
upstream
    .map(viewEntityMapper)
    .map<ViewState> { ViewState.Content(it) }
    .startWith(ViewState.Loading)
    .doOnError { Timber.e(it, "Error while executing operation.") }
    .onErrorReturnItem(createErrorState())
```

现在，重试的部分。这是棘手的部分，因为当出现错误时`retryWhen()`开始起作用。但是我们不会有错误，因为我们有`onErrorReturnItem()`不会让错误传到下游*。*

好在`retryWhen()`有个妹子叫`repeatWhen()` *。*不同的是，`retryWhen()`监听错误事件，而`repeatWhen()`监听完成事件。

这是完美的，因为当有一个错误时，`onErrorReturnItem()`将只发出一个项目，然后它完成。我们的`ViewState.Error`将通过这个链并被发布到我们的 LiveData。

在`ViewState.Error`之后发生的完成事件将触发`repeatWhen()` *、*，我们将开始监听来自`refreshSignalStream`的刷新事件。用户将在屏幕上看到重试按钮(因为`ViewState.Error`)，当他们点击按钮`refreshSignalStream` *，*时，它将发出，我们将从`ViewState.Loading`开始重新订阅我们的上游。

我们最后的链条变成这样:

我认为这种方式更好，因为现在我们有了一个发出完整视图状态的流。侦听这些视图状态所需的其他操作现在可以这样做，因为视图状态都没有不同的处理方式。

这个帖子到此为止。请在评论中告诉我你对此的看法！
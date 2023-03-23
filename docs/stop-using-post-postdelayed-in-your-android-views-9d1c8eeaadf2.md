# 停止在你的 Android 视图中使用 Post/PostDelayed

> 原文：<https://betterprogramming.pub/stop-using-post-postdelayed-in-your-android-views-9d1c8eeaadf2>

## 防止可避免的碰撞

![](img/18819c3591033bd603442651abacc30d.png)

[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

从一开始，Android 就提供了一个`[Handler](https://developer.android.com/reference/android/os/Handler)` API。正如文档所述，它允许您在一个`[Looper](https://developer.android.com/reference/android/os/Looper)`的线程上从一个队列中传递消息。

```
Handler().postDelayed({
   doSomething()
}, delay)
```

这个 API 很方便，但却很狡猾。在视图上使用它时，不要让自己被愚弄。为了理解危险在哪里，我们需要更深入地挖掘`View`类。

# 视图的处理程序

Android 中的`View`利用了`Handler` API。它充当内部`Handler`的通道，并公开`post()`和`postDelayed()`两个函数。

仔细看看它的实现(在我写这篇文章的时候):

我希望您注意 Javadoc 注释。`removeCallbacks`行暗示了一些关于移除回调的事情。

让我们来看看它的实际应用:

```
myView.postDelayed({
    myView.animateSomething()
}, delay)
```

在这个例子中，我想在给定的延迟到期时动画显示我的视图。

一旦延迟过期，什么告诉我我的视图仍然存在？用户仍然可以看到它？

你看，视图有生命周期。它们可能会在任何时候被销毁，要么是被系统销毁，要么是因为你的用户在你的应用内导航。

因为您在一个`Looper`中对一条消息进行了排队，所以如果您不告诉它这样做，系统就会传递它。您的应用程序可能会因为`NullPointerException`而崩溃。

你现在明白这个小评论是多么重要了。我见过太多由于这个 API 导致的崩溃，因为开发人员未能处理生命周期。

# 如果我想延迟对视图的操作，该怎么办？

您仍然可以使用这个 API 并提取在您的`Handler`中声明的`Runnable`。正如方法注释中所暗示的，只要回调与代码相关，就需要移除它。

```
private val animateRunnable = Runnable {
    myView.animateSomething()
}myView.postDelayed(animateRunnable, delay)// Somewhere in your code
myView.removeCallback(animateRunnable)
```

我主张禁止这些方法，因为它们被误用或不合适。

例如，如果您正在使用 RxJava，您将不再需要它们。创建一个带延迟或定时器的流是微不足道的。您可以轻松处理您的流。

```
val disposable = Observable.timer(delay, TimeUnit.MILLISECONDS)
    .subscribe {
        myView.animateSomething()
    }// Somewhere in your code
disposable.dispose()
```

# 如果我需要等待下一帧呢？

到目前为止，我只提到了`postDelayed()`。对我来说，`post()`是我见过的应用于`View`的最被误用的 API。

既然没有延迟，为什么还要使用`post()`？记得`Handler`正在`Looper`排队等候消息。过账会将消息排队并传递到下一帧。

通常，我看到开发人员使用这种方法，因为视图没有被布局。假设您想在一个视图出现时就对其进行动画处理。您需要它的位置和/或大小，这取决于您想要实现的动画。

您必须等待视图被测量。因此，您将动画委托给下一帧，并祈祷视图将准备就绪。有了这个解决方案，有两点非常突出:

1.  你不能保证你的视角会在下一帧被测量。
2.  这段代码看起来很不完整。

像`postDelayed()`还有更靠谱的机制。您应该使用`ViewTreeObserver`来代替，特别是以下两个回调函数:

*   `[OnPreDrawListener](https://developer.android.com/reference/android/view/ViewTreeObserver.OnPreDrawListener)`当视图即将被布局时通知您。此时，视图已被测量
*   `[OnGlobalLayoutListener](https://developer.android.com/reference/android/view/ViewTreeObserver.OnGlobalLayoutListener)`每当视图状态改变时触发一个事件

使用这些方法时你必须小心。如果在执行操作后忘记删除侦听器，很可能会造成内存泄漏。

```
myView.*viewTreeObserver*.addOnPreDrawListener(object : ViewTreeObserver.OnPreDrawListener {
    override fun onPreDraw(): Boolean {
        myView.animateSomething()myView.*viewTreeObserver*.removeOnPreDrawListener(this)
        return true
    }
})myView.*viewTreeObserver*.addOnGlobalLayoutListener(object : ViewTreeObserver.OnGlobalLayoutListener {
    override fun onPreDraw(): Boolean {
        myView.animateSomething()myView.*viewTreeObserver*.removeOnGlobalLayoutListener(this)
    }
})
```

更好的是，使用 [KTX 扩展，](https://developer.android.com/kotlin/ktx)并让它们为您处理样板文件。

```
myView.*doOnPreDraw* {    myView.animateSomething()
}myView.*doOnLayout* {    myView.animateSomething()
}
```

那都是乡亲们！当提倡禁止这种机制时，我可能听起来很刺耳。至少，我鼓励你把他们赶走，并思考他们是否适合这项任务。
# 如何从 Jetpack Compose 获取活动

> 原文：<https://betterprogramming.pub/how-to-get-activity-from-jetpack-compose-d0af406d534f>

## 安全地获取可组合的当前活动

![](img/9b799bfb98911cd64855a841663d4ac7.png)

塞缪尔·施文登纳在 Unsplash 上的照片

在使用 Compose 时，经常需要获取一个`Activity`。例如，我需要一个`Activity`来调用`finish()`，当一个按钮被按下时，屏幕需要关闭。但是，目前还没有内置的方法可以直接获取放置 composable 的`Activity`。在这篇文章中，我将介绍在 Compose 中获得`Activity`的 3 种方法。

## LocalContext.current 作为活动

最简单最容易的方法就是`LocalContext.current as Activity`。只是把`Context`铸造成`Activity`。在某种程度上，这可能是一种非常危险的方法。但是我认为它是安全的。要理解为什么这是一种安全的方法，我们必须先看看`LocalContext`是如何提供的。

所有可组合数据都从`ComponentActivity.setContent`开始，所有组合数据(包括`LocalContext`)都在这里初始化。

如果你看一下`setContent`，那么`ComposeView`是由调用`setContent`到`ComposeView(this)`的`Activity`初始化的。之后，构图初始化处理通过`ComposeView(this).setContent(content).`进行

在该过程中，调用`ProvideAndroidCompositionLocals`函数。你可以看到这里提供了大部分的`CompositionLocal`，也提供了`LocalContext`。作为参数接收到的`owner`变成之前用`Activity`初始化的`ComposeView`，从这个`ComposeView`中获得`Context`并作为`LocalContext`提供。

通过这个过程，我们可以确认`LocalContext`正在将我们称之为`setContent`的`Activity`传入`Context`，所以`LocalContext.current as Activity`是安全的。

## 本地活动.当前

然而，将`Context`转换为`Activity`仍然会有些尴尬。在这种情况下，可以创建一个`LocalActivity`并直接提供`Activity`来使用。

> 性能优化提示:`Activity`实例不经常改变或者根本不改变，所以用`staticCompositionLocalOf`创建一个`CompositionLocal`消除了跟踪值变化的不必要过程，这减少了开销并优化了性能。

```
*setContent* {
    *CompositionLocalProvider*(*LocalActivity* provides this) {
        composable()
    }
}
```

在使用 composable 之前只需要提供一次`provides`，它是被传播的，所以同根的 composable 可以很容易地通过`LocalActivity.current`得到`Activity`。

谁知道数据是如何存储在 Compose 中的，可能会担心将`Activity`放入数组会导致内存泄漏。(所有合成数据，包括`CompositionLocal`数据，在`Array<Any?>`数组中管理)

如果`Activity`消失，所有可组合程序都有一个擦除分配给`Activity`的可组合程序使用的所有数据的过程。所以不存在内存泄漏。

## Context.findActivity()

最后 Google 用的的方法[也是最安全的方法。](https://github.com/google/accompanist/blob/6611ebda55eb2948eca9e1c89c2519e80300855a/permissions/src/main/java/com/google/accompanist/permissions/PermissionsUtil.kt#L99)

这是在`accompanist/permissions`中使用的代码，它在`Context`中迭代时搜索`Activity`。它在`Activity`直到最后都找不到的时候抛出，所以对于想要最强安全性的人来说是个好方法。

# 结束了！

这样，我介绍了我所知道的从 Composable 中获取`Activity`的三种方法。如果你有更好的方法或者正在使用的方法，请在评论中分享！

感谢阅读。

```
[[View in Korean]](https://sungbin.land/jetpack-compose-%EC%97%90%EC%84%9C-%EC%95%A1%ED%8B%B0%EB%B9%84%ED%8B%B0%EB%A5%BC-%EA%B0%80%EC%A0%B8%EC%98%A4%EB%8A%94-%EC%B5%9C%EC%84%A0%EC%9D%98-%EB%B0%A9%EB%B2%95%EB%93%A4-a806f746713b)
```

*审查通过*[吉普森](https://medium.com/u/2bd0a0e19600?source=post_page-----d0af406d534f--------------------------------) *。谢谢！*
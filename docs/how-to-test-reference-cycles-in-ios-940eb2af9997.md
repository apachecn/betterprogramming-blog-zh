# 如何在 iOS 中测试参考周期

> 原文：<https://betterprogramming.pub/how-to-test-reference-cycles-in-ios-940eb2af9997>

## 关于如何在 Swift 应用程序或库中测试参考周期的建议

![](img/07ab889cdf50831cb690397a157d8ee8.png)

本杰明·索乌在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

受 Vincent Pradeilles[](https://medium.com/u/341500d28721#if DEBUG</code>条件。这样，如果有人试图在生产中使用它，该应用程序根本不会编译。</p><figure class=)

[使用这种方法，危险的`init`甚至不会包含在我们的框架或应用程序的发布版本中。`deinitObserver`闭合将始终为`nil`并像`noop`一样工作！](https://medium.com/u/341500d28721#if DEBUG</code>条件。这样，如果有人试图在生产中使用它，该应用程序根本不会编译。</p><figure class=)

[](https://medium.com/u/341500d28721#if DEBUG</code>条件。这样，如果有人试图在生产中使用它，该应用程序根本不会编译。</p><figure class=)
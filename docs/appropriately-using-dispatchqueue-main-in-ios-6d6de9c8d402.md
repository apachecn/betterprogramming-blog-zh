# 在 iOS 中正确使用 DispatchQueue.main

> 原文：<https://betterprogramming.pub/appropriately-using-dispatchqueue-main-in-ios-6d6de9c8d402>

## 理解向主线程分派代码意味着什么以及何时应该这样做

![](img/c180018eaddac5018226b894726e823b.png)

图为[安库什·明达](https://unsplash.com/@an_ku_sh?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/release?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

许多 iOS 开发人员最终会遇到调用`DispatchQueue.main`的代码。

很明显这样做是为了更新 UI，但是我见过很多这样的情况，如果 UI 没有像他们期望的那样更新，或者如果他们遇到他们不理解的崩溃，开发人员使用`DispatchQueue.main`试图让他们的代码工作。

出于这个原因，我想把这篇文章献给这些问题:什么时候我应该使用`DispatchQueue.main`？为什么呢？

# 了解主调度队列做什么

在 iOS 中，我们使用调度队列并行执行工作。这意味着您可以同时运行几个调度队列，并且它们可以同时执行任务。通常，调度队列以先进先出的方式一次只执行一个任务。但是，可以将它们配置为同时调度工作。

主调度队列是一个一次运行一个任务的队列。它也是执行所有布局更新的队列。如果有人谈论不阻塞主线程的重要性，他们真正想说的是他们不想让主调度队列忙碌太久。如果主队列太忙，你会发现应用程序的滚动性能变得不稳定，动画断断续续，按钮没有反应。

这样做的原因是主队列负责所有与 UI 相关的事情，但是就像我们已经建立的那样，主队列是一个串行队列。因此，如果主队列忙于做一些事情，它就不能响应用户输入或绘制新的动画帧。

iOS 中的很多代码是需要一段时间才能运行的代码。例如，发出一个网络请求就是一个执行非常慢的代码的好例子。一旦网络调用被发送到服务器，代码必须等待响应。等待时，该队列不做任何其他事情。几秒钟后响应返回时，队列可以处理结果并继续下一个任务。如果您在主队列上执行这项工作，那么在整个网络请求期间，您的应用程序将无法绘制任何 UI 或响应用户输入。

我们可以用一句简短的话来概括这一部分:主队列负责绘制 UI 和响应用户输入。在下一节中，我们将仔细看看什么时候应该使用`DispatchQueue.main`以及它到底有什么作用。

# 在实践中使用 DispatchQueue.main

继续以进行网络调用为例，您可以假设网络调用是在它们自己的队列上进行的，远离主队列。在我们继续之前，我想提醒您一下，并向您展示一下网络呼叫的代码是什么样子的:

```
URLSession.shared.dataTask(with: someURL) { data, response, error in
  // implementation
}
```

数据任务是用一个完成闭包创建的，这个闭包在网络调用完成时被调用。

由于服务器返回的数据可能仍然需要处理，iOS 在后台队列上调用您的完成闭包。通常，这很好。我不认为在任何情况下，您都不想对从服务器接收的数据进行任何处理。有时您可能需要比其他时候做更多的处理，但是根本不处理是非常罕见的。然而，一旦处理完数据，您可能会想要更新您的 UI。

因为您知道在处理网络响应时您不在主队列上，所以您需要使用`DipatchQueue.main`来确保您的 UI 在主队列上更新。下面的代码是一个在主队列上重新加载表视图的示例。

```
DispatchQueue.main.async { 
  self.tableView.reload() 
}
```

这段代码看起来很简单，对吗？但这到底是怎么回事？

`DispatchQueue.main`是`DispatchQueue`的一个实例。所有的调度队列都可以安排他们的工作在`sync`或`async`执行。

通常，您会希望调度工作`async`，因为同步调度您的工作会暂停当前线程的执行，等待目标线程执行您传递给`sync`的闭包，然后恢复当前线程。使用`async`允许当前线程恢复，而目标线程可以在需要时调度并执行闭包。让我们看一个例子:

```
func executesAsync() { 
  var result = 0 
  DispatchQueue.global().async { 
    result = 10 * 10 
  } print(result) // 0 
} func executesSync() { 
  var result = 0 
  DispatchQueue.global().sync { 
    result = 10 * 10 
  } 
  print(result) // 100 
}
```

前面的两个函数看起来非常相似。这里的主要区别是`executesAsync`异步调度到主队列，导致`result`在`result`更新之前被打印。

`executesSync`函数同步调度到主队列，导致`executesSync`的执行暂停，直到传递给`DispatchQueue.main.sync`的闭包执行完毕。这意味着当`print`被调用时`result`被更新。

在重载表视图的上下文中考虑前面的例子。如果我们使用`sync`而不是`async`，当主线程重新加载表视图时，网络调用完成闭包的执行会暂停。

一旦重新加载了表视图，闭包的执行就会恢复。通过使用`async`，完成闭包继续执行，只要主队列有时间，表视图就会重新加载。希望这几乎是瞬间完成的，因为如果需要一段时间，这可能是阻塞主线程的征兆。

# 了解何时使用 DispatchQueue.main

既然您已经知道了什么是主队列，并且已经看到了如何以及何时使用`DispatchQueue.main`的示例，那么您如何知道在您的项目中何时应该使用`DispatchQueue.main`？

最简单的答案是，当您在委托方法或完成闭包中更新 UI 时，总是使用它，因为您无法控制如何或何时调用该代码。

此外，如果 Xcode 检测到你在主线程之外做 UI 工作，它会让你的应用崩溃。虽然这是一个非常方便的功能，帮助我不时地防止错误，但它并不是 100%可靠。有更好的方法来确保您的代码在必要时运行在主线程上。

记住总是使用`DispatchQueue.main.async`将队列异步分派到主队列，以避免阻塞当前线程——甚至可能死锁你的应用程序，如果你从已经在主队列上的代码调用`DispatchQueue.main.sync`,就会发生这种情况。用`async`分派到主队列不会带来同样的风险，即使你已经在主队列中。

让我们看最后一个例子。如果您获取用户的当前推送通知权限或请求他们的联系人，您知道该操作异步运行，可能需要一段时间。如果您想在用于这些操作的完成闭包中更新 UI，最好通过将 UI 更新包装在一个`DispatchQueue.main.async`块中来明确确保您的 UI 更新是在主队列上完成的。

# 概括起来

编写使用多个队列的应用程序可能非常复杂。作为一般规则，请记住主队列是为 UI 工作保留的。这并不意味着所有的非 UI 工作都必须离开主队列，但这意味着所有的 UI 工作大部分都在主队列中，而所有其他工作可以在其他地方(例如，如果您知道一个操作可能需要一段时间才能完成)。

换句话说，对本文开头的问题的简短回答是，您应该使用`DispatchQueue.main`将 UI 相关的工作从非主队列发送到主队列。
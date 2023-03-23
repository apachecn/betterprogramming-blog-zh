# 解耦颤振视图以实现可重用性

> 原文：<https://betterprogramming.pub/decoupling-flutter-views-for-reusability-1ec8b58f5521>

## 创建更好的结构化视图

![](img/e89b13d4233c7a6ca066e273965312ef.png)

照片由[马特雷姆斯](https://unsplash.com/@spookymatt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/buildings?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。

颤动视图代表了呈现给用户的界面。它们提供交互，让用户感受到应用程序的本质。不幸的是，颤振视图也经常被忽略，导致紧密耦合和不可重用的组件。

在本文中，我将讨论可以帮助您构建更多可重用和松耦合视图的技术。

# 显示客户列表

让我们考虑一个场景，其中我们必须在应用程序中显示客户列表。点击客户后，我们会将用户发送到详细信息屏幕。为了创建这个应用程序，我们将从实现一个代表整个屏幕的`CustomerListScreen`小部件开始。我们可以使用`ListView`小部件显示客户列表。这在下面实现:

上面的代码工作并在小部件上显示客户列表，但是它不可重用。这意味着如果我们想在其他小部件中显示客户列表，我们将不得不手动复制/粘贴代码。

我们可以通过创建一个`CustomerList`小部件来解决这个问题。这个小部件将负责显示客户列表。

# 正在创建 CustomerList 视图

创建`CustomerList`小部件的基本思想是制作一个可重用的小部件，可以在我们的应用程序中的多个地方使用。我们将传递一个客户列表给`CustomerList`小部件，它将负责向用户显示他们。

这使得`CustomerListScreen`更加精简，因为它可以简单地将工作委托给`CustomerList`小部件。

尽管这很有效，但现在我们的`CustomerList`小部件与导航紧密耦合。这意味着每次我们点击`CustomerList`中的一个项目，我们总是导航到`CustomerDetailScreen`。如果调用者而不是`CustomerList`小部件负责目的地，那就好了。

# 客户列表视图和导航

为了将控制传递回调用者，我们将在 Dart 语言中使用闭包/函数。我们将允许用户传入闭包，这个闭包稍后可以被`CustomerList`视图调用。

在上面的代码中，我们更新了我们的`CustomerList`小部件，以包含一个`onSelected`闭包。我们没有把它作为一个必需的参数，因为有时小部件只是想显示数据而不执行任何导航。稍后，当用户点击客户时，我们调用`onSelected`，传递选定的客户。

`CustomerListScreen`捕获闭包并执行适当的导航。

这使得我们的 Flutter 小部件可以重用，并且与应用程序的其他部分分离。现在，如果其他屏幕想要显示客户列表，它可以使用`CustomerList`小部件导航到一个完全独立的屏幕。

# 结论

视图是任何 Flutter 应用程序的构建块。松散耦合和可重用的架构视图可以为您的项目带来好处。我希望本文展示的技术能够帮助您创建更好的结构化视图。
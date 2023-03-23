# Swift 中的外观设计模式

> 原文：<https://betterprogramming.pub/design-patterns-in-swift-facade-a7303dc3d95b>

## 让我们使用 Swift 中的一个真实例子来讨论门面设计模式

![](img/747be9490a7dd8ec98c58aa9ddc9bddd.png)

照片由[丹尼尔·法齐奥](https://unsplash.com/@danielfazio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/facade?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Facade 是一种设计模式，它为一组子系统提供了一个简单的接口。

它有助于降低复杂性，并最小化子系统之间的依赖性。

让我们来看一个 Facade 实现的通用结构:

门面实现通常有两个主要参与者:

**Facade** :提供了一个更高级别的接口，当我们不关心我们子系统的细节，或者当我们想要简化它们之间的交互时，可以使用这个接口。它可以是一个类或者一个结构(为了简单起见，我们只是在例子中使用了一个类，但是如果场景允许的话，它肯定可以是一个结构)。

**子系统**:实现子系统的功能，对外观类型一无所知。与 Facade 组件相同，它可以是类或结构。

## 真实世界的例子

假设我们正在开发一个电影应用程序，我们负责获取即将上映的电影列表。

这种获取应该考虑到我们有两种类型的用户帐户:高级和非高级，并且显示的列表将包含每种帐户类型的不同电影。

所有这些信息(用户帐户类型和根据帐户类型的电影列表)都应该从后端服务中检索。

此外，假设我们的应用程序中已经有了这两个客户端:

`AccountClient`有一个方法来检索当前用户的帐户类型，而`MoviesClient`有一个方法来检索给定特定帐户类型的即将上映的电影列表。

**注意**:这两个协议的实现细节对于本文来说不是必需的。我们只需要关注协议的方法定义。

您可能会想，无论谁想要检索电影列表，都应该将这两个方法链接起来(从`AccountClient`获取帐户类型，并将其传递给`MovieClient`以获取即将上映的电影)；但是这种方法有几个缺点:

1.  我们依赖两个客户端来执行一个相当简单的任务(如果要执行的任务非常复杂，依赖性可能会迅速增加)。任何时候，我们想要获得即将上映的电影列表，无论是付费的还是非付费的，我们都需要与这两种依赖关系进行交互。
2.  当检索电影列表时，我们需要了解两个客户端的实现细节。这些客户可能有很多我们不关心的方法，在列出电影时对我们没有用。

这是一个使用门面模式的好地方！

我们将实现一个`MovieListFetcher`,作为我们两个客户的门面:

正如您所注意到的，`AccountClient`和`MoviesClient`将是我们的子系统，`MovieListFetcher`将是这两个子系统的门面实现。

使用 Facade 模式，我们基本上抽象了子系统的接口，使它们更容易使用:我们不应该再关心`AccountClient`或`MoviesClient`如何工作，我们只需要创建一个`MovieListFetcher`的实例，并调用它的`getMovies`方法！

考虑每个子系统本身也可以是一个门面也很重要:`MovieClient`或`AccountClient`实现可以有大量内部使用的子系统。

就是这样！我希望这个 Facade 模式的真实用例对您和您未来的项目有用。
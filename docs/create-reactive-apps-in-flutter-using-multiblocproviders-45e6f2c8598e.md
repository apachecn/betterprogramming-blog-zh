# 如何使用 Flutter Bloc 在 Flutter 中编写反应式应用程序

> 原文：<https://betterprogramming.pub/create-reactive-apps-in-flutter-using-multiblocproviders-45e6f2c8598e>

## 揭开 MultiBlocProviders 的神秘面纱，同时在此过程中提供出色的用户体验。

![](img/5e12e0d3be8e87de5f2c3735c89401f2.png)

詹姆斯·温斯科特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

颤振席卷了整个世界。越来越多的人转向 it，越来越多的公司使用它来构建他们的应用程序。迁移背后的原因不是 fluth 本身，而是过去几年中生态系统的成熟。

如果你曾经编写过 Flutter 应用程序，你可能知道一个名为`[Cubit](https://github.com/felangel/cubit)`的库。`[Cubit](https://github.com/felangel/cubit)`是一个帮助实现反应式编程的状态管理库。当构建移动应用时，你需要某种状态管理。否则，你的数据会到处都是。

但是我们今天根本不会谈论`[Cubit](https://github.com/felangel/cubit)`。我们将讨论颤动集团。更具体地说，我们如何使用`[flutter_bloc](https://pub.dev/packages/flutter_bloc)`库在 Flutter 中实现多块提供者。

# 什么是 Flutter BLoC？

`[flutter_bloc](https://pub.dev/packages/flutter_bloc)`是一个状态管理库，是前面提到的`[Cubit](https://github.com/felangel/cubit)`库的延续。它提供了一个易于使用的 BLoC 模式实现。您不再需要从头开始编写自己的流和事件订阅，因为`[flutter_bloc](https://pub.dev/packages/flutter_bloc)`会为您完成所有工作。

## 什么是集团模式？

BLoC 代表“业务逻辑组件”它是由 Flutter 开发人员开发的一种模式，用于解决关注点分离问题。在 Flutter 中，您可以混合前端代码和业务逻辑，这使得创建不可读的意大利面条式代码变得非常容易。此外，干净的颤振代码通常涉及使用缩进，这可能会使您的混乱既长又宽(字面意思！).

开发人员也选择实现 BLoC 模式，因为它具有固有的反应性。您可以通过创建侦听器来使您的应用程序对 BLoC 更加敏感。每次收到事件时，每个侦听器都会触发 UI 重建。本质上，我们将为我们的 UI 订阅数据流。

## 什么是 BlocProviders？

Bloc 提供程序初始化 bloc 并将其提供给其子节点。它是一种类似于**的注射机构。bloc 提供者中的小部件可以从`BuildContext`访问 bloc，并从那里发出事件。因为构建上下文是传递下来的，所以您可以很容易地访问部件树深处的块提供者。**

## **多块提供者怎么样？**

**多块提供程序是一个允许初始化多个块实例的小部件。当您需要一次更新多个组件时，您可以在父小部件上初始化一个多块提供者，然后从上下文中获取每个提供者。检索提供者将使您能够向流发送事件，从而更新组件。**

# **使用颤振集团的利与弊**

**尽管`[flutter_bloc](https://pub.dev/packages/flutter_bloc)`似乎是你的应用程序的一个显而易见的选择，你可能要考虑利弊。我做过研究，结合自己和团队使用的经验，我觉得可以给你更广阔的视野。**

## **优点:简单**

**使用第三方库的目标显然是简化过程。颤振集团就是这样做的。您不必像在其他情况下那样学习反应式编程概念。产生的代码是可读和可管理的。**

## **优点:灵活的状态管理**

**在我看来，Flutter Bloc 对于它所解决的问题类型是非常灵活的。您可以从最小的组件轻松触发状态更改。你所需要的是`BuildContext`，你实际上已经准备好了。**

## **缺点:对于小应用来说太复杂了**

**尽管这很简单，但我确实认为 Flutter Bloc 对于小应用来说是大材小用。从使用 BLoC 模式中获益不多的应用根本不应该实现 Flutter Bloc(很明显)。**

## **缺点:更难的学习曲线**

**阻塞模式本身是一个很难理解概念，更不用说颤动阻塞了。集团是一个完全不同概念。也许这一点听起来不真诚，因为它证明了集团多于动荡集团。但是，因为需要对 BLoC 有一个很好的理解，甚至理解正在发生的事情，我认为它仍然需要说。**

# **正在初始化 MultiBlocProvider**

**首先，您必须在应用程序启动时初始化多块提供程序:**

**作者的代码片段**

**直觉上很简单。你只需要在做任何事情之前初始化`runApp`函数中的`MultiBlocProvider`。然后在里面创建你想要初始化的`BlocProviders`。**

**不用担心`BlocProvider`在组件初始化之前被调用。[因为](https://pub.dev/documentation/flutter_bloc/latest/flutter_bloc/BlocProvider/lazy.html) `[BlocProvider](https://pub.dev/documentation/flutter_bloc/latest/flutter_bloc/BlocProvider/lazy.html)` [默认是懒惰的](https://pub.dev/documentation/flutter_bloc/latest/flutter_bloc/BlocProvider/lazy.html)，所以只有当第一个请求 bloc 的事件被调用时才会调用。**

# **从上下文调用 BlocProvider**

**现在每当你需要调用一个事件时，你只需要从块内部访问上下文。只要小部件的父部件被包装在一个`MultiBlocProvider`中，您就可以通过这种方式安全地访问这个块:**

```
**BlocProvider.of<ProductBloc>(context)**
```

**通过这种方式可以访问`ProductBloc`或`SessionBloc`。将事件添加到流中后，侦听器将在接收到其预期状态后更新 UI。为此，您可以利用`BlocBuilder`:**

**作者的代码片段**

**你可以把它添加到你应用的任何地方，它会自动重建。只要确保你没有在其中重新启动同一个块的`BlocBuilder`。**

# **为什么我们需要多集团提供商？**

**我们需要多块提供者来解决与同时更新具有不同状态的多个组件相关的问题。我知道这听起来非常接近使用多集团提供商的基本前提，我在重复我自己。但是听我说完。**

**一个很好的例子是一个移动市场应用程序，您需要在搜索产品的同时获取购物车中的产品。因为有两个不同的事件，所以需要有两个块提供者。**

**等等，有没有可能把它们都合并到一个事件状态里？是的，但是每次你添加一个商品到你的购物车时，你都必须得到产品。该集团将失去其原子性。**

**你也不能连续调用两个返回不同状态的事件。例如:**

```
**BlocProvider.of<ProductBloc>(context)
  ..add(GetProduct())
  ..add(GetCart())**
```

**可以在产品块上添加一个`Cart`事件，并在检索产品后调用它。但是因为在`GetProduct`之后调用了另一个事件，所以先前加载产品数据的监听器也被触发，状态发生变化。**

**解决这个问题同时仍然获得我们需要的原子性的一种方法是通过创建多个块并连续调用:**

```
**BlocProvider.of<ProductBloc>(context)
  ..add(GetProduct())BlocProvider.of<CartBloc>(context)
  ..add(GetCart())**
```

**这样，我们可以将`ProductBloc`和`CartBloc`的`BlocBuilder`分离成不同的组件。**

# **最后的话**

**创建反应式应用从来都不容易。但是有了 Flutter 和 BLoC，完全可以有一个流畅的反应式 app。你不需要担心你的组件在应该更新的时候没有更新，你可以提高你的应用程序的 UX 质量。没有什么比反应式应用程序更能说明 UX 的优秀了。**

**最终，用户体验是关键。出色的用户体验自然会带来更多的用户参与。赢得用户不仅要营销你的产品，还要给他们能想象到的最好的体验。**
# 如何在 Redux 状态下删除嵌套对象中的项目

> 原文：<https://betterprogramming.pub/deleting-an-item-in-a-nested-redux-state-3de0cb3943da>

## 你的 Redux 状态可能会变得复杂，以下是如何移除嵌套数据

![](img/baa126667f6e97e2ba16aaeef4b9559f.png)

照片由[吕山德·袁](https://unsplash.com/@lysanderyuen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/complex?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

[Redux](https://redux.js.org/) 的一个关键特点就是它的[商店](https://redux.js.org/api/store/)；它将应用程序的逻辑保存为一个状态对象。

本质上，您可以在商店中触发有限数量的方法。例如，使用`[getState](https://redux.js.org/api/store/#getstate)`作为读取当前状态树的机制。

你可能会问:“给国务院写信怎么样？”

嗯，这就是`[dispatch(action)](https://redux.js.org/api/store/#dispatchaction)`的用武之地。这是 Redux 中触发状态改变的唯一方式。调度行为与`reducer`概念紧密相关。减速器接收`action`和`state`，并返回新的应用状态。

黄金法则是，我们不会回到一个突变的状态，而是一个新的状态。根据您的动作类型，当它遇到 reducer 时，您可能需要以各种形式更新您的状态树。

*   您可能需要向其中一个状态属性添加一个新项目。
*   可能需要更新或重新分配状态属性。
*   或者，您可能需要从状态属性中删除一个项目。

这让我们想到了 [Redux 的不可变更新(或数据修改)模式](https://redux.js.org/recipes/structuring-reducers/immutable-update-patterns/)的概念。不变性是关键，因为我们从来不想直接改变状态树中的值，而是总是进行复制并基于旧值返回新值。

状态属性可以有许多不同的类型。但是当我们谈论数据修改模式的挑战时，`Array`和`Object`可以被认为是具有更多边缘情况的。

在本文中，我们将关注一个特定的 Redux 数据修改模式，该模式涉及删除状态树上嵌套对象中的一个项目。

# 用例:在 Redux 状态下删除嵌套对象中的项目

假设我们有一个时尚电子商务应用程序，初始状态如下。

状态树很简单，包括几个对象键，如`outfits`或`filters`。

在我们的应用程序中，我们有一个过滤机制，它根据像`brand` 或`colour`这样的属性过滤服装。例如，如果用户对具有`Black`颜色的`Bally`品牌的服装感兴趣并进行过滤；这将导致下面的状态树。

如果我们想要一个更加用户友好的应用程序，我们可能会对显示用户选择的过滤器感兴趣；就在过滤系统下面。

这样，用户可以选择查看他们选择的所有过滤器，并可以删除他们不再感兴趣的过滤器。

![](img/7e452168e652aa911baccc435e4718c0.png)

为了实现这个目标，我们需要一个`removeFilter` `actionCreator` ，它接受显示的过滤器中的字段类型(例如，`brand`)和索引(例如，第一个元素),并从状态树中相应的元素中删除特定的过滤器值。

下面的代码显示了渲染部分。通过迭代过滤列表来呈现按钮，过滤列表呈现`Button`并向它们提供`index`和`field`。

# **回购**

如果你有兴趣，[这里有一个回购](https://github.com/kasrakhosravi/redux-update-patterns) 更好地展示了我们在本文中涉及的用例。
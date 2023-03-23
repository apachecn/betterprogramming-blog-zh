# 非冗余全局状态库的 4 种不同方法

> 原文：<https://betterprogramming.pub/4-different-approaches-to-non-redux-global-state-libraries-1fd159efb5fa>

## 从消费者的角度来看

![](img/e0531c1fb5da2e5287607d000f69b72d.png)

# 介绍

自从 React Hooks 登陆以来，已经有很多针对全局状态的库被提出。其中一些是简单的上下文包装器，而另一些是全功能的状态管理系统。

从技术上讲，如何存储状态和通知更改有几种实现。在这篇文章中，我们不详细讨论，只注意两个轴:

1.  无论是基于上下文的存储还是外部存储
2.  无论是基于订阅还是上下文传播。

在这篇文章中，我们关注的是消费者端钩子的 API 设计。根据我的观察，API 设计有四种方法。让我们通过伪代码的例子来看看每种方法。举个简单的例子，我们假设一个应用程序具有以下内容:

*   两个全局计数器；
*   两个计数器组件；和
*   增加两个计数器的动作。

注意，在提供者端，它是与实现无关的。因此，`<Provider>` 不一定意味着对上下文做出反应。

# 方法 1:多重环境

这种方法可能是最惯用的。用 React context 和 useContext 可以很容易地实现这种方法。

有两个采用这种方法的库: [constate](https://github.com/diegohaz/constate) 和[unstated——next](https://github.com/jamiebuilds/unstated-next)。

# 方法 2:通过属性名(或路径)进行选择

这种方法是将更多的价值放在一个商店中。单个存储允许您分派一个操作来更改多个值。您可以指定一个属性名来获取相应的值。通过名称来指定很简单，但是在复杂的情况下有点受限。

这种方法有两个库: [react-hooks-global-state](https://github.com/dai-shi/react-hooks-global-state) 和 [shareon](https://github.com/storeon/storeon) 。

# 方法 3:通过选择器函数进行选择

与前面的代码相比，只有两行发生了变化。选择器函数比属性名更灵活。如此灵活，以至于它们可能会像进行昂贵的计算一样被误用。最重要的是，性能优化通常需要您保持对象引用相等。

采用这种方法的两个库: [zustand](https://github.com/react-spring/zustand) 和 [react-sweet-state](https://github.com/atlassian/react-sweet-state) 。

# 方法 4:状态使用跟踪

注意`state`部分与之前的代码有所不同。`dispatch`部分不变。这种方法消除了选择器功能，而且很难被误用。一个大问题是性能优化。这超出了本文的范围，但根据一些基准测试，它实际上已经相当不错了。如果你有兴趣，请查看[的另一个帖子](https://blog.axlight.com/posts/benchmark-react-tracked/)。

这里有一个采用这种方法的库:[反应跟踪](https://github.com/dai-shi/react-tracked)。

# 结束语

这些例子可能太做作，但我希望它们能解释不同之处。就个人而言，我会根据案例及其需求使用任何方法。

最后，本文的第二个目的是让读者了解最后一种方法，“状态使用跟踪”我希望你得到它。

原刊[此处](https://blog.axlight.com/posts/four-different-approaches-to-non-redux-global-state-libraries/)。
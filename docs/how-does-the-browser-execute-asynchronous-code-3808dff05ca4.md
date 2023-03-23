# 浏览器如何执行异步代码？

> 原文：<https://betterprogramming.pub/how-does-the-browser-execute-asynchronous-code-3808dff05ca4>

## JavaScript 如何与浏览器 API 一起工作，逐步解释

![](img/94928cff0fcf04b660cb2cce921b3fc5.png)

戈兰·艾沃斯在 [Unsplash](https://unsplash.com/) 上的照片

本文一步一步地解释了 JavaScript 引擎如何工作并与浏览器 API 交互，从而能够执行异步代码而不阻塞正常的执行线程。

重要的事情先来。这不是快速阅读。我试图用日常的例子来帮助你理解它是如何工作的，但是如果你想阅读并理解它，这将需要你大量的时间。

我的文章基于我从[的信息。](https://medium.com/u/5dccb9bb4625#sec-jobs-and-job-queues)

# JavaScript 的执行

代码的执行过程有两个部分。我想介绍一些[术语](https://www.bookstack.cn/read/AsyncPerformance/spilt.1.ch2.md)。

## 执行的线程

也叫*程序的前半部分*。

这是第一次在代码出现时逐行解析和执行代码。它分配变量和函数，代码在执行上下文中执行。(此部分同步执行)

## 延迟代码执行

也叫*程序的后半部分*。

这段代码是在我们完成正常的执行线程后的某个时间点执行的。例如，如果我们调用`setTimeout`，或者等待 API 请求数据从服务器返回，就会发生这种情况。我们不应该在等待的时候阻塞 JavaScript 线程的执行。

## Web APIs

浏览器提供了一些特性，这些特性有助于在不阻塞线程的情况下执行异步代码。这些特性被称为[*web API*](https://developer.mozilla.org/en-US/docs/Web/API)*(浏览器)*。

您可能会认出其中的几个，比如计时器，`setTimeout` 和`setInterval`方法所在的位置，`[XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)`由 fetch 方法使用，[控制台 API](https://developer.mozilla.org/en-US/docs/Web/API/Console_API) ， [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model) ，以及 [web 存储 API。](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)其中一些如图所示。

# 代码的执行:设置超时和承诺

我想借助简单的例子来说明这些概念，所以我将详细解释当我们调用`setTimeout`时会发生什么，接下来，我将使用承诺来经历一个相当类似的场景。

## **举例:** `setTimeout`

让我们通过一个简单的日常场景来研究这个案例。在我的日常生活中，当我早上醒来时，亲吻女儿后我需要的第二件事是一杯*咖啡*。

所以，计划是喝咖啡，对吗？

我打开咖啡机，按下*浓缩咖啡*。为了使这个例子更简单，让我们假设咖啡机总是花费相同的时间来制作咖啡，并且只有 60000 毫秒(1 分钟)。

因此，我们“要求”咖啡机开始为我们准备咖啡。请注意，不是我在做这件事，而是一个外部机制在为我做这件事。与此同时，我可以做其他事情，比如给朋友写短信。

当机器完成准备我们的咖啡，它告诉我们，是准备好了。我们不需要立即开始喝咖啡，这意味着我们可以完成此刻正在做的任何事情，把喝咖啡的行为留到以后。

像“有喝咖啡的意图”、“按下机器上的按钮开始准备咖啡”和“给朋友写短信”这样的动作是发生在节目前半部分的事情。

准确地说，正确的“喝咖啡”行为是推迟的，发生在未来的某个时刻，在节目的后半部分。

让我们把它写成代码:

喝咖啡的简单守则

## 逐步时间表

为了使事情更简单，让我们假设每条指令需要 1 毫秒来执行，这样我们就可以看到它们是如何发生的，一毫秒接一毫秒。

跟随幻灯片，将带你到执行的每一个步骤。

setTimeout 的异步执行

## 但是，承诺会怎么样呢？

在 JavaScript 中， [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 对象代表未来数据的占位符。

例如，当我们调用 [fetch](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch) 函数向服务器发出请求时，此时会发生两件事:

1.  一个[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)对象被创建。
2.  [fetch](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch) 命令请求浏览器中的 [web API](https://developer.mozilla.org/en-US/docs/Web/API) 为其解决问题。

创建承诺对象时，代码在浏览器中的[web API](https://developer.mozilla.org/en-US/docs/Web/API)**的帮助下执行。**

**在设置了`value`属性之后，`.then()`方法附加了我们希望被调用的回调。这些方法在内部注册。一旦设置了承诺`value`，它就调用所有注册的方法。**

## ****例子**:承诺**

**让我们再回到我们的日常生活，打个比方。我又开始喝咖啡了，但这一次，我是在咖啡店里喝的。我向柜台的人点了一杯“拿铁玛奇朵”。这个人给了我一张有数字的票，作为承诺，在未来，我会得到咖啡。**

**拿到咖啡就有“喝一杯拿铁玛奇朵”的意向。这是一个推迟的行动，将在未来发生。**

**在我之前有几个订单，所以我将不得不等到它准备好。后来，在某个时候，服务员会把它带到我的桌子上。在此期间，我可以做其他事情，所以我决定继续我的中等文章。**

**“要咖啡”、“买票”和“写文章”发生在节目的第一部分。**

**以后咖啡好了，服务员给我端来拿铁玛奇朵。但是，我仍然需要写完我的最后一段，然后我才能继续喝我的咖啡。**

**“喝拿铁玛奇朵”的行为发生在节目的第二部分，只是在我准备好咖啡的时候。**

**让我们把它写成代码:**

## **逐步时间表**

**为了简单起见，让我们假设每条指令需要 1 毫秒来执行，这样我们就可以看到它们是如何一毫秒一毫秒地发生的。**

**跟随幻灯片，将带你到执行的每一步。**

**承诺的异步执行**

# **结论**

**我们从基本概念、JavaScript 引擎组件和程序前半部分和后半部分的代码执行开始。**

**然后，我们用一个非常简单的例子讨论了`setTimeout` 通过把它放入时间线的工作方式。我们也经历了一个类似的使用承诺的例子。**

**尽管这些是棘手的概念，我希望我能够使它们简单易懂。掌握它们以避免在编写异步代码时出现问题是很重要的。**

**感谢阅读！**

# **资源**

1.  **Kyle Simpson [“重新思考异步 JavaScript”](https://frontendmasters.com/courses/rethinking-async-js/)(2016 年 3 月 29 日)。**
2.  **[Will Sentance](https://medium.com/u/c211a09475?source=post_page-----3808dff05ca4--------------------------------)《JavaScript:新的硬部件》(2018 . 7 . 10)。**
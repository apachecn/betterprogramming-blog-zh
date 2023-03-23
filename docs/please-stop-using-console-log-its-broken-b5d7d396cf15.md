# 请停止使用 console.log()进行调试，它已经坏了

> 原文：<https://betterprogramming.pub/please-stop-using-console-log-its-broken-b5d7d396cf15>

## 为什么坏了，怎么做更好

![](img/0dd2e4b1848302e0e11da200b3594bd7.png)

照片由 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/broken?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在我们的代码中添加`console.log()`可能是开发人员最常见的做法之一。然而，我在生活中花了很多时间来说服初学者(有时也是熟练的编码员)停止使用它来调试 JavaScript。原因如下。

首先，我必须承认我仍然在代码中使用`console.log()`语句——旧习难改。我不是一个人:[大约 75%的 Node.js 开发人员报告使用它](https://www.clarkio.com/2017/04/25/debugging-in-nodejs/)(2016 年)来发现他们应用程序中的错误。

在一些情况下，这要么是最简单的做法，因为您确切地知道记录信息的内容和位置，要么是唯一要做的事情，因为您处于受限制的生产/嵌入式环境中，没有其他工具。然而，这不是一个借口，使例外导致你的日常实践。事实上，一般来说，`console.log()`是痛苦的，容易出错——你将在下文中看到。还有更复杂的解决方案。

# 缺少上下文信息

`console.log()`强迫您在调试之前有意识地选择要记录的信息。你首先展示的东西是不充分的，甚至是完全不相关的，因为你通常还不知道发生了什么。

每次你启动你的应用程序，你都会更进一步——无论是意识到你仍然没有在正确的时间记录正确的信息，还是浪费时间一次又一次地改变你的陈述，以显示新的信息和隐藏无关的信息。

## **用调试工具反击**:

*   调试时显示/观察任何 JS 变量[(函数参数、局部变量、全局变量等)。)](https://developers.google.com/web/updates/2015/07/preview-javascript-values-inline-while-debugging)
*   探索[调用堆栈](https://developers.google.com/web/tools/chrome-devtools/javascript/reference#call-stack)以获得出现问题的完整上下文

## 信息太多

算法通常被设计成自动化大量的小任务——循环和递归是基本的构建模块。与`console.log()`一起，它导致大量的行显示在你面前，所以你可能很难找到正确的信息。

## **用调试工具反击**:

*   创建[条件断点](https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints#conditional-loc)以在满足特定条件时暂停执行，这样您就可以花时间分析发生了什么
*   观看[自定义 JS 表达式](https://developers.google.com/web/tools/chrome-devtools/javascript/reference#watch)(变量、条件等。)这样你就不会浪费时间在循环的每一步推导相同的表达式
*   除了您的标准应用程序日志之外，创建一个[调试日志分类](https://blog.risingstack.com/node-js-logging-tutorial/)，以便根据需要为感兴趣的域(如文件、服务、类等)激活调试消息。).

## 不可信的信息

你不能总是相信`console.log()`报告的信息，因为根本没有关于它的标准化行为。你真的不知道引擎盖下会发生什么。

大多数时候，在控制台还没有激活的时候调用`console.log()`只会导致对象[的引用被排队](https://stackoverflow.com/questions/23392111/console-log-async-or-sync)，而不是控制台将包含的输出。

作为一种变通方法，您需要克隆信息或序列化它的快照。[渲染与将来与记录的对象的交互(例如，在浏览器控制台中扩展对象属性)异步发生](https://stackoverflow.com/questions/23392111/console-log-async-or-sync)(被限制为限速更新)。

## **用调试工具反击**:

*   [异步堆栈跟踪](http://mrbool.com/how-to-debug-asynchronous-javascript-with-chrome-devtools/33573)(现在是 Chrome 的默认设置)允许你检查当前事件循环之外的函数调用，就像一次对异步回调发起者的上下文感知旅行

# 改变代码行为

调试异步代码的标准方法是控制台日志`1`、`2`、`3`、`4`等。(即，在您得到正确的顺序之前，在您期望的输出之前执行的所有步骤)。

结果，您修改了代码，从而修改了它的运行方式，这可能导致真正难以跟踪的、不稳定的行为。完成调试后，您还必须记得删除代码中所有零散的控制台日志。

## **用调试工具反击**:

*   当真正理解一个应用程序的流程时，一步一步地进行[是必须的](https://developers.google.com/web/tools/chrome-devtools/javascript/reference#stepping)
*   当真正理解异步回调的计时时，[断点](https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints)是你最好的朋友(选择最适合你的问题的类型)

# JavaScript 调试工具包

为了帮助您调试完整的 JS 应用程序，您实际上需要一些工具:

*   [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/) 现在支持 [Node.js 调试](https://medium.com/the-node-js-collection/debugging-node-js-with-google-chrome-4965b5f910f4)以及在本地或[远程浏览器](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/)中运行的 js 代码(例如，在移动设备上)
*   Node.js [调试](https://github.com/visionmedia/debug)模块

如果你认为在运行你的测试时不能使用调试器，考虑阅读[这篇文章](https://peterlyons.com/js-debug)和你在互联网上很容易找到的其他类似资源。

无论如何，可能没有一个放之四海而皆准的解决方案，因此，如果我没有说服您，并且您不想将调试器作为您的主要调试工具，我会建议您阅读[这篇文章](https://medium.com/@shanebdavis/debuggers-are-broken-become-a-console-log-samurai-f352accd9ef6)以使您的日志记录更加智能。人们也可以提到第三方模块，如用于 JavaScript 的 [winston](https://github.com/winstonjs/winston) 或 [loglevel](https://github.com/pimterry/loglevel) 是相当好的可定制日志程序，但在我看来，我更喜欢将它们用于生产级日志(例如，信息、警告、错误等)。).其他模块如 [debug](https://github.com/visionmedia/debug) 更适合调试/跟踪日志。
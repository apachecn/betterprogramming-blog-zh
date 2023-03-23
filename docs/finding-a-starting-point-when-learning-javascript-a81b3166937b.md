# 学习 JavaScript 如何找到起点

> 原文：<https://betterprogramming.pub/finding-a-starting-point-when-learning-javascript-a81b3166937b>

## 说服自己后退几步

![](img/2ad45fbab7c729b6a9e736156a027382.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Jesus Kiteque](https://unsplash.com/@jesuskiteque?utm_source=medium&utm_medium=referral) 拍摄的照片。

当我第一次开始进入前端开发时，我觉得到那时为止我所学的一切都毫无意义。我来自 Python 后端，在那里事情是有意义的。

我甚至接受了 Python 中的新框架，比如 Django 和 Flask。当然，我遇到了障碍和正常的挫折，但是通过使用 Python/Flask/Jinja2，我实现了最终目标。我不需要处理 JavaScript。一点也没有。

但是一旦你开始和其他人、公司、项目经理和成熟的软件一起工作，你会很快发现你必须适应。我艰难地认识到，如果你真的想达到你希望的理解水平，有时你必须从头开始。也许不是对所有语言都是如此，但对 JavaScript 来说绝对是这样。

JavaScript 的问题在于它有“包袱”。JavaScript 不仅仅是一种语言。JavaScript 是一个问题的答案。自从 JavaScript 出现以来，许多其他编码人员试图通过开发库、框架，甚至允许 JavaScript 在浏览器之外运行的完整运行时环境来简化事情。

这不是 JavaScript 的初学者指南。事实上，我将几乎完全避免使用这种语言。JavaScript 本身非常容易学习。任何已经掌握 OOP 或函数式编程的人应该会发现 ES6 是一种非常可靠的方法。

我想做的是强调贯穿始终的大主题，并整理出一个基本路线图，它将帮助你知道从哪里开始以及何时继续。

# **第 1 部分:普通 JavaScript ES5**

我看的第一个 JavaScript 教程叫做《绝对初学者的 JavaScript 之类的东西》。老师首先谈到的是 jQuery，以及事情过去是如何以不同的方式完成的，但是有了 jQuery，现在一切都变得容易多了。他实际上觉得这是 JavaScript 初学者的零起点。

ES5 和 ES6 经常被来回使用，因为它们都还在这个领域，你永远不知道你会遇到什么。在你开始学习 JavaScript 的时候，这两者通常是同时教授的。有时候，指导老师甚至会在整个教程中在两者之间来回跳转。这是另一个 JavaScript 初学者教程。

这两个教程都在偷工减料，以便专注于他们所教授的内容。有一些非常好的教程会列出一些你应该在开始之前就明确了解的东西。如果你对清单上的任何东西都不熟悉，那么你需要回溯并改变你当天的学习计划。

这就把我们带到了一个绝对的起点:在前进之前，你需要学习普通的 JavaScript ES5，并知道它如何与浏览器交互。学习 ES5 只是为了当你开始看到 ES6 是如何不同的时候，你能真正理解它为什么不同，而不是同时学习两件事情。

我强烈推荐托尼·阿里西亚的《JavaScript:理解奇怪的部分》。这将 JavaScript 撕成了碎片。他甚至在你输入一行代码之前就解释了它在做什么。这段视频长达三个多小时。没人说过这很容易。

# **第二部分:ES6**

在你对普通 JavaScript ES5 有了深刻的理解之后，是时候开始学习 ES6 的变化了。

Traversy Media 的这个 [React JS 速成班](https://www.youtube.com/watch?v=sBws8MSXN7A&list=PLFnNK1nWevjggqqgIA0oznAu8SmeLwtjS)从一个 ES6 主题列表开始，在继续学习之前，您需要了解这些主题:

*   班级
*   解构
*   高阶数组方法— forEach、map、filter
*   箭头功能
*   获取 API 和承诺

如果 React 是你的最终目标，那么在前进之前，你应该花尽可能多的时间学习这些科目。

# **第三部分:Node.js 和 NPM**

对于那些不知道的人，JavaScript 在浏览器中运行。每个浏览器都有自己的引擎:

*   铬合金— V8
*   边缘脉轮
*   火狐——蜘蛛猴

这意味着 JavaScript 不能在浏览器之外做任何事情——直到 Node.js 出现。Node.js 是一个允许 JavaScript 在浏览器之外运行的运行时环境。

它基于 Chrome 的 V8 引擎，但允许与电脑互动，这在以前是不可能的。伴随 Node.js 而来的是 NPM(节点包管理器)，它允许简单地安装模块以及他们想要的任何依赖。这绝对是你在处理框架之前需要知道的事情。

您可以使用 Node.js 和一个带有 Visual Studio 代码的`script.js`文件轻松地设置一个测试环境。所有的控制台读数将直接进入终端，就像使用 Python 时一样。Traversy Media 的这个 [Node.js 速成班](https://www.youtube.com/watch?v=fBNz5xF-Kx4)是一个完善这个工具集的完美地方。

# **第 4 部分:框架**

我开始写这篇文章是因为我的任务是学习 React。我从我能找到的最好的 React 教程开始。我很快发现，我不能只是粉饰一下这段代码，然后在周末把它搞清楚。这是我开始逆向工作的时候。一直到香草 ES5 的开头。

在我理解了 JavaScript 作为一种语言，ES6 带来的变化及其重要性，以及 Node.js 和 NPM 是如何工作的之后，我就可以充满信心和动力地回到我的 React 教程了。

在讨论 JavaScript 以及可以使用的许多库、框架和工具时，会涉及到许多其他的活动部分。我不想给人留下没有更多的东西要学的印象。因为有。我只是想让你明白这些其他的事情是什么，这样你在处理它们的时候就会舒服、自信、有动力。

非常感谢你的阅读。我希望我能以任何方式帮助别人。
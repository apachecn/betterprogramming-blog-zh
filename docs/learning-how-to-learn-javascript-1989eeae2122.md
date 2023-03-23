# 学习如何学习 JavaScript

> 原文：<https://betterprogramming.pub/learning-how-to-learn-javascript-1989eeae2122>

## 学习 JavaScript 时应该如何安排时间的 5 条建议

![](img/8318f9680afd95007f3f70e340e8841c.png)

照片由[阿尔方斯·莫拉莱斯](https://unsplash.com/@alfonsmc10?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在现代 JavaScript 的庞大生态系统中导航是一项艰巨的任务。有大量的[前端框架](https://raygun.com/blog/popular-javascript-frameworks/)，少量的[模块捆绑器](https://medium.com/@ajmeyghani/javascript-bundlers-a-comparison-e63f01f2a364)，以及数以千计的[实用程序库](https://blog.bitsrc.io/11-javascript-utility-libraries-you-should-know-in-2018-3646fb31ade)——更不用说所有可以在你的机器或服务器上运行的 [Node.js 模块](https://www.codementor.io/ashish1dev/list-of-useful-nodejs-modules-du107mcv3)。你怎么知道从哪里开始？你应该花时间在哪些主题上？

我学到的很多东西都没有用到，还有一些东西我希望我花了更多的时间在上面。根据我 7 年职业生涯中学到的经验，以下是我对你应该如何学习 JavaScript 的建议。

# 1.熟悉异步 JavaScript。

如果你看看 JavaScript 的历史(或者一般的 web 开发)，你会发现异步 JavaScript 完全改变了游戏。它允许网站从只有客户端动作的静态页面发展到浏览器中成熟的应用程序。毫不夸张地说，发出 HTTP 请求并等待响应而无需重新加载页面的能力改变了万维网。

可以肯定地说，异步编程是 web 开发的核心原则。这是您应该在学习 JavaScript 的早期投入大量时间的地方，因为它封装了其他核心原则，如回调、承诺、异步/等待和获取。

花些时间阅读我下面列出的资源，按顺序排列。它们应该给你一个关于从哪里开始异步编程的好主意。

**推荐资源:**

*   来自 Okta 开发者博客的异步 JavaScript 的历史(和未来)
*   [第 2 章:凯尔·辛普森的异步&性能手册中的回调](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch2.md)
*   第三章:凯尔·辛普森的异步&表演书中的承诺
*   [使用来自 Mozilla 开发网络(MDN)的 Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)
*   来自 scotch.io 的[异步/等待教程](https://scotch.io/tutorials/asynchronous-javascript-using-async-await)

# 2.学习 TypeScript 的基础知识。

我和自己争论了大概一个小时是否要包括这个，因为这是一个关于学习 JavaScript 的帖子，而 TypeScript 不是 JavaScript；它是 it 的超集，需要一个构建步骤才能使用。然而，我非常相信 TypeScript，所以我不得不包括它。

这个建议与 JavaScript 关系不大，而是与最佳编码实践关系更大。JavaScript 是一种松散类型的动态编程语言。当您传递没有类型契约和不可变性的变量和数据对象时，编写易受副作用影响的代码太容易了。 [TypeScript](https://www.typescriptlang.org/) 通过添加[强类型](https://www.typescriptlang.org/docs/handbook/basic-types.html)和创建[只读属性](https://mariusschulz.com/blog/typescript-2-0-read-only-properties)的能力缓解了这些问题(还有许多其他美妙的事情)。

有鉴于此，我建议您尽快学习 TypeScript 的基础知识。我所说的“基础”是指:如何给变量、类属性、函数参数和函数返回值添加类型注释。TypeScript 非常强大，除了简单的类型注释之外，还有很多东西需要学习，但是这第一步将防止您经常搬起石头砸自己的脚。我仍然对 TypeScript 编译器捕捉到我最初没有发现的错误的频率感到惊讶。

是的，编译器会对你大吼大叫。没关系！它是你的朋友，它希望你成为一名更好的程序员。花点时间考虑一下 TypeScript 给你的错误消息，我保证你的代码会有很大的改进。

**推荐资源**

*   [从正式打字稿文档开始 5 分钟](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)内打字稿
*   在[在线打字稿 REPL](https://repl.it/languages/typescript) 里到处玩
*   Mike North 撰写的 4 小时研讨会

# 3.理解 JavaScript 框架存在的原因。

早在 2012 年末我第一次涉足 web 开发时，JavaScript 框架就已经存在了。那时候， [AngularJS](https://angularjs.org/) 独占鳌头， [Backbone.js](https://backbonejs.org/) 拥有可观的市场份额，而 [Ember](https://emberjs.com/) 则是炙手可热的后起之秀(双关语)。有趣的是，即使它们现在已经成为过去(大部分被 [React](https://reactjs.org/) 和 [Vue](https://vuejs.org/) 所取代)，这些前端框架解决的问题并没有太大的变化。例如:

*   **将视图绑定到控制器的数据**。这是最重要的一条。保持 UI 视图与前端应用程序的状态同步是所有前端框架的主要原因。无论框架是完全的 MVC、MVVM，还是仅仅是一个视图层，它们都有办法将一些状态绑定到一个视图，并使它们保持同步。
*   **可重用组件。这是 AngularJS 做对的最重要的事情。基于组件的前端视图组合，以及 JS 将模板编译成 HTML，是 React 和 Vue 等现代框架的核心，但自 AngularJS 以来就已经存在了。如果不使用现有的框架或创建自己的框架，创建动态的、可重用的组件是根本不可能的，因为 [web 组件的 API](https://developer.mozilla.org/en-US/docs/Web/Web_Components) 非常有限，并且不处理数据绑定。**

这些只是我想到的几个例子。关键是，如果你看看那些花哨的扳手、螺丝刀和其他工具后面，螺母和螺栓是一样的。工具在变化，技术在发展，但是如果您理解了 web 开发的核心难点(这些工具最初存在的原因)，您将能够更好地理解和正确使用任何现在、未来或遗留的框架。

**推荐资源**

*   现代 JavaScript 框架存在的最深层原因

# 4.同时学习两个相似的框架。

借用我关于框架解决的底层问题没有改变的观点，我现在建议你同时学习两个前端框架。比如 Vue 和 React。

当大量使用一个框架时，很容易开始觉得该框架的语法和模式是底层语言(在我们的例子中是 JavaScript)的一部分。俗话说，如果你只有一把锤子，所有的东西看起来都像钉子。这句话，翻译成编程术语，意思是**如果你只使用一个框架，你将固有地开始根据该框架考虑你的解决方案，它将成为你的拐杖而不是你的力量。**

同时学习或了解两个框架可以改变这种想法，让你更深入地了解框架实际上为你做了什么。这就像学习一门外语能让你更好地掌握你的母语一样。你看到了两者之间的相似和不同，这让你思考是什么语言结构导致了这些相似和不同。了解两个 JavaScript 框架可以提供类似的视角。通过了解通向同一解决方案的两条路径，您可以更好地理解它们所解决的根本问题。

# 5.揭开构建过程的神秘面纱。

我没有忘记，我现在已经提出了 4 条建议，其中只有一条涉及到学习普通 JS。**不管你喜不喜欢，编写现代 JavaScript 涉及许多工具和框架，将它们联系在一起的是构建过程**。

构建工具是在我的职业生涯中变化最大的领域，因为这是性能和资产规模最大的地方。基于网络的公司和开源社区不断试图从构建过程中挤出每一个小的改进，这导致了一系列不断变化的工具和过程。

我不得不承认，我花了一段时间才理解像 [Webpack](https://webpack.js.org/) 这样的工具是如何工作的。您设置了一个简单的配置，运行一个命令，突然您就有了一个包含优化的、最小化的和浏览器兼容的代码的文件。构建工具看起来像是魔法，因为它们都力求零配置或极少配置。这对于开始使用它们来说是非常好的，但是当您实际上必须进行一些定制配置时，这使得它们令人望而生畏。

理解这些工具为您做了什么非常重要。试着理解你的代码正在经历什么样的转换，以什么样的顺序。试着理解你的模块是如何被捆绑的，这样你就可以更好地优化它们。尝试了解每个配置选项，因为这对性能和文件大小影响最大。

**推荐资源**

*   [**从无到有**。](https://what-problem-does-it-solve.com/webpack/intro.html)强烈推荐！对 webpack 所解决问题的深入探究
*   [Grunt 和 Gulp.js:简化前端开发的任务运行工具](https://www.upwork.com/hiring/development/grunt-and-gulp-js-task-runner-tools-to-streamline-your-front-end-development/)

就是这样！我希望这篇文章能带来可观的价值。正如我在介绍中所说的，JavaScript 生态系统令人生畏。我从事专业工作已经 7 年了，但我仍然经常觉得自己只是触及了皮毛。所以，不要试图一下子全明白——集中精力做好这五件事，你就会走得很好。
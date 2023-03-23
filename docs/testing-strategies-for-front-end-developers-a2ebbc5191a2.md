# 前端开发人员的测试策略

> 原文：<https://betterprogramming.pub/testing-strategies-for-front-end-developers-a2ebbc5191a2>

## 固化您的前端代码

![](img/1a568d194e58452763ee3cbe59c2b8e7.png)

[自由股票](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

作为前端开发人员，有这么多不同的方法来测试我们的工作。手动测试和自动化测试是我们工具箱中的必需品。

但是什么时候应该使用手动测试，什么时候应该选择自动化测试方法呢？在这篇文章中，我想深入自动化测试方法，看看我们什么时候应该选择 BDD，DDD，或者 TDD。

# 测试方法

1.  BDD:帮助理解问题是什么，以及我们的项目将为我们的用户带来什么解决方案。
2.  DDD:帮助将一个项目分解成更小的部分，并提出问题的解决方案。
3.  TDD:避免错误或者防止你的应用程序崩溃。

## BDD/T *在*思考外部

行为驱动的开发过程帮助我们理解问题。在这个过程中，应该会有很多开放性的问题得到解答。这是项目的发现阶段。

在这个过程的最后，应该理解为什么这个特定的项目将解决问题，以及它将为最终用户带来什么价值。

## DDD/思考大局

既然我们对问题有了清楚的了解，我们必须把解决方案分解成更小的部分。将项目分解成更小的领域是一个好主意。在这些领域中，一个团队或团队的一部分将与领域专家一起工作，以端到端地解决该领域的问题。

领域专家是对用户在该领域中遇到的问题有专业知识的人。这将有助于团队构建可靠的解决方案，让最终用户更加满意。

## TDD/从里到外思考

既然我们对问题有了清晰的理解，并将解决方案分解成更小的领域，并围绕这些领域创建了团队，我们就可以开始考虑代码了。

对于测试驱动开发，我们首先编写一个测试，然后看着它失败。测试必须根据团队与领域专家一起提出的解决方案来编写。

为了通过测试，我们应该编写最少的代码。之后，通常会在不改变代码行为的情况下重构代码，但是通过这一步，我们可以确保代码符合可接受的标准。

# 如何测试

我们可以用两种方式测试我们的软件:手动(手动完成所有事情)或自动(编写测试脚本，可以在 CI/CD 过程中自动启动)。

为了理解我们将如何以及何时测试我们的前端应用程序，我们需要理解一些自动化测试方法之间的差异。

# 自动化测试的方法

*   单元测试:孤立地测试代码(没有依赖)。
*   集成测试:测试代码中几个不同部分之间的通信。
*   端到端测试:测试应用程序前端和后端之间的通信。

## 单元测试

单元测试是一种测试形式，在这种测试中，您测试一段代码，并孤立地测试它，以检查一切是否按预期工作。

您可以测试某些属性是否可用，或者测试在调用方法后某个属性是否被更改。

关注代码的逻辑是一个好主意。如果你输入一个字符串、数字或对象，函数会返回一个特定的值吗？所有这些测试将确保我们的代码按照我们的预期运行。

尽管我需要调整一些逻辑，但它总是能帮助我确保一切按预期运行。除此之外，这也将帮助你保持你的代码(函数和类)小。否则，您的测试也会变得庞大，这会破坏可维护性。

## 集成测试

尽管您在单独的单元测试中进行测试，但是在集成测试中，我们测试应用程序的不同组件之间的通信。如果一个控制器必须向另一个控制器传递一些数据结构，我们必须检查是否有一些数据模型进入，以及另一个控制器的输出是否如我们所预期的那样。

覆盖这些数据结构的一些变体是一个好主意，以测试是否出错，以及这是否是预期的。

## 端到端测试

使用集成测试，您不需要测试后端如何与前端交互。端到端测试适用于这样的场景，当前端发出请求时，我们需要测试后端是否给出预期的响应。

大多数情况下，这不会发生在生产服务器上，但更有可能发生在临时服务器上。

# 我们可以使用哪些 JavaScript 测试库？

为了测试我们的 JavaScript，我们可以使用许多库来编写测试。这里有几个我喜欢的。

# 单元和集成测试框架

## 玩笑

Jest 由脸书创建，专注于简单。您可以将它与 Node.js、TypeScript、Angular、React、VueJS 或普通 JavaScript 结合使用。

您可以使用 Jest 对前端和后端的 Node.js 进行单元测试和集成测试。

## 茉莉

[Jasmine](https://jasmine.github.io/) 是一个行为驱动的 JavaScript 框架，用于测试 JavaScript 代码。在许多棱角分明的项目中，你会发现茉莉也在其中。

使用 Jasmine，您可以测试 JavaScript 代码以及 DOM。加载一段 HTML 并对其进行测试很容易。

Jasmine 可以用于前端的单元和集成测试，后端的 Node.js。

## 其他的

*   [裸考](https://volument.com/baretest)
*   [柴](https://www.chaijs.com/)
*   [摩卡](https://mochajs.org/)

# 端到端测试框架

对于端到端测试，有几种不同的工具可用。那些测试框架使用(无头的)浏览器来运行他们的测试，并且有一个可用的 DOM。

## WebdriverIO

有了 [WebdriverIO](https://webdriver.io/) ，你可以从前端到后端测试你的应用。在 CI/CD 流程中完全自动化地测试这些东西是一个好主意。

WebdriverIO 的好处是，如果你想访问 DOM，你有一个类似 jQuery 的语法，这使得代码非常可读(例如`$('h2.sub')`)。

## 柏树

Cypress 提供了一整套测试套件，用于在浏览器中测试你的网站或网络应用。

你可以用 JavaScript 写测试，录视频，截图，存储在云端。

使用 Cypress，您可以得到一个很棒的仪表板，在那里您可以看到通过和失败的测试，这非常有用。

## 其他的

*   [硒](https://www.selenium.dev/)
*   [量角器](https://www.protractortest.org/)
*   [黄瓜](https://cucumber.io/)
*   [Nightwatch.js](https://nightwatchjs.org/)
*   [木偶师](https://developers.google.com/web/tools/puppeteer)
*   [剧作家](https://www.npmjs.com/package/playwright)

# 其他测试工具

但是还有很多工具可以测试您的 JavaScript 代码或前端应用程序:

*   [沙袋鼠](https://wallabyjs.com/)
*   [Sinon.js](https://sinonjs.org/)
*   [testDouble.js](https://github.com/testdouble/testdouble.js/)
*   [JSDom](https://github.com/jsdom/jsdom)
*   [因果报应](https://karma-runner.github.io/latest/index.html)
*   [伊斯坦布尔](https://istanbul.js.org/)
*   [AVA](https://github.com/avajs/ava)

# 结论

我希望我澄清了什么时候使用单元测试、集成测试或者端到端测试。概括一下:

*   当您需要在隔离环境中测试逻辑时，请使用单元测试。嘲笑我们的依赖是很重要的。
*   当您想要测试服务、控制器、组件等之间的逻辑时，请使用集成测试。测试进入其中一个的输入，并检查另一个的输出。这是你如何确保他们一起工作。
*   当您需要测试前端和后端如何协同工作时，请使用端到端测试。通过这些测试，您可以测试应用程序的整个堆栈。

如果你对这些测试有任何问题，请在评论中添加，我会尽我所能帮助你。

[![](img/0c6e6e4c319b5a5aa36c79ab75920cb8.png)](https://mailchi.mp/239d4f7b0d9d/programming-content-creator)

注册我的[免费电子邮件课程](https://mailchi.mp/239d4f7b0d9d/programming-content-creator)！

# 从我这里读更多

[](https://levelup.gitconnected.com/typescript-for-beginners-97b568d3e110) [## 初学者打字稿

### 从头开始学习 TypeScript 的实用方法

levelup.gitconnected.com](https://levelup.gitconnected.com/typescript-for-beginners-97b568d3e110) [](https://medium.com/better-programming/we-are-not-serious-enough-about-software-development-b9e3222f2906) [## 我们对软件开发不够认真

### 为什么我们不像对待现实世界的基础设施那样对待软件呢？

medium.com](https://medium.com/better-programming/we-are-not-serious-enough-about-software-development-b9e3222f2906) [](https://medium.com/better-programming/build-fast-json-powered-forms-on-angular-with-ngx-formly-b7a00733e66e) [## 使用 NGX Formly 在 Angular 上构建快速的、基于 JSON 的表单

### 表格可能是一场噩梦——让我们把它们变得更好

medium.com](https://medium.com/better-programming/build-fast-json-powered-forms-on-angular-with-ngx-formly-b7a00733e66e) [](https://medium.com/better-programming/how-to-build-fast-advanced-json-powered-forms-on-angular-with-ngx-formly-77aeed406f73) [## 如何使用 ngx-formly 在 Angular 上构建快速、高级的 JSON 表单

### 验证、可重复部分、条件字段，以及将表单提交给 API

medium.com](https://medium.com/better-programming/how-to-build-fast-advanced-json-powered-forms-on-angular-with-ngx-formly-77aeed406f73) [](https://medium.com/better-marketing/5-lessons-learned-from-writing-online-development-tutorials-for-8-years-ebc10c08297b) [## 8 年写编程教程的 5 个教训

### 冲破所有的界限，尽管这很难

medium.com](https://medium.com/better-marketing/5-lessons-learned-from-writing-online-development-tutorials-for-8-years-ebc10c08297b)
# 为什么我离开 Angular 去 Vue.js

> 原文：<https://betterprogramming.pub/why-i-left-angular-behind-for-vue-js-f8b7da829c7b>

## 开发者体验

## 我不后悔，但是…

![](img/ef15bf1461e1d611d78c229da6bb2a8c.png)

伊恩·杜利在 [Unsplash](https://unsplash.com/s/photos/balloons?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我已经用 Angular 开发了两年多了。我在版本 6 中发现了这个 JavaScript 框架。我享受了这么多月，季度，年。但是当早期版本 9 和 Ivy renderer 引擎发布时，我放弃了它。

从 Angular 转到 Vue.js 一直是一个愉快的时刻，是我前端开发者生活中真正的享受。这篇文章的目的是向你解释为什么。

最开始，我是一个后端开发人员，为几家公司开发 REST API web 服务。我喜欢这种结构良好的代码，有意义的指令和强大的业务逻辑。目的是真实的:从数据库获取数据，转换数据，并以 web 服务的形式提供复杂的对象，以便前端开发人员可以使用它们。

# JS 框架的兴起

2015 年前后，最流行的前端框架增长越来越快。Angular、React 和 Vue.js 获得了越来越多的支持者。他们在单页应用程序(SPA)方面肯定有不同的方法。

Angular **这一堆工具可以帮助你创建一个应用程序，而不用考虑你可以使用哪个库来处理 HTTP 请求、单元测试、i18n 等等。**

Angular 的直接并发 React 在他们发布 Redux(他们的通量模式实现)时改变了游戏。React 的主旨是通过一组专用的状态来呈现组件。React 是以用户界面为中心的。添加钩子改变了开发 web 应用程序的方式，并利用了组件组合。

Vue.js 是微小的局外人。尽管使用 Vue.js 的人比 Angular 或 React 少，但 Vue.js 可以自豪地说，它得到了一个发展非常迅速的强大社区的支持。从技术上来说，它几乎是 Angular 和 React 的混合，使用反应状态来渲染组件。

## 心灵觉醒

此时，我是一名后端开发人员，构建 web APIs 来获取地理数据(例如，聚类计算、兴趣点)，当我看到前端开发人员的代码时，我完全迷失了。我对 AngularJS 有一些了解(来自工程学校)，但那是完全不同的。我因为几个原因开始感到无聊，是时候对我的工作生活做出强烈的改变了。

我下定决心换个岗位，开始前端生涯。我的决定有几点:

*   我意识到我喜欢视觉内容，喜欢看到我的代码呈现一些东西。所有这些 JSON 束是不够的。
*   我开始厌倦了去理解所有的商业逻辑。作为后端开发人员，您首先需要了解上下文、所有概念和涉及的实体。相比之下，前端开发的目标是渲染数据。有时，必须实现业务规则，但是它们不像背景层中的规则那样复杂。
*   web 开发世界发展非常迅速，在处理呈现内容、学习新的浏览器功能或新的 JS 框架模式时，可能性几乎是无限的。我们的知识总是需要更新的。
*   因为这是一项以用户为中心的工作，所以你要兼顾用户界面和用户体验。你可以把自己想象成用户，浏览网站，检查每一个可能打扰你的小细节。
*   一个前端开发者可以为自己开发的东西感到骄傲:他们的 web 应用是具体的(嗯，只是像素，但你懂我的意思)。但是后端开发人员不能有同样的感觉，当 REST APIs 关闭时，前端对他们发火，他们甚至会感到沮丧。
*   最后，由于 JS 框架的兴起，我认为我遗漏了一些重要的东西。

# 棱角分明，我选择你！

我选择了学 Angular。当然，我已经有了一些关于它的知识，而且，因为它是为企业级使用而设计的，所以它是被雇佣来实践这个框架的最简单的方法。

通过在 Udemy 平台上的一些课程和阅读大量的媒体文章，我学到了很多。

## 到处都是打字稿

当我开始用第六版练习 Angular 时，TypeScript 是必修的，我爱上了它。你可能会说这很简单，因为它围绕 OOP 的假设接近后端编程语言。拥有这种定义良好的上下文就像是学习前端开发所有要素的软化剂。

## 类组件样式和装饰器

声明组件的类组件风格吸引了我，因为它看起来像 OOP 类。装饰器如`Input` / `Output`或`Service` / `Component`、`Module`类似于 C #数据标注。

## 应用架构

在 Angular 应用程序中，默认情况下，代码被整齐地分开:

*   数据—所有与数据相关的内容都包含在服务和应用状态存储中(及其功能模块、`reducers`、`selectors`、…)。
*   渲染工具—过滤器和指令易于使用和测试。
*   使用原子设计模式或以功能为中心的设计模式很容易设置组件层次结构。
*   模块组合——Angular 模块是一致的容器，其中的代码块专用于一个应用程序域、一个工作流或一组密切相关的功能。
*   每个组件的专用文件:三个文件(甚至四个，如果我们包括测试文件)有助于可读性和关注点的分离。

我想你在这里已经明白了:结构，结构，再结构——这与后端环境非常接近。

## 强大的 UI 库(Angular Material)

[有角材质](https://material.angular.io/)是材质设计的有角实现。这些组件和实用工具给我留下了深刻的印象，并帮助我提高了很多 Angular 方面的技能，特别是关于内容投影、依赖注入或组件结构的高级主题。

## 易测性

[可测性](https://angular.io/guide/testing)是 Angular 固有的，有 [Karma](https://karma-runner.github.io/latest/index.html) ，它的测试 runner，还有 [Jasmine](https://jasmine.github.io/) ，测试库。就好像你开发的每一个应用组件都已经可以测试了。一组重要的函数可用于获取组件并操纵它来处理几乎所有的用例。

此外，由于 Angular 开发了自己的库:[量角器](https://www.protractortest.org/#/api-overview)，创建端到端(E2E)测试变得更加容易。

# 蜜月已经过去了

在使用框架数月甚至数年之后，您已经处理了许多主题和问题。你知道它的优点和缺点。

## 学习曲线

Angular 的学习曲线非常陡峭，即使经过多年的练习。深刻的主题由一个热情的专家组成的小团体很好地理解和管理。

在所有 JS 框架中，它是最陡峭的一个。这来自于 Google-leg 框架的特定运行时、依赖注入和应用程序生命周期。

## 过度键入的语言意味着复杂性

事实上，这个框架是用 TypeScript 编写的，这是一个用一个结构良好的大纲来控制所有代码的真正机会。

然而，对于特定的主题，它可能会导致大量的代码和输入来完成简单的任务。

例如，当测试一个组件时，你需要所有这些设置(代码提取自[角度文档](https://angular.io/guide/testing#component-dom-testing)):

这一点的另一个例子是 [CDK 门户网站的功能](https://material.angular.io/cdk/portal/overview)。

最后，还有 [Angular 的内容投影能力](https://blog.angular-university.io/angular-ng-template-ng-container-ngtemplateoutlet/)。

## 主要版本，但有微小的变化

Angular 对其发布有一个精确的时间表。

我从版本 6 开始使用这个框架，直到版本 9。在此期间，没有添加主要的新功能或特定的范例来改善开发人员的编码体验。

*   v7: CDK 改进(虚拟滚动、拖放等。)
*   v8:角常春藤作为选择加入，差异加载
*   v9: Ivy 正式发布

你可以在这里找到更多细节。

在这期间，React 提供了它的钩子。Vue 增加了新的属性或改进了 TypeScript 兼容性。

在我写这篇文章的时候，Angular v10 刚刚发布，正如我所说的，还没有开发出任何新的东西。

# Vue.js:前端开发变得简单

2019 年夏天，我在 Udemy 上关注了一堂关于 Vue 2 的课。我一个朋友用的特别开心，我也想有自己的看法。它的简单性和启动项目的容易程度给我留下了深刻的印象。

## 回归基础

关于 Vue 框架，我真正喜欢的是我们可以创建的每个组件如何接近模板，以及它的选项是以模板为中心的:

*   `computed`可作为准备好的数据，以便在模板中轻松呈现
*   `watchers`允许对模板中的数据变化做出反应
*   [与](https://vuejs.org/v2/guide/components.html#Dynamic-Components)[角度设置](https://angular.io/guide/dynamic-component-loader)相比，动态组件非常容易操作
*   `transitions`还有动画、动态风格等。

## 表演的

尤雨溪的框架非常快速和轻量级，构建时间非常短，即使在较大的应用程序中也是如此。构建过程清晰，可以轻松定制(即使 Angular 已经推广了 [CLI Builders](https://angular.io/guide/cli-builder) )。

例如，`computed`是存储在内存中的方法，当它们的依赖关系改变时，它们会自我更新。相比之下，`methods`每次都被重新执行。

## 学习曲线

Angular 的学习曲线可能是最陡的，但 Vue 应该是最软的。文档实用且易于理解。开始一个项目非常快。

## 编码经验

拥有与组件相关的所有代码非常方便——不需要在模板、样式和组件文件之间切换。

组件的创建、导入和使用是清晰而简单的。[懒装组件](https://vueschool.io/articles/vuejs-tutorials/lazy-loading-individual-vue-components-and-prefetching/)也这么容易。

此外，我们可以向 Vue 实例添加插件，并且可以从任何地方调用它们:

```
Some examples:this.$store
this.$emit
this.$i18n (when you use [vue-i18n](https://kazupon.github.io/vue-i18n/))
...
```

当然，我们可以[延长它](https://vuejs.org/v2/cookbook/adding-instance-properties.html)。

## 类型脚本支持

与 Angular 不同，TypeScript 在 Vue 应用中不是强制性的。这个 JavaScript 超集是一个补充工具。当出现 TS 编译错误时，它不会中断应用程序。你可以用它来组织你的代码。在单个项目中，纯 JS 组件和 TypeScript 组件可以共存。而且这个超级强！

# 我不后悔，但是…

现在，我想蜜月期还没有结束，因为我已经使用 Vue 快一年了。但我很遗憾我在 Vue 中没有发现 Angular 可以夸耀的几件事:

*   创建一个可以在模板的几个地方重用的模板(比如`ngTemplate`)是很强大的，但是我没有找到在 Vue 中复制它的方法。
*   模板类型检查是 Angular v9 的一个强大资产。
*   应用架构在 Angular 中比在 Vue 中定义得更好。例如，有一个专用于数据(服务)的文件、一个模块定义文件等等。

可以肯定的是，我不会再回到 Angular 了。我真的很喜欢使用 Vue。此外，Vue 3 承诺了令人难以置信的新特性，比如原生类型脚本支持和一个新范例:Vue composition API。

尤雨溪 Vue 3.0 的设计原则— [VueConf Toronto](https://www.youtube.com/channel/UCSrbuSqU7S-g9vydCLRYCZg)

# 最后的想法

Angular，React，Vue 都是不同的 JS 框架，有很强的方法。它们提供了大量的功能，运行应用程序也很快。

无论如何，我都会继续关注 Angular，因为我相信它的潜力。我对 Vue 3 感到非常兴奋，迫不及待地想看到它的实际应用。即使[组合 API](https://composition-api.vuejs.org/) 非常接近 React，我也想发现它的所有资产。

# 资源

[](https://www.udemy.com/courses/search/?q=angular) [## 在线课程-随时随地| Udemy

### Udemy 是全球最大的在线课程目的地。发现关于 Udemy.com 的在线课程并开始学习…

www.udemy.com](https://www.udemy.com/courses/search/?q=angular) [](https://blog.angular.io/version-10-of-angular-now-available-78960babd41) [## Angular 的第 10 版现已推出

### 10.0.0 版本来了！这是一个跨越整个平台的主要版本，包括框架、角度…

blog.angular.io](https://blog.angular.io/version-10-of-angular-now-available-78960babd41) [](https://vuejs.org/v2/guide/) [## 简介- Vue.js

### vue . js——渐进式 JavaScript 框架

vuejs.org](https://vuejs.org/v2/guide/) [](https://www.youtube.com/channel/UCSrbuSqU7S-g9vydCLRYCZg/videos) [## 多伦多 VueConf

### 来自多伦多 VueConf 的会议和闪电谈话视频

www.youtube.com](https://www.youtube.com/channel/UCSrbuSqU7S-g9vydCLRYCZg/videos)
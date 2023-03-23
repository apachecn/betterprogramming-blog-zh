# Auralia JS vs. Angular

> 原文：<https://betterprogramming.pub/auralia-js-vs-angular-ccaf61e4d27d>

## 两个亲戚之间的争斗

![](img/2789a9aabb826e6b57493775acc7ef4b.png)

锡德·巴拉钱德朗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

[Aurelia](https://aurelia.io) 和 [Angular](https://angularjs.org) 是流行的 JavaScript 框架，被用于 web 应用程序开发。在目前可用的众多 JavaScript (JS)框架中，Aurelia 是一个相对较新的 JS 前端开发框架。通过它们的现代特性，如 HTML、路由、单页应用程序(SPA)和用户界面(UI)的组合，这两个框架都为开发复杂的 web 应用程序提供了可行性。在这篇文章中，我将讨论什么是 Aurelia，什么使 Aurelia 特别，我将比较 Aurelia 和 Angular。

众所周知，大家都听说过很多关于 Angular 的事情，让我们来看看 Aurelia 吧。

# 奥雷利亚是什么？

Aurelia 是免费开源的，仍处于初始版本，最初由 Rob Eisenberg 和他的团队在麻省理工学院的许可下于 2015 年发布。Aurelia 已经被分解成一系列面向功能的模块，而不是一个僵化的框架。它通过其丰富的组件模型、动态 UI 设计、路由和一组广泛的插件，提供了全方位的功能和工具来构建任何前端体验。

# 奥雷利亚有什么特别的？

## 约定胜于配置

约定优于配置是软件框架使用的软件设计范例，旨在减少程序员必须做出的决策数量，而不牺牲固有的灵活性。在编写配置文件时，开发人员会发现 Aurelia 的行为密切相关；当期望的行为偏离约定时，开发人员只需要定义设置。

## 简单易学

Aurelia 基于标准的、不引人注目的风格使其成为唯一鼓励您使用普通 JavaScript 或 TypeScript 创建组件的框架。如果你有现代 JS 和 HTML 的基础知识，即使是创建复杂的应用程序，你也没有更多的需要学习。

## 简单测试

Aurelia 通过混合现代 JS 模块和不引人注目的方法，使单元测试像测试普通 JS 一样简单。用一个好的依赖注入容器和测试库来编写集成测试既快捷又方便。您将从高可维护性和长寿命的应用程序中获益。

## 反应结合

Aurelia 通过选择最有效的方式来监控模型中的每个属性，从而允许任何对象具有强大的反应性绑定。它通过使用自适应技术并以最佳效率无缝同步您的 UI 和状态来实现这一点。

## 展开性

Aurelia 提供了可扩展性，允许开发人员创建自定义元素，向现有元素添加自定义属性，控制模板的创建，自定义模板的语法，以及创建新类型的反应式绑定。

# Aurelia vs .角度比较

这一部分包含了本文的精髓。我们来看看 Aurelia 和 Angular 哪个好。

## 符合标准

Aurelia 完全符合已建立的网络标准以及目前正在发展的标准。在这种情况下，Angular 仍然落后，并且倾向于偏离标准。Aurelia 库主要由普通的 JS 和 TS 类组成。

Agular 的不遵从性给程序员带来的后果中最值得注意的一个方面是大小写的敏感性。Aurelia 不区分大小写，因为标准 HTML 不区分大小写。Angular 语法是区分大小写的，因此 web 浏览器不能使用 HTML 解析器。Aurelia 使得所有模板都可以方便地包装在一个标签中。这个功能很难在 Angular 中执行，可能根本不可行。

## 有约束力的

Aurelia 简单地支持单向和双向绑定，这在角度上很复杂，会导致开发人员的困惑。默认情况下，Aurelia 具有单向绑定。对于像将输入小部件绑定到特定视图模型这样的应用程序，双向绑定更合适。Aurelia 在类内绑定变量的语法也可以与 JS 相比。他们使用的绑定类型可以由开发人员在自我文档中定义。

Auralia 简单数据绑定:

Auralia 双向数据绑定:

## 语言支持

只允许开发者用 Angular 用 TypeScript 写 JS 代码。TypeScript 也可以在 Aurelia 中使用，但也可以在其他为开发人员提供更多灵活性的编程语言中使用，如 [ECMAScript](https://www.ecma-international.org/ecma-262/) (ES)及其所有变体。虽然开发人员可以使用 ES，因为 Aurelia 的大部分文档和资源都是 TypeScript 格式的，但是强烈建议他们使用 Typescript。

## 按指定路线发送

两个框架以相似的方式完成路由配置，尽管这种路由的概念有很大的不同。在 Aurelia 中，父路由在一个地方定义，并将配置带入子路由的子组件中。该策略完全封装了这些组件，涵盖了它们的内部复杂性。

在中央配置文件中，模块的路由应该以角度定义，即路径、可路由组件和子路由。这种方法使得 Angular 的路线描述更加具有挑战性。下面是一个使用 Auralia 的基本路由示例(来源: [Auralia 文档](https://aurelia.io/docs/routing/configuration#basic-configuration))。

## 互用性

Aurelia 与其他框架配合得很好，比 Angular 有更好的互操作性。

# 结论

在现代应用开发中，Angular 和 Aurelia 都是前端框架。它们都受到第三方工具的支持，并允许集成现有的应用程序。最佳选择取决于发展中的组织最重视的标准，包括社区支持、安全和保障。在 Angular 和 Aurelia 之间切换的一个重要考虑因素是使用框架和组件特性解决技术问题的便利性。

这两个框架之间最吸引人的区别是 Angular 拥有更大的受众和生态系统，这要归功于 Google 的支持。无论如何，Aurelia 比 Angular 更坚固，因此在一致性和可靠性非常重要的高端应用中，Aurelia 会更受青睐。在应用程序较小且请求率较高的情况下，Angular 可能更好。
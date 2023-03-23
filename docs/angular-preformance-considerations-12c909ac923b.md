# 角度性能—生产和运行时优化的 8 个考虑因素

> 原文：<https://betterprogramming.pub/angular-preformance-considerations-12c909ac923b>

![](img/fde3099401e42dc330c36acea3d83ffb.png)

[乔·塞拉斯](https://unsplash.com/@joaosilas?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/optimize-production?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

开发人员面临的最重要的问题之一是应用程序的大捆绑包和低性能，尤其是当应用程序变大时。

开发人员认为低性能与框架本身有关，但这是不正确的。这是由于缺乏他们必须知道的考虑因素的知识。

在这篇文章中，我试图整合我和我的团队在处理大量有角度的大规模应用时所面临的问题

以下是生产和运行时优化的八个注意事项:

# **1。使用** `**enableProdMode**`

在 dev 模式下，Angular 框架执行开箱即用的额外检查，用于调试目的。它还具有变化检测机制，因此，当启用生产模式时，我们不需要它。

# 2.超前编译

AOT 是一种非常强大的编译模式，Angular 团队采用它来提高运行时性能。它通过树抖动和在构建过程中执行计算来减少渲染应用程序所需的计算量。

另一种编译模式是 JIT (Just In Time ),它构建包的速度更快，而应用程序的渲染速度更慢，所以我强烈建议使用 AOT 来加速应用程序初始化的渲染过程。

# 3.网络工作者

Web 工作者就像线程。众所周知，JS 是单线程的。

Web workers 部分去除了 JS 是单线程的限制，因为它使 JS 在浏览器的线程中运行，而不会阻塞事件循环，也不会中断浏览器的主 UI 线程。

回到网络工作者的角度。角度变化检测每秒需要执行数百万次检查。

由于 Angular 的平台不可知论以及它与 DOM 架构的解耦，可以在 web worker 中运行我们的整个应用程序(包括变化检测),让主 UI 线程只负责渲染。

因此，更改检测机制将在浏览器的后台以单独的线程运行。

Angular 与 web workers 的参考资料:

[](https://github.com/angular/angular/tree/master/modules/playground/src/web_workers) [## 角度/角度

### 一个框架。移动和桌面。在 GitHub 上创建一个帐户，为 angle/angle 开发做贡献。

github.com](https://github.com/angular/angular/tree/master/modules/playground/src/web_workers) 

# 4.角度通用和服务器端渲染

任何单页面应用程序(不仅仅是 Angular)的一个大挑战是，在呈现所需的 JS 可用之前，整个应用程序无法呈现，这导致了两个问题:

1.  搜索引擎优化(SEO)在这些应用程序中无法正常工作，因为它们无法索引应用程序的动态内容。(模板绑定如`ngModel={content}`中的内容。)
2.  糟糕的用户体验，因为用户将有一个空白页，直到应用程序被渲染。

服务器端呈现(SSR)通过在服务器上预先呈现所请求的页面并在初始页面加载期间提供所呈现页面的标记来解决这个问题。

参考资料:

 [## 角度文档

### 编辑描述

angular.io](https://angular.io/guide/universal) 

# 5.指定变更检测机制

默认情况下，Angular 在整个组件树上执行变化检测，这导致复杂应用程序中的大量计算，尤其是在计算正在进行时。

## `ChangeDetectionStrategy.OnPush`

`OnPush`变更检测策略允许我们禁用组件树的子树的变更检测机制。

通过将任何组件的变化检测策略设置为值`ChangeDetectionStrategy.OnPush`，我们将使变化检测仅在组件接收到不同输入时执行。

Angular 将输入与之前的参考输入进行比较时，认为输入不同，参考检查的结果为`false`。它是使用[不可变数据结构](https://facebook.github.io/immutable-js/)执行的。

如果你来自一个[反应](https://reactjs.org/)的背景，那就像`[PureComponents](https://lucybain.com/blog/2018/react-js-pure-component/)`。或者，像用特定的标准实现`[shouldComponentUpdate()](https://developmentarc.gitbooks.io/react-indepth/content/life_cycle/update/using_should_component_update.html)`。

# 6.缓存查找和配置

observables 最强大的特性之一是可以缓存一个 HTTP 请求，正如我们所知，Angular 的默认 HTTP 请求不返回 observables。

如果您在应用程序中有大量的配置和查找，这将非常有效。

使用两种链接方法实现可观测量中的缓存:

*   `PublishReply()`
*   和`RefCoun()`

示例 *:*

注意:缓存生存期是应用程序的生命周期，因此，当您刷新应用程序时，它将发出一个新的 HTTP 请求，产生一个新的响应实例。

# 7.使用纯管道(缓存过滤值)

`pure`标志表示管道不依赖于任何全局状态，并且不会产生副作用。

这意味着当使用相同的输入进行调用时，管道将返回相同的输出。这样，Angular 可以缓存调用管道的所有输入参数的输出，并重用它们，而不必在每次评估时重新计算它们。

# 8.使用*ngFor 指令的 trackBy 选项

参考资料:

[](https://medium.com/better-programming/improving-angular-ngfor-performance-through-trackby-ae4cf943b878) [## 通过 trackBy 提高 Angular *ngFor 性能

### 了解和使用*ngFor

medium.com](https://medium.com/better-programming/improving-angular-ngfor-performance-through-trackby-ae4cf943b878)